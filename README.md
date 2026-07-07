# 📦 Personal Arch Linux Repository Builder

[🇻🇳 Xem bản tiếng Việt](#-hướng-dẫn-tiếng-việt)

A fully automated, zero-cost Arch Linux binary repository powered entirely by GitHub Actions and GitHub Releases.

This template allows you to maintain a personal `pacman` repository without paying for a VPS or managing a build server. It automatically tracks updates from the Arch User Repository (AUR), builds the packages (including split packages), caches them smartly to save runner memory, and publishes the `.pkg.tar.zst` binaries to a fixed GitHub Release.

---

## 🚀 How to use this repository (For End-Users)

If you just want to install packages that have already been built in this repository, you simply need to add it to your pacman configuration.

### 1. Check CPU compatibility

This repository currently builds packages using the `cachyos/cachyos-v3` Docker image. That means the published binaries are intended for systems that support the `x86-64-v3` instruction set or newer.

Before using this repository, make sure your machine supports `x86-64-v3` or higher. If your CPU only supports generic `x86-64`, the packages from this repository may not run correctly on your system.

### 2. Edit your `pacman.conf`

Append the following configuration to the bottom of your `/etc/pacman.conf`:

```ini
[my-arch-repo]
SigLevel = Optional TrustAll
Server = https://github.com/nhktmdzhg/my-arch-repo/releases/download/repository
```

### 3. Sync and Install

Refresh your package databases and install your desired packages just like official Arch packages:

```bash
sudo pacman -Sy
sudo pacman -S <package-name>
```

## 🛠️ Create Your Own Automated Repo

Want to build your own automated AUR farm? Follow these steps to fork and configure this system.

### Step 1: Fork and Clean

- Fork this repository to your own GitHub account.
- Delete any existing package directories you do not need.

### Step 2: Choose the correct Docker image

This repository currently uses the `cachyos/cachyos-v3` Docker image in the workflows `build-release.yml` and `verify-change.yml`.

Before running your own AUR farm, review your target machines and adjust the Docker image if needed:

- Use `cachyos/cachyos` if you want generic builds for wider hardware compatibility.
- Use `cachyos/cachyos-v3` if your target machines support `x86-64-v3`.
- Use `cachyos/cachyos-v4` if your target machines support `x86-64-v4` or newer.

If you need a different target level, edit the workflow files `build-release.yml` and `verify-change.yml` and replace the image name accordingly.

### Step 3: Configure Repository Permissions

To allow GitHub Actions to build and release packages, you must enable specific workflow permissions:

- Go to your repository Settings > Actions > General.
- Under Workflow permissions, select Read and write permissions.
- Check the box that says Allow GitHub Actions to create and approve pull requests.
- Click Save.

### Step 4: Create a Personal Access Token (PAT)

Why is this needed? When the auto-sync bot creates a Pull Request, GitHub's default token prevents other workflows (like the build verification) from triggering to prevent infinite loops. Using your PAT tells GitHub that you authorized the PR, allowing the build checks to run automatically.

- Go to your personal GitHub Settings (top right profile picture) > Developer settings > Personal access tokens > Tokens (classic).
- Click Generate new token (classic).
- Give it a note (e.g., Arch Repo Sync), set an expiration, and check the repo scope.
- Generate and copy the token (it starts with `ghp_`...).
- Go back to your Repository Settings > Secrets and variables > Actions.
- Click New repository secret.
- Name it exactly: `MY_PAT_TOKEN` and paste your token into the Secret field. Add it.

### Step 5: Manage Your Packages

The entire repository is controlled by a single file: `packages.txt`.

- Open `packages.txt`.
- Add the exact names of the AUR packages you want to build (one per line).

  > Note on Split Packages: If a package has multiple sub-packages, just write the base package name. The workflow will automatically discover and build all sub-packages.

- Commit the changes to the `main` branch.

### Step 6: Relax and Let it Run

- The Sync Workflow runs at a scheduled time. It reads `packages.txt`, pulls the latest PKGBUILDs from the AUR, deletes removed packages, and creates a clean Pull Request.
- The Verify Workflow automatically triggers on that PR to test-build the packages using paru, ensuring nothing is broken before merging.
- The Publish Workflow triggers when you merge the PR into `main`. It smartly checks existing assets, only rebuilds what has changed, generates a fresh pacman database, and uploads everything to the repository Release tag.

**Disclaimer:** `SigLevel = Optional TrustAll` is used for convenience in this personal setup, meaning packages are not GPG-signed. Only use this configuration for repositories you fully control or trust.

---

## 🇻🇳 Hướng dẫn tiếng Việt

Kho gói nhị phân Arch Linux cá nhân này được tự động hóa hoàn toàn, không tốn chi phí máy chủ riêng, và hoạt động bằng GitHub Actions kết hợp GitHub Releases.

Template này cho phép bạn duy trì một kho `pacman` cá nhân mà không cần thuê VPS hoặc tự quản lý build server. Hệ thống sẽ tự theo dõi cập nhật từ AUR, build gói (bao gồm cả split packages), dùng cache hợp lý để tiết kiệm tài nguyên runner, rồi phát hành file `.pkg.tar.zst` lên một GitHub Release cố định.

### 🚀 Cách dùng repository này (dành cho người dùng cuối)

Nếu bạn chỉ muốn cài các gói đã được build sẵn từ repository này, bạn chỉ cần thêm repo vào cấu hình `pacman`.

#### 1. Kiểm tra khả năng tương thích CPU

Repository này hiện build package bằng Docker image `cachyos/cachyos-v3`. Điều đó có nghĩa là các gói nhị phân được phát hành được nhắm tới các máy hỗ trợ tập lệnh `x86-64-v3` trở lên.

Trước khi dùng repository này, hãy chắc chắn rằng máy của bạn hỗ trợ `x86-64-v3` hoặc cao hơn. Nếu CPU của bạn chỉ hỗ trợ mức `x86-64` generic, các gói từ repository này có thể không chạy đúng trên hệ thống của bạn.

#### 2. Chỉnh sửa `pacman.conf`

Thêm đoạn cấu hình sau vào cuối file `/etc/pacman.conf`:

```ini
[my-arch-repo]
SigLevel = Optional TrustAll
Server = https://github.com/nhktmdzhg/my-arch-repo/releases/download/repository
```

#### 3. Đồng bộ và cài đặt

Làm mới cơ sở dữ liệu gói rồi cài gói bạn muốn giống như gói chính thức của Arch:

```bash
sudo pacman -Sy
sudo pacman -S <package-name>
```

### 🛠️ Tạo repository tự động của riêng bạn

Nếu bạn muốn tự dựng một hệ thống build AUR tự động cho tài khoản của mình, hãy làm theo các bước sau.

#### Bước 1: Fork và dọn dẹp

- Fork repository này về tài khoản GitHub của bạn.
- Xóa các thư mục gói có sẵn mà bạn không cần.

#### Bước 2: Chọn Docker image phù hợp

Repository này hiện đang dùng Docker image `cachyos/cachyos-v3` trong hai workflow `build-release.yml` và `verify-change.yml`.

Trước khi chạy AUR farm của riêng bạn, hãy xem máy đích của bạn thuộc mức nào và chỉnh Docker image cho phù hợp:

- Dùng `cachyos/cachyos` nếu bạn muốn build kiểu generic để tương thích với nhiều phần cứng hơn.
- Dùng `cachyos/cachyos-v3` nếu máy đích của bạn hỗ trợ `x86-64-v3`.
- Dùng `cachyos/cachyos-v4` nếu máy đích của bạn hỗ trợ `x86-64-v4` hoặc cao hơn.

Nếu cần đổi target, hãy sửa hai file workflow `build-release.yml` và `verify-change.yml`, rồi thay tên image tương ứng.

#### Bước 3: Cấu hình quyền cho repository

Để GitHub Actions có thể build và phát hành gói, bạn cần bật đúng quyền cho workflow:

- Vào `Settings > Actions > General` của repository.
- Trong phần **Workflow permissions**, chọn **Read and write permissions**.
- Tích vào ô **Allow GitHub Actions to create and approve pull requests**.
- Nhấn **Save**.

#### Bước 4: Tạo Personal Access Token (PAT)

Vì sao cần bước này? Khi bot auto-sync tạo Pull Request, token mặc định của GitHub sẽ chặn việc kích hoạt các workflow khác (ví dụ workflow verify build) để tránh vòng lặp vô hạn. Khi dùng PAT của bạn, GitHub hiểu rằng PR đó được bạn cho phép, nên các build check có thể chạy tự động.

- Vào GitHub cá nhân của bạn (ảnh đại diện góc trên phải) > `Developer settings` > `Personal access tokens` > `Tokens (classic)`.
- Chọn **Generate new token (classic)**.
- Đặt tên dễ nhớ (ví dụ: `Arch Repo Sync`), chọn thời hạn hết hạn, rồi cấp quyền `repo`.
- Tạo token và sao chép lại token đó (thường bắt đầu bằng `ghp_`...).
- Quay lại repository của bạn tại `Settings > Secrets and variables > Actions`.
- Chọn **New repository secret**.
- Đặt tên chính xác là `MY_PAT_TOKEN` rồi dán token vào ô Secret.

#### Bước 5: Quản lý danh sách gói

Toàn bộ repository này được điều khiển bởi một file duy nhất là `packages.txt`.

- Mở file `packages.txt`.
- Thêm đúng tên các gói AUR mà bạn muốn build, mỗi dòng một gói.

  > Lưu ý về Split Packages: Nếu một gói có nhiều gói con, bạn chỉ cần ghi tên gói gốc. Workflow sẽ tự phát hiện và build toàn bộ các gói con liên quan.

- Commit thay đổi vào branch `main`.

#### Bước 6: Để hệ thống tự chạy

- **Sync Workflow** sẽ chạy theo lịch, đọc `packages.txt`, kéo PKGBUILD mới nhất từ AUR, xóa gói đã bị loại bỏ, rồi tạo một Pull Request sạch.
- **Verify Workflow** sẽ tự kích hoạt trên Pull Request đó để test-build gói bằng `paru`, giúp kiểm tra trước khi merge.
- **Publish Workflow** sẽ chạy khi bạn merge PR vào `main`. Workflow này sẽ kiểm tra asset đã có, chỉ rebuild phần thay đổi, tạo lại pacman database mới, rồi upload tất cả lên Release tag của repository.

**Lưu ý:** `SigLevel = Optional TrustAll` được dùng để tiện cho mô hình repository cá nhân này, nghĩa là gói không được ký GPG. Chỉ nên dùng cấu hình này cho repository do chính bạn kiểm soát hoặc bạn thực sự tin tưởng.
