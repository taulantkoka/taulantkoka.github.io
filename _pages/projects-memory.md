---
title: "The Memory Game"
permalink: /projects/memory/
layout: single
sidebar:
  nav: "projects"
toc: true
---
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: {
      extensions: ["bm.js", "AMSmath.js", "AMSsymbols.js"]
    },
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
      processEscapes: true
    }
  });
</script>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

<h2 data-toc-skip> The Optimal Strategy for Memory Under Bounded Working Memory</h2>

## 1. The Game

Memory (also known as Concentration or Pairs) is a card game played with $n$ pairs of identical cards ($2n$ cards total), shuffled and placed face down on a table. Players alternate turns. On each turn, a player flips two cards face up. If the two cards match, the player takes the pair and plays again. If they do not match, both cards are flipped back face down and the turn passes to the opponent. The player with the most pairs at the end wins.

Despite being a children's game, Memory has a surprisingly rich strategic structure. Every card flip reveals information to *both* players, and the key decision is how to balance learning (flipping new cards) against exploiting what you already know (flipping a card whose match you remember). This raises a natural question: **does it matter who goes first?**

If you want to skip the theory and just play, there's an [interactive game](#9-play-against-the-bot) at the end where you can take on the optimal strategy yourself.

*A note on methodology: this is the first time I've produced a mathematical result with the help of an AI agent. The proofs, dynamic programming formulations, and simulations in this post were developed in collaboration with Claude Opus (Anthropic).*
 
## 2. What Zwick and Paterson Showed

In 1993, Uri Zwick and Michael Paterson published the definitive analysis of Memory under the assumption that both players have *perfect memory*, they remember the identity and position of every card ever flipped (Zwick & Paterson, 1993).

Their key insight was to characterise each game position by just two numbers:
- $n$: the number of pairs remaining on the board
- $k$: the number of cards both players have previously inspected (and remember perfectly)

At each turn, the player chooses one of three moves:

- **0-move (pass):** Flip two already-known, non-matching cards ("do nothing"). The turn passes to the opponent.
- **1-move:** Flip one new card. If it matches a remembered card, take the pair and play again. If not, "waste" the second flip by re-flipping a card you already know.
- **2-move:** Flip one new card. If it matches a remembered card, take the pair. If not, flip a *second* new card. If the two new cards happen to match, take the pair. Otherwise, the turn passes.

Through backward induction on the state space $(n, k)$, they computed the exact optimal strategy. The results are surprising:

1. **Player 2 has a slight advantage** for even $n \geq 8$, of magnitude $\approx 1/(4n)$ pairs.
2. **The key strategic weapon is the pass.** At high $k$, both players know where most pairs are but deliberately refrain from taking them, engaging in a delicate game of parity-control. The pass manipulates *whose turn it will be* when the last pairs are finally taken.
3. **Stalemates occur** in about 20–30% of games under optimal play, when both players pass indefinitely.

This is elegant, but immediately raises the question: does any of this survive when players don't have perfect memory?

## 3. First Attempt: Stochastic Memory Decay

