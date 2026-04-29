# Anino Assets Repository

A centralized, production-ready asset repository for the Anino project, supporting multiple game types: **AR**, **RPG**, **VR**, **Web**, and **shared assets**. This repository uses **Git LFS** for efficient management of large binary files and **Git submodules** for seamless integration into main projects.

---

## 📋 Repository Structure

```
anino-assets/
├─ .github/
│  ├─ workflows/
│  │  ├─ assets-ci.yml              # CI validation on push/PR
│  │  └─ release.yml                # Release workflow for version tags
│  └─ pull_request_template.md      # PR template for contributors
├─ ar/                              # AR game assets
│  ├─ audio/
│  ├─ models/
│  └─ textures/
├─ rpg/                             # RPG game assets
│  ├─ animations/
│  ├─ audio/
│  ├─ sprites/
│  └─ tiles/
├─ vr/                              # VR game assets
│  ├─ audio/
│  ├─ models/
│  └─ textures/
├─ web/                             # Web game assets
│  ├─ audio/
│  ├─ images/
│  └─ other/
├─ shared/                          # Shared assets across all game types
│  ├─ audio/
│  ├─ fonts/
│  ├─ textures/
│  └─ UI/
├─ metadata/                        # Asset descriptions, licenses, versioning
├─ LICENSE
├─ README.md                        # This file
└─ ASSET_MANAGEMENT.md              # Detailed asset management guide
```

---

## 🚀 Getting Started

### Prerequisites

Install the following on your development machine:

- **Git** — Version control system
  ```bash
  # Arch Linux / EndeavourOS
  sudo pacman -S git
  
  # Ubuntu / Debian
  sudo apt install git -y
  ```

- **Git LFS** — Large file storage
  ```bash
  # Arch Linux / EndeavourOS
  sudo pacman -S git-lfs
  
  git lfs install
  ```
  For other platforms: https://git-lfs.github.com/

- **Game Engine** (optional) — Godot 4.x, Unity, or your project's engine version

---

## 📖 Integration Guide: Adding Assets to Your Game Project

This section provides a **step-by-step workflow** for integrating assets into game projects using Git submodules, sparse checkout, and Git LFS.

### Step 1: Clone with Submodules

Clone your game repository with assets initialized:

```bash
git clone --recurse-submodules https://github.com/YourOrg/your-game-project.git
cd your-game-project
```

> If already cloned, initialize submodules: `git submodule update --init --recursive`

### Step 2: Add Assets (If Not Already Configured)

```bash
git submodule add https://github.com/NoContextOrg/anino-assets.git assets
git add .
git commit -m "chore(assets): add anino-assets as submodule"
```

### Step 3: Configure Sparse Checkout (Recommended)

Pull only needed folders to optimize disk usage:

```bash
cd assets
git sparse-checkout init --cone
git sparse-checkout set web shared  # Adjust as needed
cd ..
```

**Benefits:** Reduces storage, faster clones, avoids unrelated folders (`ar/`, `rpg/`, `vr/`)

### Step 4: Pull Git LFS Assets

```bash
cd assets
git lfs install
git lfs pull
cd ..
```

### Step 5: Lock to Stable Version (Recommended)

Pin your project to a tested asset snapshot:

```bash
cd assets
git fetch --tags
git checkout v1.0.0  # Use semantic versioning
cd ..
git add assets
git commit -m "chore(assets): lock assets to v1.0.0"
git push
```

### Daily Workflow

```bash
# Pull latest code
git pull

# Update submodules
git submodule update --init --recursive

# Update assets (if pinned, skip this—you'll stay on your tag)
cd assets
git pull origin main && git lfs pull  # Or: git checkout v1.1.0 && git lfs pull
cd ..
git add assets && git commit -m "chore(assets): update" && git push
```

### Quick Reference

| Task | Command |
|------|---------|
| Clone with assets | `git clone --recurse-submodules <repo>` |
| Initialize submodules | `git submodule update --init --recursive` |
| Sparse checkout | `cd assets && git sparse-checkout init --cone && git sparse-checkout set web shared` |
| Pull LFS files | `cd assets && git lfs pull` |
| Update assets | `cd assets && git pull origin main && git lfs pull` |
| Lock to tag | `cd assets && git checkout v1.0.0` |
| List LFS files | `git lfs ls-files` |
| Check file tracking | `git check-attr -a <file>` |

---

## 🔐 Best Practices

### For Game Developers (Consuming Assets)

1. **Do not `.gitignore` the `assets/` folder** — Track it as a submodule
2. **Use sparse checkout** to reduce clone size and improve performance
3. **Lock to a stable tag** for releases to prevent unexpected changes
4. **Never edit submodule content directly** — Always update the central repository
5. **Document asset versions** in your release notes
6. **Keep commits atomic** — Small, focused updates improve traceability
7. **Test after updates** — Verify the game loads properly with new assets

### For Asset Maintainers (Managing This Repository)

1. **Use semantic versioning** for tags: `v1.0.0`, `v1.1.0-beta`
2. **Commit message convention:**
   ```
   feat: add new character animations for RPG
   fix: resolve texture compression issue
   docs: update asset guide
   chore: optimize image sizes
   ```
3. **Keep PRs scoped** to a single purpose (one asset type or feature)
4. **Git LFS configuration** — Track all large file types in `.gitattributes`:
   ```bash
   git lfs track "*.png" "*.jpg" "*.wav" "*.ogg" "*.psd" "*.blend" "*.fbx" "*.glb"
   ```
5. **Coordinate updates** with game projects, especially when paths/filenames change
6. **Document breaking changes** for consuming projects

---

