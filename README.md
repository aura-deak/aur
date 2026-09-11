# aur

AUR 专属维护仓库 — `curfew` 与 `python-chinese-calendar`

## 包列表

| 包名 | 上游 | 检测方式 |
|------|------|----------|
| `curfew` | [aura-deak/Curfew](https://github.com/aura-deak/Curfew) | GitHub Releases latest `tag_name` (去 `v` 前缀) |
| `python-chinese-calendar` | [PyPI chinese-calendar](https://pypi.org/project/chinese-calendar/) / [LKI/chinese-calendar](https://github.com/LKI/chinese-calendar) | PyPI JSON `info.version` + sdist URL |

## 自动化

` .github/workflows/aur-sync.yml` 每日 **10:00 CST (02:00 UTC)** 自动运行，也可手动触发 `workflow_dispatch`：

1. 查询上游最新版本，对比 `PKGBUILD` 中 `pkgver`
2. 若有更新：下载源码包计算 `sha256sums`，`sed` 更新 `pkgver`/`pkgrel=1`/`sha256sums`（`python-chinese-calendar` 同时更新 `source` 为 PyPI sdist 真实 URL）
3. 使用 `archlinux:latest` 容器执行 `makepkg --printsrcinfo` 重新生成 `.SRCINFO`
4. `git commit && git push` 回本仓库

失败时 `check-updates-notify.yml` 会自动创建 Issue 通知。

### 手动触发

GitHub 页面：Actions → **AUR Daily Sync** → Run workflow

或本地模拟：

```bash
curl -s https://pypi.org/pypi/chinese-calendar/json | jq .info.version
curl -s https://api.github.com/repos/aura-deak/Curfew/releases/latest | jq -r .tag_name
```

## AUR 发布（可选）

本仓库默认只更新 GitHub 上的 PKGBUILD。如需同步推送到 `aur.archlinux.org`，在仓库 **Settings > Secrets** 添加：

- `AUR_SSH_PRIVATE_KEY` — AUR 账户的 SSH 私钥
- `AUR_USERNAME` — 可选，用于日志

然后取消 `aur-sync.yml` 底部 `Push to AUR` 段的注释。

## 本地维护结构

```
aur/
  curfew/PKGBUILD / .SRCINFO
  python-chinese-calendar/PKGBUILD / .SRCINFO
  .github/workflows/aur-sync.yml
```

与主仓库 `aura-deak/Curfew` 的 `aur/` 目录保持一致，更新后可直接 `cp` 同步或通过 CI 自动提 PR。
