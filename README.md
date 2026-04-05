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

## 🚀 Quick Start

### Prerequisites

- **Git with LFS support:** https://git-lfs.github.com/
- **Access to the main project repository**

### Adding Assets as a Submodule

Add the asset repository to your game project:

```bash
cd /path/to/your/game-project
git submodule add -b main --depth 1 https://github.com/NoContextOrg/anino-assets.git assets/rpg
```

**Parameters:**
- `-b main` — Track the main branch
- `--depth 1` — Shallow clone to minimize storage
- `assets/rpg` — Local path where assets will reside

### Initializing Git LFS

Ensure Git LFS is installed on your system:

```bash
git lfs install
git lfs pull  # Pull all large files
```

### Updating Assets

Fetch the latest asset updates from the central repository:

```bash
git submodule update --remote --merge
```

---

## 📦 Asset Organization

| Folder | Purpose | Includes | Game Types |
|--------|---------|----------|-----------|
| `ar/` | AR game assets | Audio, models, textures | AR games |
| `rpg/` | RPG game assets | Animations, audio, sprites, tiles | RPG games |
| `vr/` | VR game assets | Audio, models, textures | VR games |
| `web/` | Web game assets | Audio, images, misc files | Web games |
| `shared/` | Common assets | Audio, fonts, textures, UI | All game types |
| `metadata/` | Asset metadata | Descriptions, licenses, versioning | Reference |

---

## 🔄 CI/CD Workflows

### Assets CI Workflow (`assets-ci.yml`)

**Triggers:** Automatically on every `push` and `pull_request` to `main`

**Validations:**
- ✅ Checks that all required folders exist: `ar/`, `rpg/`, `vr/`, `web/`, `shared/`, `metadata/`
- ✅ Scans for untracked files larger than 5MB (must use Git LFS)
- ✅ Ensures Git LFS pointers are properly configured
- ✅ Generates `asset-manifest.txt` listing all files with timestamps
- ✅ Uploads manifest as workflow artifact

**Status Check:** Required — pull requests cannot merge without passing this workflow.

### Release Workflow (`release.yml`)

**Triggers:** 
- Version tags matching `v*` (e.g., `v1.0.0`, `v1.1.0-beta`)
- Manual trigger via `workflow_dispatch`

**Actions:**
- ✅ Generates/reuses `asset-manifest.txt` with release metadata
- ✅ Creates a GitHub Release with auto-generated notes
- ✅ Uploads asset manifest as release asset
- ✅ Auto-detects prerelease status from tag format

---

## 📝 Contributing

### Branch Protection Rules

All changes to `main` branch require:

- ✅ **Pull Request Review** — Cannot push directly to `main`
- ✅ **Minimum 1 Approval** — At least one reviewer must approve
- ✅ **CI Workflow Pass** — `assets-ci` workflow must pass successfully
- ✅ **Conversation Resolution** — All discussions must be resolved
- ✅ **No Force Push** — Prevents accidental history rewriting
- ✅ **No Deletion** — Branch cannot be deleted without admin override

### Creating a Pull Request

1. **Create a feature branch:**
   ```bash
   git checkout -b feature/add-rpg-sprites
   ```

2. **Make your changes** (add/update assets):
   ```bash
   # Ensure large files are tracked by Git LFS
   git lfs track "*.png" "*.wav" "*.ogg" "*.psd"
   ```

3. **Commit your changes:**
   ```bash
   git add .
   git commit -m "feat: add new character sprites for RPG"
   ```

4. **Push to GitHub:**
   ```bash
   git push -u origin feature/add-rpg-sprites
   ```

5. **Open a Pull Request:**
   - Navigate to GitHub and create PR from your feature branch
   - Complete the [PR template](.github/pull_request_template.md)
   - Request reviewers
   - Address feedback and iterate

---

## 📚 Documentation

| Document | Purpose |
|----------|---------|
| **[ASSET_MANAGEMENT.md](./ASSET_MANAGEMENT.md)** | Comprehensive guide: Git submodules, Git LFS, best practices, troubleshooting |
| **[.github/pull_request_template.md](./.github/pull_request_template.md)** | Standard template for all pull requests |
| **[LICENSE](./LICENSE)** | Repository licensing information |

---

## 🔐 Best Practices

### Asset Management

1. **Selective Submodule Inclusion** — Only clone game-type folders your project needs to optimize repository size
   ```bash
   # AR project - only include AR assets
   git submodule add -b main https://github.com/NoContextOrg/anino-assets.git assets/ar
   
   # RPG project - include RPG + shared
   git submodule add -b main https://github.com/NoContextOrg/anino-assets.git assets/rpg
   git submodule add -b main https://github.com/NoContextOrg/anino-assets.git assets/shared
   ```