My first approach, inspired by [Samuel Kilian](https://samuelkilian.de/about.html) (whose posts about the memory game piqued my interest), was to model forgetting as probabilistic decay: each turn, every remembered card has a probability $\delta$ of being forgotten. This is analytically convenient, the Zwick framework mostly carries over, and it lets you sweep continuously from perfect memory ($\delta = 0$) to complete amnesia ($\delta = 1$).

I ran large-scale Monte Carlo simulations (up to 1 million games per parameter point) under this model. The results were interesting: at low $\delta$, the Zwick P2 advantage survives. Around $\delta \approx 0.10\text{–}0.15$, there's a phase transition where the Zwick stalemate regime collapses and the game becomes more decisive. At high $\delta$, the game converges to a coin flip.

But the model bothered me. Probabilistic decay means you might remember a card you saw 20 turns ago while forgetting one you saw 2 turns ago. That doesn't match my experience of how memory works in this game. You either correctly remember where a card is or you don't. And when your brain is full and you see something new, something old gets pushed out. The bottleneck feels more like *capacity* than *reliability*.

## 4. From Miller's Law to a Tractable Model

This pointed me toward George Miller's famous 1956 paper on the "magical number seven, plus or minus two" (Miller, 1956). Later work has revised the effective capacity downward, with Cowan (2001) arguing for closer to 4 chunks under stricter definitions, and the exact number depends heavily on chunking and task design. For this project I use $M = 7$ as a convenient human-scale benchmark, not as a literal psychological constant. The important point is not the precise number but the qualitative fact: working memory has a hard capacity limit, and it is much smaller than a typical Memory board.

Of course, real human memory is more complex than a fixed-size buffer. Decay, interference, attention, emotional salience, rehearsal, they all play a role. A fully realistic model would need to account for all of these, and would almost certainly be intractable.

So I opted for a deliberate simplification: a deterministic bounded-memory model that captures the capacity constraint while remaining analytically solvable. Each player has a working memory of capacity $M$ (number of card positions). When memory is full and a new card is observed, the **least recently used** entry is evicted. This is not meant to be a faithful model of human cognition, it's the simplest model that has the property I care about: memory is bounded, and new information displaces old information.

The reason this simplification works so well turns out to be mathematical rather than psychological.

**The key property:** Since both players see every flip and both use the same deterministic eviction rule, **both players have identical memory at all times.** The memory state is common knowledge. This means the game remains one of perfect information, the same class of game Zwick analysed. All that changes is the state space.

Any stochastic model of forgetting, whether decay, random interference, or attention fluctuation, would give the two players *different* memories, creating private information and turning the game into something like poker. That's a vastly harder problem (and an interesting open one). The deterministic LRU model sidesteps this entirely, keeping Zwick's backward induction machinery available.

Following Zwick, I characterise positions by $(n, k)$ where $k \leq M$ is the number of cards currently in the shared memory.

## 5. Greedy Matching Is Optimal
 
Before computing the optimal strategy, I need to settle a foundational question: when you know where a matching pair is, should you always take it immediately? Or could it sometimes be better to hold the pair in reserve, leaving it in memory unmatched to control the timing of who gets the last pair, as Zwick's pass does under perfect recall?
 
### Theorem
 
*Under shared bounded memory with deterministic LRU eviction, suppose that at the start of a player's turn the shared memory contains both positions of some matching pair $P = \{\alpha, \beta\}$. Then there is an optimal move that flips $\alpha$ and $\beta$ immediately. Equivalently: any legal move that leaves $P$ on the board is weakly dominated by taking $P$ at once.*
 
Note: the claim is weak dominance, not strict. If you know two pairs simultaneously, taking either one first yields the same value. What cannot happen is that deferring a known pair is *strictly better* than taking it.
 
### Proof
 
I work at the level of the full game state: the set of unmatched cards on the board, whose turn it is, and the ordered shared memory list $L$ (from least recently used to most recently used). Assume it is Player A's turn, and $L$ contains both positions $\alpha, \beta$ of a matching pair $P$. Fix any legal action $a$ that does not take $P$ immediately. I will construct another strategy for A that starts by taking $P$ first and does at least as well against every strategy of B.
 
**Step 1: Coupling two plays on the same board.** Fix the underlying shuffled board and fix an arbitrary strategy for B. Compare two plays from the same initial state:
 
- *Deferred play:* A begins with action $a$, leaving $P$ on the board.
- *Immediate-take play:* A first flips $\alpha, \beta$, scores the pair, keeps the turn, and then on the bonus turn follows the same intended move $a$ on the remaining board (which is well-defined since $a$ does not involve $\alpha$ or $\beta$, as those were not flipped in the deferred play either).
 
In the immediate-take play, the board is the same as in the deferred play except that pair $P$ has been removed, and the memory list is the same except that $\alpha, \beta$ have been deleted.
 
**Step 2: LRU monotonicity lemma.** The key technical ingredient:
 
*Lemma (LRU monotonicity).* Let $L$ be an LRU memory list, and let $L'$ be obtained from $L$ by deleting some entries. Suppose both lists are then updated by the same sequence of observations, none of which is a deleted entry. After every prefix of that sequence, $L'$ is exactly the list obtained from $L$ by deleting some subset of the originally deleted entries. In particular, every non-deleted card remembered in $L$ is also remembered in $L'$, in the same relative LRU order.
 
*Proof of lemma.* By induction on the number of observations. The invariant holds initially by construction. Suppose it holds before the next observation $x$.
 
- If $x$ is already present in both lists, both move $x$ to the MRU end, preserving the relation.
- If $x$ is absent from both lists, both insert it. If no eviction occurs, the relation is preserved. If eviction occurs, $L$ evicts its least-recently-used entry, while $L'$, being obtained by deleting entries from $L$, either evicts the same entry or one that was already deleted from $L$. Thus $L'$ remains obtainable from $L$ by deleting entries.
- If $x$ is present in $L'$, then by the induction hypothesis it is also present in $L$, reducing to the first case.
- If $x$ is present in $L$ but absent from $L'$, then $x$ must be one of the deleted entries, which is excluded by hypothesis.
 
Therefore the invariant holds after every step. $\square$
 
Applying this with $\alpha, \beta$ as the deleted entries: after the immediate take and then exposing the same sequence of non-$P$ cards, the immediate-take play always has at least as much memory about the remaining board as the deferred play.
 
**Step 3: Follow the two plays until $P$ is resolved.** In the deferred play, let $\tau$ be the first time one of the following happens: (1) A takes $P$, (2) B takes $P$, or (3) one of $\alpha, \beta$ is evicted from memory before $P$ is taken.
 
Until $\tau$, neither play flips $\alpha$ or $\beta$ except that the immediate-take play removed them at time 0. So both plays reveal the same non-$P$ cards in the same order, and by the lemma, the immediate-take play has weakly better memory about all remaining cards throughout.
 
**Case 1: B takes the pair in the deferred play.** The deferred play gave the opponent a publicly known point that was available to A. In the immediate-take play, A already scored it at time 0, and memory for the rest of the board is weakly better by the lemma. Immediate take is strictly better.
 
**Case 2: The pair is evicted before being taken.** The deferred play failed to bank an immediately available point and weakly reduced future information by allowing eviction. In the immediate-take play, A already scored the point and has weakly better memory by the lemma. Immediate take is strictly better.
 
**Case 3: A eventually takes the pair in the deferred play.** This is the non-strict case. When A finally takes $P$ in the deferred play, delete that move and compare what remains with the immediate-take play. At that point: both plays have removed the same pair $P$, both are at the same turn parity (taking a pair grants another turn in both timelines), both have seen the same non-$P$ observations, and the immediate-take play has weakly better memory by the lemma. Since the remaining board is the same (all non-$P$ cards in the same positions), every move that is legal in the deferred continuation is also legal in the immediate-take continuation, and the weakly better memory means A can replicate any decision rule the deferred play would have used. Therefore A can mimic the deferred continuation and is never worse off. Immediate take is at least as good. $\square$
 
**Remark.** This proof requires shared memory. If players had private, unobservable memories, A could know a pair that B doesn't. Holding it in reserve could then be genuinely strategic, because B can't steal what B doesn't know about. The private-memory case remains an open problem.
 
**Corollary.** We may restrict attention to optimal strategies that never leave a publicly known pair unmatched at a decision point. Hence the shared memory contains only unmatched singletons with distinct values, and the game state is fully described by $(n, k)$ where $n$ is the number of remaining pairs and $k \leq M$ is the number of remembered singleton positions. Backward induction on this state space yields the optimal strategy.
 
## 6. The Optimal Strategy
 
With greedy matching established, I can compute the optimal 0/1/2-move strategy by backward induction on the state space $(n, k)$ with $0 \leq k \leq \min(n, M)$. This section walks through the derivation step by step. If you want to skip the math and go straight to the results, jump to [Section 7](#7-results).
 
### 6.1 Value Function
 
The value function $e_{n,k}$ represents the expected score advantage for the player to move. The base case is $e_{0,0} = 0$ (no cards left, no advantage). If $k = n \leq M$ (all remaining cards are known and fit in memory), the current player takes all remaining pairs: $e_{n,n} = n$.
 
For each state, the player chooses the move (0, 1, or 2) that maximises $e_{n,k}$. After the move, it becomes the opponent's turn, and the opponent's value is $-e$ of whatever state results (since the game is zero-sum from the opponent's perspective).
 
### 6.2 Move Values for $k < M$ (Memory Not Full)
 
Here the transitions are identical to Zwick–Paterson. Let
 
$$p = \frac{k}{2n - k} \qquad q = \frac{2(n-k)}{2n-k}$$
 
where $p$ is the probability that a randomly flipped unknown card matches one of the $k$ singletons in memory (there are $k$ target cards among the $2n - k$ unknown positions), and $q = 1 - p$.
 
**0-move (pass):** Flip two known non-matching cards. The opponent faces state $(n, k)$. Value:
$$e^0_{n,k} = -e_{n,k}$$
This is only worth considering if $e_{n,k} \leq 0$ (i.e., the current position is unfavourable), giving $e^0 = 0$. The pass is legal only for $k \geq 2$.
 
**1-move:** Flip one new card.
- With probability $p$: it matches a known singleton. Take the pair ($+1$), memory loses one entry ($k \to k-1$), board shrinks ($n \to n-1$), and you play again from $(n-1, k-1)$.
- With probability $q$: no match. The new card enters memory ($k \to k+1$). You waste your second flip on a known card (revealing nothing new). Turn passes to opponent, who faces $(n, k+1)$.
 
$$e^1_{n,k} = p \cdot (1 + e_{n-1,\, k-1}) - q \cdot e_{n,\, k+1}$$
 
**2-move:** Flip one new card. If it matches (prob $p$), same as the 1-move. If not (prob $q$), flip a *second* new card. The second card's outcomes, among the $d = 2n - k - 1$ remaining unknown positions:
- With probability $\frac{1}{d}$: it matches the first card (lucky match). Take the pair, play again. The first card's singleton match was already removed from memory when the pair was taken, but the lucky match means both new cards leave together. Memory returns to $k$ of the original singletons minus the matched one, so the state is $(n-1, k)$ with the current player to move.
- With probability $\frac{k}{d}$: it matches a *different* singleton in memory. The opponent auto-takes that pair (both players saw the match). A note on the memory bookkeeping here: after the auto-take, the matched singleton and the second new card are removed ($-2$), but the first new card remains in memory ($+1$). Net change: $k - 1 + 1 = k$. The state is $(n-1, k)$, opponent to move.
- With probability $\frac{2(n-k-1)}{d}$: no match at all. Both new cards are now in memory ($k \to k+2$). Turn passes.
 
The full 2-move formula (following Zwick–Paterson Section 3) is:
 
$$e^2_{n,k} = p \cdot (1 + e_{n-1,\, k-1}) + q \left[ \frac{1}{d}(1 + e_{n-1,\, k}) - \frac{k}{d}(1 + e_{n-1,\, k}) - \frac{2(n{-}k{-}1)}{d} \cdot e_{n,\, k+2} \right]$$
 
### 6.3 Move Values for $k = M$ (Memory Full: The New Boundary)
 
This is where the bounded model departs from Zwick. When memory is full and a new card doesn't match, LRU eviction fires: the new card enters memory and the oldest entry is pushed out. The net effect: $k$ stays at $M$ instead of increasing to $M + 1$.
 
**1-move at $k = M$:** The miss transitions back to $(n, M)$ instead of $(n, M+1)$:
 
$$e^1_{n,M} = p \cdot (1 + e_{n-1,\, M-1}) - q \cdot e_{n,M}$$
 
If the 1-move turns out to be optimal at this state (i.e., $e_{n,M} = e^1_{n,M}$), this becomes self-referential. Solving:
 
$$e^1_{n,M} + q \cdot e^1_{n,M} = p \cdot (1 + e_{n-1,\, M-1})$$
$$e^1_{n,M} = \frac{p \cdot (1 + e_{n-1,\, M-1})}{1 + q}$$
 
**2-move at $k = M$:** The same LRU capping applies to both new cards. The lucky-match and auto-take branches are unchanged (they reduce $n$, so they don't hit the boundary). Only the "no match on either card" branch is affected: instead of transitioning to $(n, M+2)$, both evictions keep $k$ at $M$, looping back to $(n, M)$. The full equation is:
 
$$e^2_{n,M} = p \cdot (1 + e_{n-1,\, M-1}) + q \left[ \frac{1}{d}(1 + e_{n-1,\, M'}) - \frac{M}{d}(1 + e_{n-1,\, M'}) - \frac{2(n{-}M{-}1)}{d} \cdot e_{n,\, M} \right]$$
 
where $d = 2n - M - 1$ and $M' = \min(M, n-1)$ (the memory state after a pair is removed). Collecting the $e_{n,M}$ terms and solving:
 
$$e^2_{n,M} = \frac{p \cdot (1 + e_{n-1,\, M-1}) + q \left[ \frac{1}{d}(1 + e_{n-1,\, M'}) - \frac{M}{d}(1 + e_{n-1,\, M'}) \right]}{1 + q \cdot \frac{2(n-M-1)}{d}}$$
 
All terms on the right-hand side involve states with $n - 1$ pairs, which are already computed. So the boundary is resolved in closed form, just like the 1-move case.
 
### 6.4 Putting It Together: Optimal Move Selection
 
At each state $(n, k)$, the optimal move is:
 
$$e_{n,k} = \begin{cases} e^2_{n,0} & \text{if } k = 0 \text{ (1-move not available)} \\\ \max(e^1_{n,k},\; e^2_{n,k}) & \text{if } k = 1 \\\ \max(0,\; e^1_{n,k},\; e^2_{n,k}) & \text{if } k \geq 2 \text{ (pass available)} \end{cases}$$
 
The recursion is computed by decreasing $n$, and within each $n$ by decreasing $k$ from $\min(n, M)$ down to $0$. At the boundary $k = M$, the self-referential equations are solved algebraically before proceeding to lower $k$.
 
### 6.5 A Subtlety: Transition Probabilities After Eviction
 
One subtle point: why is the probability that a new card matches a remembered card still $k/(2n-k)$ after evictions have occurred? Because a forgotten card is indistinguishable from a never-seen card. The original shuffle is uniform, and conditioning on the current memory state (which cards are remembered with which values), the unremembered positions remain uniformly distributed. The eviction history doesn't create any bias.
 
### 6.6 Why Optimal Moves Change Even Below Capacity
 
A subtlety that initially confused me: the optimal move at $k = 3$ can differ between Zwick and the bounded model, even though $k = 3 < M = 7$ and the local transition formula is identical. The reason is that the *values* being plugged into the formula are different.
 
Think of it like a river. The riverbed (transition formulas) is the same for the first 7 miles. But Zwick's river flows another 5 miles to the sea, while the bounded model hits a dam at mile 7. The water level (values) at mile 3 is different because of the dam downstream, even though the riverbed at mile 3 is identical.
 
Concretely: $e_{12,3}$ depends on $e_{12,5}$, which depends on $e_{12,7}$. In Zwick's model, $e_{12,7}$ feeds into the deep endgame at $k = 8, 9, \ldots, 12$, including the crucial passes at $k = 9$ and $k = 11$. Under $M = 7$, $e_{12,7}$ loops back to itself. Different boundary, different values, different optimal moves.
 
## 7. Results
 
### Move Tables
 
For $n = 12$ (24 cards), the optimal move at each knowledge level $k$:
 
| | $k{=}0$ | $1$ | $2$ | $3$ | $4$ | $5$ | $6$ | $7$ | $8$ | $9$ | $10$ | $11$ | $12$ |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Zwick** $(M{=}\infty)$ | 2 | 2 | 1 | 2 | 1 | 2 | 1 | 2 | 1 | **0** | 1 | **0** | 1 |
| **Bounded** $(M{=}7)$ | 2 | 2 | 1 | **1** | 1 | **1** | 1 | **1** | — | — | — | — | — |
 
The strategies agree when you know 0, 1, or 2 cards. They diverge at $k = 3, 5, 7$: Zwick says flip two new cards (aggressive exploration), bounded-memory says flip only one (conservative). States $k \geq 8$ don't exist under $M = 7$, since your brain can't hold that many cards.
 
**In practical terms:** once you know a few card positions, stop flipping two unknowns per turn. Flip one, keep what you know, match when you can. The second unknown card just pushes out something useful.
 
### Position Values
 
Expected gain for the starting player (negative = Player 2 advantage):
 
| $M$ | $n=8$ | $n=10$ | $n=12$ | $n=16$ | $n=20$ |
|-----|-------|--------|--------|--------|--------|
| 3 | $+0.039$ | $+0.030$ | $+0.024$ | $+0.017$ | $+0.014$ |
| 5 | $-0.004$ | $+0.013$ | $+0.018$ | $+0.015$ | $+0.012$ |
| **7** | $-0.007$ | $-0.039$ | $\mathbf{-0.030}$ | $+0.007$ | $+0.010$ |
| 9 | $-0.033$ | $-0.039$ | $-0.020$ | $-0.026$ | $-0.001$ |
| $\infty$ | $-0.033$ | $-0.038$ | $-0.020$ | $-0.018$ | $-0.012$ |
 
These are exact values computed in rational arithmetic, with no simulation noise. For $n = 12$ at $M = 7$, the P2 advantage is $-0.030$, which is 52% larger than Zwick's perfect-recall value of $-0.020$. Bounded memory *amplifies* the second player's advantage.
 
At very low $M$, memory is too constrained for strategy to matter and Player 1's first-mover advantage dominates. At high $M \geq n$, the model recovers Zwick exactly. The P2 advantage peaks around $M \approx 5\text{–}9$, which happens to coincide with Miller's range. I don't claim this is anything more than a coincidence: the game was designed for humans, not the other way around.

## 8. Simulations

The exact DP gives me values and optimal moves, but to see how strategies actually *perform* against each other, and to explore settings where exact computation isn't available (fluctuating capacity, asymmetric players), I run Monte Carlo simulations. All simulations use joblib for parallelisation and the exact optimal strategy tables from the DP.

### 8.1 Bounded-Memory Optimum vs Zwick

Both players have $M = 7$. I pit the bounded-memory optimal strategy against Zwick's perfect-recall strategy in all four matchup combinations, across board sizes from $n = 8$ (16 cards) to $n = 36$ (72 cards).

<a href="/figures_memory/bounded_vs_zwick_conditional.svg" class="image-popup">
  <img src="/figures_memory/bounded_vs_zwick_conditional.svg" alt="Conditional win rate: Bounded vs Zwick"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>

<a href="/figures_memory/bounded_vs_zwick_gain.svg" class="image-popup">
  <img src="/figures_memory/bounded_vs_zwick_gain.svg" alt="Expected gain: Bounded vs Zwick"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>

The bounded-memory strategy dominates Zwick at every board size. The advantage is not subtle: at $n = 36$ (72 cards, a standard large game), the bounded-memory player gains about 3 full pairs over a Zwick opponent. The gain grows with board size because larger boards mean more time at $k = M$ where the strategies diverge.

The strategy matrix at $n = 16$ shows that bounded-memory optimal dominates Zwick within this two-strategy comparison: no matter which of the two strategies your opponent plays, you're better off playing bounded. (The DP proves optimality within the full class of 0/1/2-move strategies; the simulation confirms it specifically against Zwick.)

<a href="/figures_memory/bounded_vs_zwick_matrix.svg" class="image-popup">
  <img src="/figures_memory/bounded_vs_zwick_matrix.svg" alt="Strategy matrix"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>

An interesting detail: under symmetric play, both strategies give nearly fair games (P2 conditional win rate $\approx 50\%$), but the *direction* of the tiny bias differs. Bounded v Bounded slightly favours P2 (consistent with the exact DP), while Zwick v Zwick under $M = 7$ slightly favours P1. The Zwick strategy's aggressive 2-moves churn memory, destroying the parity structure that gives P2 an advantage under perfect recall. With parity gone, P1's first-mover advantage in finding lucky matches takes over.

### 8.2 Robustness to Fluctuation

Real working memory capacity isn't fixed at exactly 7 every turn. Some turns you're sharp, others you lose focus. I model this by drawing the effective capacity each turn from $M_0 + \text{Uniform}(-\sigma, +\sigma)$, clamped to $[2, M_0 + \sigma]$.

<a href="/figures_memory/fluctuation_bounded_vs_zwick.svg" class="image-popup">
  <img src="/figures_memory/fluctuation_bounded_vs_zwick.svg" alt="Bounded-optimal vs Zwick under fluctuation"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>

<a href="/figures_memory/fluctuation_cross_gain.svg" class="image-popup">
  <img src="/figures_memory/fluctuation_cross_gain.svg" alt="Cross-strategy gain under fluctuation"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>

The bounded-memory strategy remains dominant across all fluctuation levels. Even at $\sigma = 5$ (capacity swinging between 2 and 12 each turn), the bounded-memory player still gains 2+ pairs over a Zwick opponent on large boards. This robustness makes intuitive sense: flipping one new card is safe whether your capacity happens to be 5 or 9, while Zwick's two-card flip is risky at any capacity.

### 8.3 Asymmetric Memory

The most practically relevant question: what happens when the two players have different memory capacities? Say, an adult ($M = 9$) playing against a child ($M = 5$). Each player uses the optimal strategy for their own capacity.

<a href="/figures_memory/asymmetric_gain_heatmap.svg" class="image-popup">
  <img src="/figures_memory/asymmetric_gain_heatmap.svg" alt="Asymmetric memory: expected gain heatmap"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>
<a href="/figures_memory/asymmetric_p2cond_heatmap.svg" class="image-popup">
  <img src="/figures_memory/asymmetric_p2cond_heatmap.svg" alt="Asymmetric memory: P2 conditional win rate"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>
<a href="/figures_memory/asymmetric_memory_advantage.svg" class="image-popup">
  <img src="/figures_memory/asymmetric_memory_advantage.svg" alt="Asymmetric memory: advantage curve"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>

Memory capacity dwarfs positional advantage. At $n = 24$ (48 cards), a player with $M = 9$ beats a player with $M = 7$ roughly 80% of the time, regardless of who goes first. The positional advantage is worth about 0.03 pairs; a single extra memory slot is worth 1–4 pairs.

The diagonal of the P2 win rate heatmap (equal memory) shows the P2 advantage at three decimal places: 0.501, 0.504, 0.508. Real, but roughly 100$\times$ smaller than the effect of one memory slot.

Note that with asymmetric memory, the two players no longer have identical memory states, so the shared-memory proof (Section 5) doesn't strictly apply. Each player uses the optimal strategy for their own $M$ as a reasonable heuristic, but this is not provably optimal in the asymmetric case.

### 8.4 Draw Rate vs Capacity

<a href="/figures_memory/draw_rate_vs_capacity.svg" class="image-popup">
  <img src="/figures_memory/draw_rate_vs_capacity.svg" alt="Draw rate and P2 advantage vs memory capacity"
     style="background: #fff; border-radius: 6px;cursor: zoom-in;">
</a>

The draw rate reveals a striking non-monotonic pattern, most visible for $n = 8$ (16 cards). Draws are low at $M = 5$ (no passes in the optimal strategy, draws are only score ties), spike to over 50% at $M = 7$ (the strategy includes a pass at $k = 6$, creating sticky stalemates), then crash back to near zero at $M = 8$ (memory covers the entire board, and one player eventually sweeps everything).

Why does $M = 8$ have so many fewer draws than $M = 7$ when both strategies include a pass? At $M = 8 = n$, knowledge $k$ can reach $8 = n$, at which point the current player knows every card and sweeps the board. This creates an escape hatch from stalemate: any game where $k$ reaches $n$ ends decisively. At $M = 7$, $k$ can never exceed 7, so there is no escape hatch, and the stalemate at $k = 6$ is permanent.

For standard game sizes ($n \geq 16$) with human memory ($M \approx 7$), $M$ is far below $n$, passes never appear, and draws are rare. The elaborate game of parity-control from Zwick's analysis simply doesn't arise in practice; real games produce a winner most of the time.

## 9. Play Against the Bot

Theory is nice, but the best way to feel the difference between strategies is to play. The game below lets you play Memory against a bot that uses either the bounded-memory optimal strategy or Zwick's perfect-recall strategy. You can adjust the bot's memory capacity and see which cards it remembers in real time.

Try this experiment: play a few games against the bounded-memory bot ($M = 7$), then switch to Zwick. Watch the bot's memory panel. With the bounded strategy, the bot flips one unknown card and keeps its memories stable. With Zwick, it flips two unknowns and old memories get evicted. The Zwick bot *looks* smarter (it explores faster) but actually performs worse, as it churns through its own memory.

You can also switch to "Bot vs Bot" mode to run simulations directly in your browser.

<iframe src="/assets/memory_game.html" width="100%" height="750" style="border:none; border-radius:8px; overflow:hidden;"></iframe>

## 10. Code & Reproducibility

All code used for the dynamic programming computations, simulations, and the interactive game is available on GitHub:

**[GitHub: memory-game](https://github.com/taulantkoka/memory-game)**

The repository includes the exact DP solver for the bounded-memory model, optimal strategy tables, Monte Carlo simulation scripts, and the browser-based game used in this post. All figures and numerical results can be reproduced from scratch using the provided code.

### Quick Start

```bash
git clone https://github.com/taulantkoka/memory-game.git
cd memory-game
pip install -r requirements.txt
python main.py
```

## 11. What I Learned

1. **Greedy matching is optimal** under shared memory: any move that leaves a publicly known pair on the board is weakly dominated by taking it immediately. The proof uses a coupling argument and an LRU monotonicity lemma.

2. **The optimal strategy is simple:** flip two new cards only at the very start; once you know a few positions, flip only one and match when you can. Never pass. This is what Kilian (2025) found empirically and called "defensive" play. I proved it's the exact optimum under bounded memory and showed *why*: the second flip churns memory when capacity is the bottleneck.

3. **The bounded-memory strategy dominates Zwick's** by a growing margin as board size increases, and this holds under capacity fluctuation.

4. **It (barely) matters who goes first.** Player 2 has a provable but tiny advantage, about 0.03 pairs at $n = 12$, roughly 1.5% conditional win rate. No human would ever notice.

5. **What actually matters is memory capacity.** A single extra memory slot is worth 50–100$\times$ more than the positional advantage. If you want to win at Memory, train your working memory. Or play against someone with less of it.

6. **Stalemates are a theoretical curiosity.** Under human memory constraints and standard board sizes, the optimal strategy never passes and games are almost always decisive.

The private-memory case (where each player has their own unobservable memory) remains open and is likely much harder, it's a game of incomplete information where bluffing and information hiding become possible.

## References

- Cowan, N. (2001). The magical number 4 in short-term memory: A reconsideration of mental storage capacity. *Behavioral and Brain Sciences*, 24(1), 87–114.
- Miller, G. A. (1956). The magical number seven, plus or minus two: Some limits on our capacity for processing information. *Psychological Review*, 63(2), 81–97.
- Zwick, U. & Paterson, M. S. (1993). The memory game. *Theoretical Computer Science*, 110(1), 169–196.
- Kilian, S. (2025). Who starts the game of memory? Blog post. [samuelkilian.de](https://samuelkilian.de/about.html)