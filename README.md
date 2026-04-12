# Fedora Kinoite (Custom)

A custom [Fedora Kinoite](https://fedoraproject.org/kinoite/) image built with
[BlueBuild](https://blue-build.org/), based on
[Universal Blue's kinoite-main](https://universal-blue.org/images/).

**DISCLAIMER: I used AI to help make this as it is a learning experience for me**

## What's included

### Layered RPM packages
| Package | Purpose |
|---|---|
| `fastfetch` | System info display |
| `gamemode` / `gamemoded` | Feral GameMode for gaming performance |
| `gamescope` | Valve's micro-compositor |
| `openvpn` | VPN client |
| `steam` | Gaming platform (via RPM Fusion) |
| `wireguard-tools` | VPN client |

### Pre-installed Flatpaks (installed on first boot)
| App | Purpose |
|---|---|
| Fedora Media Writer | Write OS images to USB |
| GOverlay | MangoHud/vkBasalt GUI config |
| Gwenview | Image viewer |
| Heroic Games Launcher | Epic/GOG/Amazon games |
| KCalc | Calculator |
| KolourPaint | Simple image editor |
| Kontainer | Distrobox GUI frontend |
| KRDC | Remote desktop client |
| Krita | Digital painting |
| LibreOffice | Office suite |
| MangoHud VulkanLayer | In-game performance overlay |
| Obsidian | Markdown knowledge base |
| Okular | Document viewer |
| Plex Desktop | Plex media player |
| Prism Launcher | Minecraft launcher |
| ProtonUp-Qt | Manage Proton-GE versions for Steam |
| Vesktop | Discord client (Vencord) |
| Virt Manager | Virtual machine manager |
| Virt Manager QEMU Extension | QEMU/KVM backend for Virt Manager |
| VLC | Media player |
| VSCodium | VS Code without Microsoft telemetry |

---

## Setting up your own build

### 1. Create your GitHub repository

Use this repo as a template (or fork it). Make sure the repo name matches what
you want the image to be called — the image will be published to
`ghcr.io/<your-github-username>/<repo-name>`.

### 2. Set up cosign signing (recommended)

Install cosign and generate a key pair:

```bash
# Install cosign (or use the GitHub CLI extension)
cosign generate-key-pair
```

Add the generated **`cosign.key`** content as a GitHub Actions secret named
`SIGNING_SECRET` (Settings → Secrets and variables → Actions → New repository
secret).

Add the generated **`cosign.pub`** file to the root of your repo and commit it.

### 3. Enable GitHub Actions and GHCR

- Go to **Settings → Actions → General** and ensure Actions are allowed.
- Go to **Settings → Actions → General → Workflow permissions** and set to
  "Read and write permissions".
- Push to `main` (or trigger the workflow manually) to kick off your first
  build. The image will be published to
  `ghcr.io/<your-username>/<repo-name>:latest`.

### 4. Make the package public (optional but recommended for rebasing)

Go to your GitHub profile → **Packages** → find your image → **Package
settings** → Change visibility to **Public**.

---

## Rebasing to this image

Once your first build completes successfully, run these commands on any Fedora
Atomic system (Silverblue, Kinoite, etc.):

```bash
# Step 1 — rebase to the unsigned image first (required for first-time rebase)
rpm-ostree rebase ostree-unverified-registry:ghcr.io/<your-username>/fedora-kinoite-custom:latest

# Reboot
systemctl reboot

# Step 2 — rebase to the signed image
rpm-ostree rebase ostree-image-signed:docker://ghcr.io/<your-username>/fedora-kinoite-custom:latest

# Reboot again to land on the signed deployment
systemctl reboot
```

Replace `<your-username>` with your GitHub username. If you renamed the repo,
replace `fedora-kinoite-custom` with your repo name as well.

---

## Customisation

Edit `recipe.yml` to add or remove packages and Flatpaks, then push to `main`.
GitHub Actions will rebuild automatically. Your system will pick up the new
image on the next `rpm-ostree upgrade` / reboot cycle.

See the [BlueBuild documentation](https://blue-build.org/learn/) for all
available modules (fonts, systemd services, kernel args, gschema overrides,
etc.).
