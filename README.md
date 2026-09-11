# aur

AUR 专属维护仓库 — 收录 `python-chinese-calendar` 与 `Vantage` 浏览器三形态

[![License: MPL-2.0](https://img.shields.io/badge/许可证-MPL--2.0-green?style=flat-square)](https://github.com/asystech-chen/Vantage/blob/main/LICENSE)
[![AUR: python-chinese-calendar](https://img.shields.io/badge/AUR-python--chinese--calendar-blue?style=flat-square)](https://aur.archlinux.org/packages/python-chinese-calendar)
[![AUR: vantage-browser](https://img.shields.io/badge/AUR-vantage--browser-blue?style=flat-square)](https://aur.archlinux.org/packages/vantage-browser)

> `curfew` 仍由 [aura-deak/Curfew](https://github.com/aura-deak/Curfew) 主仓库维护，不在本仓库。

---

## 📦 包列表

| 包名 | 上游 | 版本跟随 | 安装方式 | 适用场景 |
|------|------|----------|----------|----------|
| `python-chinese-calendar` | [LKI/chinese-calendar](https://github.com/LKI/chinese-calendar) / [PyPI](https://pypi.org/project/chinese-calendar/) | `info.version` (`2004–2026` 含春节延长，约每年 11 月随国务院发版) | `pip` 源码构建（`python-build`） | 判断某日是否工作日/节假日、获取节日名、调休判断 |
| `vantage-browser` | [asystech-chen/Vantage](https://github.com/asystech-chen/Vantage) | `v153.2.0-1` → `pkgver=153.2.0 pkgrel=1` | 编译安装（`git+tag`） | 需自定义编译参数/审计源码 |
| `vantage-browser-bin` | 同上 | 同上 + `SHA256SUMS` | `deb` 解包（`bsdtar -xf data.tar.xz`） | 开箱即用，启动最快（推荐） |
| `vantage-browser-appimage` | 同上 | 同上 + `SHA256SUMS` | `AppImage` | 免安装、便携/多版本共存 |

三 `vantage` 包 `provides/conflicts=vantage-browser`，三选一。

---

## 🔍 上游介绍（摘录/总结）

### Vantage 浏览器 — 隐私 · 快速 · 易用

> 基于 **Firefox ESR 153**（LibreWolf 代码实现），由 **ASYS 科技** 深度定制，与 Mozilla/LibreWolf 无隶属关系。跟随 ESR 维护版持续更新，下一大版本约 2027 年。

**核心特性（摘自上游 README）**

- **多架构原生**：`x86_64` / `aarch64` / `LoongArch64`（龙芯 3A6000+），覆盖 Linux / Windows / 龙芯
- **AI 侧栏**：内置 DeepSeek / 通义千问 / 豆包，侧栏即开即用
- **交互优化**：恢复最近关闭按钮、标签页滚轮切换、双击关闭、内存不足自动休眠不活跃标签页（Linux）
- **隐私与安全**：禁用遥测/实验/广告，RFP 指纹保护、WebRTC 防泄露、强制 HTTPS；默认 DoH（阿里/腾讯，故障回退系统 DNS）；预装 `uBlock Origin`
- **易用定制**：`about:preferences#vantage` 专属面板；默认 Bing 可一键切 百度/谷歌/DuckDuckGo；可选 Mozilla 账号同步；MPL-2.0 开源

**系统要求（Linux）**

- `x86_64`：Debian/Ubuntu/Fedora/Arch 等主流发行版
- `aarch64`：树莓派、国产 ARM
- `LoongArch64`：Debian Ports / Arch Linux Loong
- 依赖：`gtk3` `nss` `alsa-lib` `dbus-glib` 等（本 AUR 已声明，`bin/appimage` 自带常见依赖）

**上游安装方式对比**

```bash
# 上游官方（非 AUR）
sudo apt install ./vantage_153.2.0-1_amd64.deb
sudo dnf install ./vantage-153.2.0-1.x86_64.rpm
./vantage-153.2.0-1.x86_64.AppImage
# 本 AUR（Arch）
paru -S vantage-browser-bin        # 二进制
paru -S vantage-browser            # 编译
paru -S vantage-browser-appimage   # AppImage
```

> 详情见上游 [README](https://github.com/asystech-chen/Vantage#readme)、[CHANGELOG](https://github.com/asystech-chen/Vantage/blob/main/CHANGELOG.md)（如 `v153.2.0-1` 基于 `ESR 153.2` 含 MFSA 2026-85 安全修复）。

### python-chinese-calendar — 中国节假日

> 判断某年某月某日是否工作日/节假日，支持 `2004–2026`（含 2020 春节延长），依国务院安排每年 11 月前后发新版。

```python
import datetime
from chinese_calendar import is_holiday, is_workday
assert is_holiday(datetime.date(2018,4,30)) is True
import chinese_calendar as cal
on_holiday, name = cal.get_holiday_detail(datetime.date(2018,4,30))
# name == cal.Holiday.labour_day.value
assert cal.is_in_lieu(datetime.date(2006,2,2)) is True
```

亦可转译 [`constants.py`](https://github.com/LKI/chinese-calendar/blob/master/chinese_calendar/constants.py) 供非 Python 使用。

---

## 🛠 AUR 安装

```bash
# 推荐 bin（预编译）
paru -S vantage-browser-bin
# 或编译（耗时 1–3 小时，需 rust/clang/llvm 等，参考 PKGBUILD makedepends）
paru -S vantage-browser
# 或 AppImage
paru -S vantage-browser-appimage

paru -S python-chinese-calendar

# 启动
vantage          # 三包均提供 /usr/bin/vantage
```

---

## 🤖 自动化

` .github/workflows/aur-sync.yml` 每日 **10:00 CST (02:00 UTC)** 自动运行，可 `workflow_dispatch` 手动触发：

1. **python-chinese-calendar**：`https://pypi.org/pypi/chinese-calendar/json → info.version` 对比 `pkgver`，`sed` 更新 `pkgver/pkgrel/source/sha256sums`
2. **Vantage**：`https://api.github.com/repos/asystech-chen/Vantage/releases/latest → tag_name`（如 `v153.2.0-1`）拆 `pkgver/pkgrel`，对比 `vantage-browser/PKGBUILD`：
   - 三包同步 `pkgver/pkgrel`；`vantage-browser` 为 `git+tag`（`SKIP`）
   - `bin`/`appimage` 从 `https://github.com/.../releases/download/$TAG/SHA256SUMS`（上游 `make checksum` 生成，`release.yml` 以此为清单）提取 `sha256sums_x86_64/aarch64`，回退则下载计算
3. `archlinux:latest` 容器（或本地 `makepkg`）`makepkg --printsrcinfo > .SRCINFO`
4. `git commit && git push` 回本仓库，并 `ssh://aur@aur.archlinux.org/<pkg>.git` 分别推送

参考上游打包：`build.sh`（`./build.sh linux-x64`）/ `Makefile:package,checksum` / `release.yml` / `deploy-repo.yml`（仅 deb/rpm，不影响 AUR）。失败时 `check-updates-notify.yml` 自动建 Issue。

```bash
# 本地模拟
curl -s https://pypi.org/pypi/chinese-calendar/json | jq .info.version
curl -s https://api.github.com/repos/asystech-chen/Vantage/releases/latest | jq -r .tag_name
curl -sL https://github.com/asystech-chen/Vantage/releases/download/v153.2.0-1/SHA256SUMS | head
```

---

## 🔐 AUR 推送

已启用 `Push to AUR`，`AUR_SSH_PRIVATE_KEY` 已写入 `Settings → Secrets`（本机 `~/.ssh/id_ed25519`，`aur@aur.archlinux.org` 验证 `Welcome to AUR, chen-shuhan!`）。首次上架即使无版本变更也会通过 `AUR RPC` 探测补推。

---

## 📁 结构

```
aur/
  python-chinese-calendar/PKGBUILD / .SRCINFO
  vantage-browser/PKGBUILD / .SRCINFO               # 编译  (git tag, 参考 build.sh/Makefile)
  vantage-browser-bin/PKGBUILD / .SRCINFO           # 二进制 (deb 解包, 参考 SHA256SUMS)
  vantage-browser-appimage/PKGBUILD / .SRCINFO      # AppImage
  .github/workflows/aur-sync.yml
  .github/workflows/check-updates-notify.yml
```

许可证：`python-chinese-calendar` MIT，`vantage` MPL-2.0，详见各上游。
