---
title: "The Memory Game"
permalink: /projects/memory/
layout: single
mathjax: true
sidebar:
  nav: "projects"
toc: true
---

<h2 data-toc-skip>The Optimal Strategy for Memory Under Bounded Working Memory</h2>

## 1. The Game

Memory (also known as Concentration or Pairs) is a card game played with $n$ pairs of identical cards ($2n$ cards total), shuffled and placed face down on a table. Players alternate turns. On each turn, a player flips two cards face up. If the two cards match, the player takes the pair and plays again. If they do not match, both cards are flipped back face down and the turn passes to the opponent. The player with the most pairs at the end wins.

Despite being a children's game, Memory has a surprisingly rich strategic structure. Every card flip reveals information to *both* players, and the key decision is how to balance learning (flipping new cards) against exploiting what you already know (flipping a card whose match you remember). This raises a natural question: **does it matter who goes first?**

If you want to skip the theory and just play, there's an [interactive game](#9-play-against-the-bot) at the end where you can take on the optimal strategy yourself.

*A note on methodology: this is the first time I've produced a mathematical result with the help of an AI agent. The proofs, dynamic programming formulations, and simulations in this post were developed in collaboration with Claude Opus (Anthropic).*

## 2. What Zwick and Paterson Showed

In 1993, Uri Zwick and Michael Paterson published the definitive analysis of Memory under the assumption that both players have *perfect memory*: they remember the identity and position of every card ever flipped.

Their key insight was to characterise each game position by just two numbers:
- $n$: the number of pairs remaining on the board
- $k$: the number of cards both players have previously inspected (and remember perfectly)

At each turn, the player chooses one of three moves:

- **0-move (pass):** Flip two already-known, non-matching cards. The turn passes to the opponent.
- **1-move:** Flip one new card. If it matches a remembered card, take the pair and play again. If not, "waste" the second flip by re-flipping a card you already know.
- **2-move:** Flip one new card. If it matches a remembered card, take the pair. If not, flip a *second* new card. If the two new cards happen to match, take the pair. Otherwise, the turn passes.

Through backward induction on the state space $(n,k)$, they computed the exact optimal strategy. The results are surprising:

1. **Player 2 has a slight advantage** for even $n \ge 8$, of magnitude about $1/(4n)$ pairs.
2. **The key strategic weapon is the pass.** At high $k$, both players know where most pairs are but deliberately refrain from taking them, engaging in a delicate game of parity control.
3. **Stalemates occur** in a substantial fraction of games under optimal play, when both players pass indefinitely.

This is elegant, but it immediately raises the question: does any of this survive when players do not have perfect memory?

## 3. First Attempt: Stochastic Memory Decay

