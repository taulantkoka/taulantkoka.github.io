# Deployment Instructions

## IMPORTANT: Replace ALL existing files

This won't work if you just add these files alongside the old ones.
You need to **delete everything** in your repo first, then add these files.

### Step-by-step:

1. **Go to your repo**: https://github.com/taulantkoka/taulantkoka.github.io
2. **Delete ALL existing files** in the repo (or clone locally and remove everything)
3. **Upload/push ALL files from this zip** to the root of the repo
4. **Wait 1-2 minutes** for GitHub Pages to rebuild
5. **Check** https://taulantkoka.com

### Quick way via command line:

```bash
# Clone your repo
git clone https://github.com/taulantkoka/taulantkoka.github.io.git
cd taulantkoka.github.io

# Remove everything except .git
find . -maxdepth 1 ! -name '.git' ! -name '.' -exec rm -rf {} +

# Unzip the new site files here
unzip /path/to/taulantkoka-site-v4.zip -d .

# Remove the README (it's just for you, not for the site)
rm README.md

# Push
git add -A
git commit -m "Rebuild site with sidebar navigation"
git push
```

### File structure you should have:

```
taulantkoka.github.io/
├── _config.yml
├── _data/
│   └── navigation.yml
├── _pages/
│   ├── about.md
│   ├── blog.md
│   ├── contact.md
│   ├── projects.md                  ← overview page
│   ├── project-variable-selection.md
│   ├── project-signal-recovery.md
│   ├── project-visibility-graph.md
│   └── project-psi-cv-pipeline.md
├── _posts/
│   └── 2026-03-29-hello-world.md
├── assets/
│   └── css/
│       └── main.scss
├── Gemfile
└── index.md
```

### After deploying, replace:
- `YOUR_FORM_ID` in `_pages/contact.md` with your Formspree form ID