## 🔧 Troubleshooting

### Git LFS: Pointers Instead of Files

**Problem:** Assets show as text pointers instead of actual files

**Solution:**
```bash
cd assets
git lfs install
git lfs pull
```

### Submodule Not Updating

**Problem:** Assets folder is outdated

**Solution:**
```bash
cd assets
git fetch
git log --oneline -5

# Update or pin to a version
git pull origin main  # Latest
# OR
git checkout v1.0.0   # Specific version

cd ..
git add assets
git commit -m "chore(assets): update"
```

### Sparse Checkout Issues

**Problem:** Missing or unwanted folders

**Solution:**
```bash
cd assets
git sparse-checkout init --cone
git sparse-checkout set web shared  # Adjust as needed
git pull
cd ..
```

### CI Workflow Fails (Large Untracked Files)

**Problem:** Asset file not tracked by Git LFS

**Solution:**
```bash
git lfs track "*.png"
git rm --cached large-file.png
git add large-file.png
git commit -m "chore: configure Git LFS"
```

---

---

## � CI/CD Workflows

This repository includes automated workflows to ensure asset quality and manage releases.

### Assets CI (`assets-ci.yml`)

**Triggers:** Every push and PR to `main`

**Validations:**
- ✅ Required folders exist: `ar/`, `rpg/`, `vr/`, `web/`, `shared/`, `metadata/`
- ✅ Files >5MB tracked by Git LFS
- ✅ Generates `asset-manifest.txt` (directory listing with file sizes)
- ✅ Uploads manifest as artifact

**Status:** Required to pass before PR merge

### Release (`release.yml`)

**Triggers:** Version tags (`v*`) or manual workflow dispatch

**Actions:**
- ✅ Generates `asset-manifest.txt` with release metadata
- ✅ Creates GitHub Release with auto-generated notes
- ✅ Uploads manifest as release asset
- ✅ Auto-detects prerelease status from tag format

---

## � Asset Manifest

Both workflows generate `asset-manifest.txt`:
- Repository metadata (commit SHA, timestamp)
- Complete file listing with sizes
- Useful for auditing and tracking versions

**Locations:**
- CI: Workflow artifacts (30-day retention)
- Releases: Permanently attached to GitHub Release

---

### CI Workflow Failure (Missing Folders/Untracked Files)

**Problem:** Assets CI workflow fails on push/PR

**Debug:**
1. Check logs: GitHub → **Actions** → **Assets CI** → latest run
2. Verify folders: `ar/`, `rpg/`, `vr/`, `web/`, `shared/`, `metadata/`
3. Verify LFS tracking: `git lfs ls-files`
4. Check `.gitattributes` configuration

**Solution:**
```bash
# Create .gitkeep placeholders
touch ar/.gitkeep rpg/.gitkeep vr/.gitkeep web/.gitkeep shared/.gitkeep metadata/.gitkeep
git add .gitkeep && git commit -m "chore: add gitkeep"

# Verify LFS setup
git lfs install && git lfs pull
```

### Release Workflow Not Triggering

**Problem:** Tags don't trigger release workflow

**Solution:** Ensure tag matches semantic versioning:
```bash
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

---

## 📝 Contributing to This Repository

### Branch Protection Rules

All changes require:

- ✅ Pull request (no direct pushes to `main`)
- ✅ Minimum 1 approval
- ✅ CI workflow pass (`assets-ci`)
- ✅ Conversation resolution
- ✅ No force push or deletion

### Creating a Pull Request

1. **Create a feature branch:**
   ```bash
   git checkout -b feature/add-rpg-sprites
   ```

2. **Make changes and commit:**
   ```bash
   git add .
   git commit -m "feat: add new character sprites for RPG"
   git lfs track "*.png"  # If adding new file types
   ```

3. **Push and open PR:**
   ```bash
   git push -u origin feature/add-rpg-sprites
   ```
   Complete the [PR template](.github/pull_request_template.md)

---

## 📚 Documentation

| Document | Purpose |
|----------|---------|
| **[ASSET_MANAGEMENT.md](./ASSET_MANAGEMENT.md)** | Detailed workflows, CI/CD, asset management, best practices |
| **[.github/pull_request_template.md](./.github/pull_request_template.md)** | PR submission template |
| **[LICENSE](./LICENSE)** | Repository license |

> **Getting started?** See the **Integration Guide** section above.

---

---

## � Asset Organization

| Folder | Purpose | Contents |
|--------|---------|----------|
| `ar/` | AR game assets | Audio, models, textures |
| `rpg/` | RPG game assets | Animations, audio, sprites, tiles |
| `vr/` | VR game assets | Audio, models, textures |
| `web/` | Web game assets | Audio, images, misc files |
| `shared/` | Shared by all types | Audio, fonts, textures, UI |
| `metadata/` | Asset metadata | Descriptions, licenses, versioning |

---

## 🤝 Support

- **Questions:** Check [ASSET_MANAGEMENT.md](./ASSET_MANAGEMENT.md) for detailed workflows
- **Issues:** Report via GitHub Issues
- **Pull Requests:** Follow the [PR template](.github/pull_request_template.md)

---

## � References

- [Git Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Git LFS](https://git-lfs.github.com/)
- [Sparse Checkout](https://git-scm.com/docs/git-sparse-checkout)
- [GitHub Actions](https://docs.github.com/en/actions)
- [Semantic Versioning](https://semver.org/)

---

## 📄 License

Licensed under the terms in [LICENSE](./LICENSE).

---

**Last Updated:** April 8, 2026  
**Repository:** [NoContextOrg/anino-assets](https://github.com/NoContextOrg/anino-assets)