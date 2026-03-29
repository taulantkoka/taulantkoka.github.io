# Deployment Instructions

## IMPORTANT: Replace ALL existing files + use GitHub Actions

This site uses a GitHub Actions workflow to build Jekyll with the remote theme.

### Step 1: Set GitHub Pages source to GitHub Actions

1. Go to **Settings → Pages** in your repo
2. Under **Build and deployment → Source**, select **"GitHub Actions"** (not "Deploy from a branch")

### Step 2: Replace all files in your repo

```bash
# Clone your repo
git clone https://github.com/taulantkoka/taulantkoka.github.io.git
cd taulantkoka.github.io

# Remove everything except .git
find . -maxdepth 1 ! -name '.git' ! -name '.' -exec rm -rf {} +

# Unzip the new site files here
unzip /path/to/taulantkoka-site-v4.zip -d .

# Push (this triggers the GitHub Actions build)
git add -A
git commit -m "Rebuild site with sidebar navigation"
git push
```

### Step 3: Wait and check

- Go to **Actions** tab in your repo to watch the build
- Once the green checkmark appears, check https://taulantkoka.com

### File structure:

```
taulantkoka.github.io/
├── .github/
│   └── workflows/
│       └── jekyll.yml               ← GitHub Actions workflow
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
├── CNAME
├── Gemfile
└── index.md
```

### After deploying, replace:
- `YOUR_FORM_ID` in `_pages/contact.md` with your Formspree form ID
