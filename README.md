# aur

AUR 专属维护仓库

## 包列表

| 包名 | 上游 | 检测方式 | 说明 |
|------|------|----------|------|
| `python-chinese-calendar` | [PyPI chinese-calendar](https://pypi.org/project/chinese-calendar/) / [LKI/chinese-calendar](https://github.com/LKI/chinese-calendar) | PyPI JSON `info.version` |  |
| `vantage-browser` | [asystech-chen/Vantage](https://github.com/asystech-chen/Vantage) | GitHub Releases `tag_name` (`v153.2.0-1` → `pkgver/pkgrel`) | 编译安装：参考上游 `build.sh` / `Makefile` (`make dir` + `MOZCONFIG=... make build` + `make package`) |
| `vantage-browser-bin` | 同上 | 同上 + `SHA256SUMS` | deb 解包安装：`vantage_${ver}_${arch}.deb` → `bsdtar -xf data.tar.xz` |
| `vantage-browser-appimage` | 同上 | 同上 + `SHA256SUMS` | AppImage：`vantage-${ver}.x86_64.AppImage` / `aarch64.AppImage` |


## 自动化

` .github/workflows/aur-sync.yml` 每日 **10:00 CST (02:00 UTC)** 自动运行，也可手动触发 `workflow_dispatch`：

1. **python-chinese-calendar**：查询 PyPI 最新版本，对比 `PKGBUILD` 中 `pkgver`，`sed` 更新 `pkgver`/`pkgrel`/`source`/`sha256sums`
2. **Vantage**：查询 GitHub Releases 最新 `tag`（如 `v153.2.0-1`），拆分 `pkgver/pkgrel`，对比 `vantage-browser/PKGBUILD`，若有更新：
   - 三包同步更新 `pkgver/pkgrel`
   - `vantage-browser-bin` / `vantage-browser-appimage` 从 `SHA256SUMS`（上游 `make checksum` 生成，`release.yml` 以此为清单）提取对应哈希，回退则下载计算，回填 `sha256sums_x86_64` / `sha256sums_aarch64`
   - `vantage-browser` 为 `git+https` 源码，`sha256sums=SKIP`
3. 使用 `archlinux:latest` 容器（或本地 `makepkg`）执行 `makepkg --printsrcinfo` 重新生成 `.SRCINFO`
4. `git commit && git push` 回本仓库，并通过 SSH 分别推送至 `aur.archlinux.org/<pkg>.git`（`python-chinese-calendar`, `vantage-browser*`）

参考上游打包逻辑：
- `build.sh` 多平台编译 / `Makefile:package` / `checksum` / `release.yml` 以 `SHA256SUMS` 为权威清单
- `deploy-repo.yml` 仅处理 deb/rpm，不影响 AUR

失败时 `check-updates-notify.yml` 会自动创建 Issue 通知。

### 手动触发

GitHub 页面：Actions → **AUR Daily Sync** → Run workflow