2. **Always Include Shared Assets** — When multiple game types depend on common resources (fonts, UI)

3. **Never Edit Submodules Directly** — Always update the central repository; changes propagate automatically to all consuming projects

4. **Verify Git LFS Installation** — Ensure all team members run `git lfs install` before cloning/updating

### Git LFS Configuration

1. **Track Large File Types:**
   ```bash
   git lfs track "*.png" "*.jpg" "*.wav" "*.ogg" "*.mp3" "*.psd" "*.blend" "*.fbx" "*.glb"
   git add .gitattributes
   git commit -m "config: add Git LFS tracking for asset file types"
   ```

2. **Verify LFS Tracking:**
   ```bash
   git lfs ls-files  # List all LFS-tracked files
   git check-attr -a <file>  # Check if specific file is tracked
   ```

3. **Prevent Accidental Large File Commits:**
   ```bash
   git lfs migrate import --include="*.psd,*.blend"  # Migrate existing large files
   ```

### Development Workflow

1. **Use Semantic Versioning** — Tag releases as `v1.0.0`, `v1.1.0`, `v2.0.0-beta`

2. **Commit Message Convention:**
   ```
   feat: add new character animations for RPG
   fix: resolve texture compression issue in VR assets
   docs: update asset management guide
   chore: optimize image sizes
   ```

3. **PR Scope** — Keep PRs focused to a single purpose (e.g., one asset type or one feature)

4. **Documentation** — Update `ASSET_MANAGEMENT.md` or `README.md` if introducing new workflows or asset types

---

## 🔧 Troubleshooting

### Large Files Not Tracked by Git LFS

**Error:** CI workflow fails with "untracked large files" message

**Solution:**
```bash
# Verify Git LFS is installed
git lfs install

# Configure tracking for your file types
git lfs track "*.png"

# Remove and re-add the file
git rm --cached large-file.png
git add large-file.png
git commit -m "chore: configure Git LFS for PNG files"
```

### Missing Required Folders

**Error:** CI workflow fails "Required folder 'X' not found"

**Solution:**
```bash
# Create placeholder .gitkeep files for empty folders
touch ar/.gitkeep rpg/.gitkeep vr/.gitkeep web/.gitkeep shared/.gitkeep metadata/.gitkeep
git add .gitkeep
git commit -m "chore: add gitkeep to preserve folder structure"
```

### Git LFS Pointers Not Pulling

**Error:** LFS files appear as text pointers instead of actual content

**Solution:**
```bash
# Ensure LFS is installed
git lfs install

# Pull LFS files
git lfs pull

# Verify tracking
git lfs ls-files
```

### CI Workflow Fails Unexpectedly

**Debug:**
1. Check workflow logs in GitHub Actions: **Actions** → **Assets CI** → latest run
2. Verify folder structure exists
3. Verify all files >5MB are tracked by Git LFS
4. Ensure `.gitattributes` is properly configured

---

## 📊 Asset Manifest

Both CI and Release workflows generate `asset-manifest.txt`, which contains:

- Repository metadata (commit SHA, generation timestamp)
- Complete directory structure
- File listing with byte sizes
- Useful for auditing and asset tracking

**Location:**
- CI artifacts: Available in workflow run details (30-day retention)
- Release assets: Attached to each GitHub Release permanently

---

## 🤝 Support & Contribution

- **Issues:** Report bugs or request features via GitHub Issues
- **Pull Requests:** Follow the [PR template](.github/pull_request_template.md)
- **Questions:** Check [ASSET_MANAGEMENT.md](./ASSET_MANAGEMENT.md) first

---

## 📄 License

This repository is licensed under the terms specified in the [LICENSE](./LICENSE) file.

---

## 🔗 Links

- **Asset Repository:** https://github.com/NoContextOrg/anino-assets
- **Main Project Repository:** https://github.com/NoContextOrg/anino
- **Git LFS Documentation:** https://git-lfs.github.com/
- **Git Submodules Documentation:** https://git-scm.com/book/en/v2/Git-Tools-Submodules
- **GitHub Actions Documentation:** https://docs.github.com/en/actions
- **Semantic Versioning:** https://semver.org/

---

**Last Updated:** April 6, 2026  
**Repository:** [NoContextOrg/anino-assets](https://github.com/NoContextOrg/anino-assets)