My first approach, inspired by [Samuel Kilian](https://samuelkilian.de/about.html), was to model forgetting as probabilistic decay: each turn, every remembered card has a probability $\delta$ of being forgotten. This is analytically convenient, the Zwick framework mostly carries over, and it lets you sweep continuously from perfect memory ($\delta=0$) to complete amnesia ($\delta=1$).

I ran large-scale Monte Carlo simulations under this model. The results were interesting: at low $\delta$, the Zwick Player 2 advantage survives. Around $\delta \approx 0.10\text{–}0.15$, there is a phase transition where the Zwick stalemate regime collapses and the game becomes more decisive. At high $\delta$, the game converges to a coin flip.

But the model bothered me. Probabilistic decay means you might remember a card you saw 20 turns ago while forgetting one you saw 2 turns ago. That does not match my experience of how memory works in this game. You either correctly remember where a card is or you do not. And when your brain is full and you see something new, something old gets pushed out. The bottleneck feels more like *capacity* than *reliability*.

## 4. From Miller's Law to a Tractable Model

This pointed me toward George Miller's famous 1956 paper on the "magical number seven, plus or minus two". Later work has revised the effective capacity downward, with Cowan (2001) arguing for something closer to 4 chunks under stricter definitions, and the exact number depends heavily on chunking and task design. For this project I use $M=7$ as a convenient human-scale benchmark, not as a literal psychological constant. The important point is not the precise number but the qualitative fact: working memory has a hard capacity limit, and it is much smaller than a typical Memory board.

Of course, real human memory is more complex than a fixed-size buffer. Decay, interference, attention, emotional salience, rehearsal: they all play a role. A fully realistic model would need to account for all of these, and would almost certainly be intractable.

So I opted for a deliberate simplification: a deterministic bounded-memory model that captures the capacity constraint while remaining analytically solvable. Each player has a working memory of capacity $M$ (number of card positions). When memory is full and a new card is observed, the **least recently used** entry is evicted.

This is not meant to be a faithful model of human cognition. It is the simplest model that has the property I care about: memory is bounded, and new information displaces old information.

The reason this simplification works so well turns out to be mathematical rather than psychological.

**Key property:** since both players see every flip and both use the same deterministic eviction rule, **both players have identical memory at all times**. The memory state is common knowledge. So the game remains one of perfect information, the same class of game Zwick analysed. All that changes is the state space.

Any stochastic model of forgetting (decay, random interference, attention fluctuation) would give the two players *different* memories, creating private information and turning the game into something closer to poker. That is a much harder problem. The deterministic LRU model sidesteps this and keeps backward induction available.

At the level of the full game, a state consists of:
- the unmatched cards remaining on the board,
- whose turn it is,
- the ordered shared LRU memory list.

The next section shows why, after one key structural theorem, this can be reduced to a much smaller state description.

## 5. Greedy Matching Is Optimal

Before computing the optimal strategy, I need to settle a foundational question: when you know where a matching pair is, should you always take it immediately? Or could it sometimes be better to leave the pair on the board to manipulate the timing of later turns, as Zwick's pass does under perfect recall?

### Theorem

*Under shared bounded memory with deterministic LRU eviction, suppose that at the start of a player's turn the shared memory contains both positions of some matching pair $P=\{\alpha,\beta\}$. Then there is an optimal move that flips $\alpha$ and $\beta$ immediately. Equivalently, any legal move that leaves $P$ on the board is weakly dominated by taking $P$ at once.*

This is a weak-dominance statement, not a strict one: if you know two pairs simultaneously, taking either one first may give the same value. What cannot happen is that deferring a publicly known pair is *strictly better* than taking it immediately.

### Proof

I work at the level of the full game state: the set of unmatched cards on the board, whose turn it is, and the ordered shared memory list $L$ from least recently used to most recently used. Assume it is Player A's turn, and $L$ contains both positions $\alpha,\beta$ of a matching pair $P$.

Fix any strategy for Player B. Compare:
- a **deferred** line of play in which A does not take $P$ immediately, and
- an **immediate-take** line of play in which A first flips $\alpha,\beta$, scores the pair, keeps the turn, and then continues optimally from the resulting state.

The immediate-take state has the same board as the deferred state except that $P$ has been removed, and the same memory list except that $\alpha,\beta$ have been deleted.

The key technical ingredient is the following monotonicity fact.

**Lemma (LRU monotonicity).** Let $L$ be an LRU memory list, and let $L'$ be obtained from $L$ by deleting some entries. Suppose both lists are then updated by the same sequence of observations, none of which is a deleted entry. After every prefix of that sequence, $L'$ is exactly the list obtained from $L$ by deleting some subset of the originally deleted entries. In particular, every non-deleted card remembered in $L$ is also remembered in $L'$, in the same relative LRU order.

*Proof.* By induction on the number of observations. The claim is true initially by construction. Suppose it is true before the next observation $x$.

- If $x$ is already present in both lists, both move $x$ to the MRU end.
- If $x$ is absent from both lists, both insert it. If no eviction occurs, the relation is preserved. If eviction occurs, $L$ evicts its least-recently-used entry, while $L'$, being obtained by deleting entries from $L$, either evicts the same entry or one that was already deleted.
- If $x$ is present in $L'$ then, by the induction hypothesis, it is also present in $L$, reducing to the first case.
- The case where $x$ is present in $L$ but absent from $L'$ cannot occur, because deleted entries are excluded from the observation sequence.

So the invariant holds after every step. $\square$

Apply the lemma with $\alpha,\beta$ as the deleted entries. Relative to the remaining board, the immediate-take state is never worse informed than the deferred state under any common sequence of subsequent non-$P$ observations.

Now let $\tau$ be the first time in the deferred line at which one of the following happens:
1. A takes $P$;
2. B takes $P$;
3. one of $\alpha,\beta$ is evicted before $P$ is taken.

There are three cases.

**Case 1: B takes $P$ in the deferred line.** Then A has allowed the opponent to score a publicly known pair that A could have taken immediately. In the immediate-take line, A already banked that point and is weakly better informed about the remaining board. So immediate take is strictly better.

**Case 2: one of $\alpha,\beta$ is evicted before $P$ is taken.** Then the deferred line has failed to bank an immediately available point and has weakly reduced future information. In the immediate-take line, A already scored the point and is weakly better informed about the rest of the board. So immediate take is strictly better.

**Case 3: A eventually takes $P$ in the deferred line.** At that moment, both lines have removed the same pair $P$, and the remaining board is identical. By the lemma, the immediate-take line is weakly better informed about the remaining non-$P$ cards. Therefore optimal continuation from the immediate-take position is worth at least as much as continuation from the deferred position. So immediate take is weakly better.

In all cases, deferring a publicly known pair cannot improve A's value. Hence there is an optimal strategy that takes any publicly known pair immediately. $\square$

**Remark.** This proof uses the fact that memory is shared and publicly observable. With private memory, A could know a pair that B does not know about. In that setting, holding the pair in reserve could be genuinely strategic.

### Corollary: reduced states contain only singletons

We may therefore restrict attention to strategies that never leave a publicly known pair unmatched at a decision point. In reduced states, the shared memory contains only unmatched singleton positions, one from each of $k$ distinct pairs.

This makes a smaller state description possible.

### Lemma (symmetry of reduced states)

In a reduced state with $n$ remaining pairs and $k$ remembered singleton positions, the value depends only on $(n,k)$, not on the identities of the remembered cards or their particular LRU order.

*Sketch of justification.* Once all remembered cards are unmatched singletons with distinct values, the only strategically relevant facts are:
1. how many remembered singletons there are;
2. the probability that a new flip matches one of them;
3. how many remembered singletons remain after each outcome.

The specific labels of the remembered cards are exchangeable under relabeling of pair identities. Moreover, when a known card is used as the "wasted" second flip in a 1-move or as part of a 0-move, moving one remembered singleton to the MRU end does not create a strategically distinct class of states: all remembered singletons play the same role under relabeling, and the resulting continuation value depends only on the updated count. Thus reduced states may be indexed by $(n,k)$.

So, after greedy matching, backward induction on the state space $(n,k)$ is sufficient.

## 6. The Optimal Strategy

With greedy matching established, I can compute the optimal 0/1/2-move strategy by backward induction on the reduced state space $(n,k)$ with $0 \le k \le \min(n,M)$.

### 6.1 Value function

Let $e_{n,k}$ be the expected score advantage for the player to move.

The base case is
$$
e_{0,0}=0.
$$

If $k=n \le M$, then all remaining pairs are known and the current player takes all of them:
$$
e_{n,n}=n.
$$

At each state the player chooses the move that maximises expected value.

### 6.2 Move values for $k<M$

For $k<M$, the local transitions are identical to Zwick–Paterson. Let
$$
p=\frac{k}{2n-k},
\qquad
q=\frac{2(n-k)}{2n-k}=1-p.
$$

Here $p$ is the probability that a randomly flipped unknown card matches one of the $k$ remembered singletons.

**0-move (pass).** A pass hands the same state to the opponent, so its value is
$$
e^0_{n,k}=-e_{n,k}.
$$
Equivalently, in the optimality equation, allowing a pass is the same as including $0$ among the candidate values. The pass is only legal for $k\ge 2$.

**1-move.**
- With probability $p$, the new card matches a remembered singleton. You score 1, remove that pair, and move again from $(n-1,k-1)$.
- With probability $q$, the new card does not match. It enters memory, so the opponent moves from $(n,k+1)$.

Therefore
$$
e^1_{n,k}
=
p\bigl(1+e_{n-1,k-1}\bigr)
-
q\,e_{n,k+1}.
$$

**2-move.** Flip one new card. If it matches a remembered singleton (probability $p$), this is the same as the matching branch of the 1-move. If not (probability $q$), flip a second new card. Among the
$$
d=2n-k-1
$$
remaining unknown positions:
- with probability $\frac1d$, it matches the first new card (lucky match);
- with probability $\frac{k}{d}$, it matches one of the remembered singletons (and the opponent immediately takes that pair on the next turn);
- with probability $\frac{2(n-k-1)}{d}$, it matches neither.

The resulting formula is
$$
e^2_{n,k}
=
p\bigl(1+e_{n-1,k-1}\bigr)
+
q\left[
\frac1d\bigl(1+e_{n-1,k}\bigr)
-
\frac{k}{d}\bigl(1+e_{n-1,k}\bigr)
-
\frac{2(n-k-1)}{d}e_{n,k+2}
\right].
$$

### 6.3 Move values for $k=M$ (memory full)

This is where bounded memory changes the recursion. When memory is full and a new card does not match, LRU eviction fires: the new card enters memory and the least recently used singleton is evicted. So the number of remembered cards stays at $M$.

**1-move at $k=M$.** A miss loops back to $(n,M)$ rather than moving to $(n,M+1)$:
$$
e^1_{n,M}
=
p\bigl(1+e_{n-1,M-1}\bigr)
-
q\,e_{n,M}.
$$

If the 1-move is optimal at this state, then $e_{n,M}=e^1_{n,M}$, so
$$
e^1_{n,M}
=
\frac{p\bigl(1+e_{n-1,M-1}\bigr)}{1+q}.
$$

**2-move at $k=M$.** Suppose the first new card misses. It enters memory, one old singleton is evicted, and memory now consists of:
- $M-1$ old singletons,
- plus the first new card.

So there are still $M$ remembered cards in total, but only $M-1$ of the old singletons remain available for the second card to match.

Now the second card has three possibilities:

- **Lucky match:** it matches the first new card. The pair is removed, so what remains in memory is just the $M-1$ old singletons. The next state is $(n-1,M-1)$.
- **Auto-take:** it matches one of the remaining old singletons. That pair is removed, while the first new card stays in memory. Again the next state is $(n-1,M-1)$.
- **No match:** after the second miss and the resulting eviction, memory is still full and the game returns to $(n,M)$ with the opponent to move.

Let
$$
d=2n-M-1.
$$
Then
$$
e^2_{n,M}
=
p\bigl(1+e_{n-1,M-1}\bigr)
+
q\left[
\frac1d\bigl(1+e_{n-1,M-1}\bigr)
-
\frac{M-1}{d}\bigl(1+e_{n-1,M-1}\bigr)
-
\frac{2(n-M-1)}{d}e_{n,M}
\right].
$$

Collecting the $e_{n,M}$ terms gives
$$
e^2_{n,M}
=
\frac{
\left(
p+\frac{q(2-M)}{d}
\right)
\bigl(1+e_{n-1,M-1}\bigr)
}{
1+q\frac{2(n-M-1)}{d}
}.
$$

All terms on the right involve states with $n-1$ pairs, which have already been computed.

### 6.4 Optimal move selection

At each state,
$$
e_{n,k}
=
\begin{cases}
e^2_{n,0}, & k=0,\\[6pt]
\max\bigl(e^1_{n,k},e^2_{n,k}\bigr), & k=1,\\[6pt]
\max\bigl(0,e^1_{n,k},e^2_{n,k}\bigr), & k\ge 2.
\end{cases}
$$

The recursion is computed by decreasing $n$, and within each $n$ by decreasing $k$ from $\min(n,M)$ down to $0$. At the boundary $k=M$, the self-referential equations are solved algebraically before proceeding to lower $k$.

### 6.5 Why eviction does not bias match probabilities

A subtle point is why the probability that a new unknown card matches remembered memory is still $k/(2n-k)$ even after evictions have occurred.

The reason is that, conditional on the current reduced state, forgotten cards are indistinguishable from never-seen cards. The original shuffle is uniform, and conditioning on which singleton positions are currently remembered does not bias the distribution of values among the remaining unremembered positions. So the unseen part of the board remains exchangeable.

### 6.6 Why optimal moves can change even below capacity

A subtlety that initially confused me: the optimal move at $k=3$ can differ between Zwick and the bounded model even though $k=3<M=7$ and the local transition formula is identical. The reason is that the *values* being plugged into the formula are different.

Think of it like a river. The riverbed (the local transition formulas) is the same for the first 7 miles. But Zwick's river keeps flowing into deeper endgames, while the bounded model hits a dam at mile 7. The water level (the values) upstream changes because the downstream boundary changed.

Concretely, $e_{12,3}$ depends on $e_{12,5}$, which depends on $e_{12,7}$. In Zwick's model, $e_{12,7}$ feeds into states with $k=8,9,\dots,12$, including the crucial pass states. Under $M=7$, $e_{12,7}$ instead feeds back into the bounded boundary recursion. Same local formula, different downstream values, different optimal move.

## 7. Results

### Move tables

For $n=12$ (24 cards), the optimal move at each knowledge level $k$ is:

| | $k{=}0$ | $1$ | $2$ | $3$ | $4$ | $5$ | $6$ | $7$ | $8$ | $9$ | $10$ | $11$ | $12$ |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Zwick** $(M{=}\infty)$ | 2 | 2 | 1 | 2 | 1 | 2 | 1 | 2 | 1 | **0** | 1 | **0** | 1 |
| **Bounded** $(M{=}7)$ | 2 | 2 | 1 | **1** | 1 | **1** | 1 | **1** | — | — | — | — | — |

The strategies agree when you know 0, 1, or 2 cards. They diverge at $k=3,5,7$: Zwick says flip two new cards, while bounded memory says flip only one.

**In practical terms:** once you know a few card positions, stop flipping two unknowns per turn. Flip one, keep what you know, and match when you can. The second unknown card mostly churns memory.

### Position values

Expected gain for the starting player (negative = Player 2 advantage):

| $M$ | $n=8$ | $n=10$ | $n=12$ | $n=16$ | $n=20$ |
|-----|-------|--------|--------|--------|--------|
| 3 | $+0.039$ | $+0.030$ | $+0.024$ | $+0.017$ | $+0.014$ |
| 5 | $-0.004$ | $+0.013$ | $+0.018$ | $+0.015$ | $+0.012$ |
| **7** | $-0.007$ | $-0.039$ | $\mathbf{-0.030}$ | $+0.007$ | $+0.010$ |
| 9 | $-0.033$ | $-0.039$ | $-0.020$ | $-0.026$ | $-0.001$ |
| $\infty$ | $-0.033$ | $-0.038$ | $-0.020$ | $-0.018$ | $-0.012$ |

These are exact values computed in rational arithmetic, with no simulation noise. For $n=12$ at $M=7$, the Player 2 advantage is $-0.030$, which is larger in magnitude than Zwick's perfect-recall value of $-0.020$.

At very low $M$, memory is too constrained for strategy to matter and Player 1's first-mover advantage dominates. At high $M \ge n$, the model recovers Zwick exactly.

## 8. Simulations

The exact DP gives values and optimal moves, but simulations show how strategies perform against one another and let me explore settings where exact analysis is unavailable, such as fluctuating capacity and asymmetric players.

### 8.1 Bounded-memory optimum vs Zwick

Both players have $M=7$. I pit the bounded-memory optimal strategy against Zwick's perfect-recall strategy in all four matchup combinations, across board sizes from $n=8$ to $n=36$.

<a href="/figures_memory/bounded_vs_zwick_conditional.svg" class="image-popup">
  <img src="/figures_memory/bounded_vs_zwick_conditional.svg" alt="Conditional win rate: Bounded vs Zwick"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>

<a href="/figures_memory/bounded_vs_zwick_gain.svg" class="image-popup">
  <img src="/figures_memory/bounded_vs_zwick_gain.svg" alt="Expected gain: Bounded vs Zwick"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>

The bounded-memory strategy dominates Zwick at every board size. At $n=36$ (72 cards), the bounded-memory player gains roughly 3 full pairs over a Zwick opponent. The gain grows with board size because larger boards spend more time near the full-memory boundary where the two strategies differ.

The strategy matrix at $n=16$ shows that bounded-memory optimal dominates Zwick within this two-strategy comparison: no matter which of the two strategies your opponent plays, you are better off playing bounded. (The DP proves optimality within the full class of 0/1/2-move strategies; the simulation confirms it specifically against Zwick.)

<a href="/figures_memory/bounded_vs_zwick_matrix.svg" class="image-popup">
  <img src="/figures_memory/bounded_vs_zwick_matrix.svg" alt="Strategy matrix"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>

### 8.2 Robustness to fluctuation

Real working-memory capacity is not fixed at exactly 7 every turn. I model this by drawing the effective capacity each turn from
$$
M_0+\mathrm{Uniform}(-\sigma,+\sigma),
$$
clamped to a feasible range.

<a href="/figures_memory/fluctuation_bounded_vs_zwick.svg" class="image-popup">
  <img src="/figures_memory/fluctuation_bounded_vs_zwick.svg" alt="Bounded-optimal vs Zwick under fluctuation"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>

<a href="/figures_memory/fluctuation_cross_gain.svg" class="image-popup">
  <img src="/figures_memory/fluctuation_cross_gain.svg" alt="Cross-strategy gain under fluctuation"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>

The bounded-memory strategy remains dominant across all fluctuation levels. Even when capacity varies widely, flipping one new card is robust, while Zwick's two-card exploration remains fragile because it churns memory.

### 8.3 Asymmetric memory

What happens when the two players have different capacities? Say, an adult ($M=9$) against a child ($M=5$). Each player uses the optimal strategy for their own capacity.

<a href="/figures_memory/asymmetric_gain_heatmap.svg" class="image-popup">
  <img src="/figures_memory/asymmetric_gain_heatmap.svg" alt="Asymmetric memory: expected gain heatmap"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>
<a href="/figures_memory/asymmetric_p2cond_heatmap.svg" class="image-popup">
  <img src="/figures_memory/asymmetric_p2cond_heatmap.svg" alt="Asymmetric memory: P2 conditional win rate"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>
<a href="/figures_memory/asymmetric_memory_advantage.svg" class="image-popup">
  <img src="/figures_memory/asymmetric_memory_advantage.svg" alt="Asymmetric memory: advantage curve"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>

Memory capacity dwarfs positional advantage. A single extra memory slot is worth far more than the tiny first- or second-player effect.

With asymmetric memory, however, the shared-memory theorem no longer applies literally: the two players no longer have the same information state. So in that setting, these strategies should be understood as strong heuristics rather than proven optima.

### 8.4 Draw rate vs capacity

<a href="/figures_memory/draw_rate_vs_capacity.svg" class="image-popup">
  <img src="/figures_memory/draw_rate_vs_capacity.svg" alt="Draw rate and P2 advantage vs memory capacity"
     style="background: #fff; border-radius: 6px; cursor: zoom-in;">
</a>

The draw rate shows a striking non-monotonic pattern. For small boards, passes can create sticky stalemates at certain capacities; once memory becomes large enough to cover the whole board, those stalemates disappear because one player eventually sweeps everything.

For standard game sizes with human-scale memory ($M\approx 7$), $M$ is far below $n$, passes typically disappear from the optimal strategy, and draws are rare.

## 9. Play Against the Bot

Theory is nice, but the best way to feel the difference between strategies is to play. The game below lets you play Memory against a bot that uses either the bounded-memory optimal strategy or Zwick's perfect-recall strategy. You can adjust the bot's memory capacity and see which cards it remembers in real time.

Try this experiment: play a few games against the bounded-memory bot ($M=7$), then switch to Zwick. With the bounded strategy, the bot flips one unknown card and keeps its memories stable. With Zwick, it flips two unknowns and old memories get evicted. The Zwick bot *looks* smarter because it explores faster, but on bounded memory it performs worse.

<iframe src="/assets/memory_game.html" width="100%" height="750" style="border:none; border-radius:8px; overflow:hidden;"></iframe>

## 10. What I Learned

1. **Greedy matching is optimal** under shared memory: leaving a publicly known pair on the board is weakly dominated by taking it immediately.
2. **The bounded-memory optimum is simple:** flip two new cards only at the very start; once you know a few positions, flip only one and match when you can.
3. **The bounded-memory strategy dominates Zwick's** on large boards, and the gap grows with size.
4. **Who goes first barely matters.** The positional effect is real but tiny.
5. **What really matters is memory capacity.** A single extra memory slot is worth much more than move order.
6. **Stalemates are mostly a perfect-memory phenomenon.** Under human-scale memory limits and standard board sizes, they largely disappear.

The private-memory version of the game remains open and looks much harder: once players remember different things, the problem becomes one of incomplete information rather than perfect information.

## References

- Cowan, N. (2001). The magical number 4 in short-term memory: A reconsideration of mental storage capacity. *Behavioral and Brain Sciences*, 24(1), 87-114.
- Miller, G. A. (1956). The magical number seven, plus or minus two: Some limits on our capacity for processing information. *Psychological Review*, 63(2), 81-97.
- Zwick, U., & Paterson, M. S. (1993). The memory game. *Theoretical Computer Science*, 110(1), 169-196.
- Kilian, S. (2025). Who starts the game of memory? [Blog post](https://samuelkilian.de/about.html).