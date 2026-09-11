# aur

AUR 专属维护仓库 — `python-chinese-calendar`（`curfew` 仍由 [aura-deak/Curfew](https://github.com/aura-deak/Curfew) 主仓库维护）

## 包列表

| 包名 | 上游 | 检测方式 |
|------|------|----------|
| `python-chinese-calendar` | [PyPI chinese-calendar](https://pypi.org/project/chinese-calendar/) / [LKI/chinese-calendar](https://github.com/LKI/chinese-calendar) | PyPI JSON `info.version` + sdist URL |

## 自动化

` .github/workflows/aur-sync.yml` 每日 **10:00 CST (02:00 UTC)** 自动运行，也可手动触发 `workflow_dispatch`：

1. 查询 PyPI 最新版本，对比 `PKGBUILD` 中 `pkgver`
2. 若有更新：获取 sdist 真实 URL，`sed` 更新 `pkgver`/`pkgrel=1`/`source`/`sha256sums`
3. 使用 `archlinux:latest` 容器执行 `makepkg --printsrcinfo` 重新生成 `.SRCINFO`
4. `git commit && git push` 回本仓库，并通过 SSH 推送至 `aur.archlinux.org/python-chinese-calendar.git`

失败时 `check-updates-notify.yml` 会自动创建 Issue 通知。

### 手动触发

GitHub 页面：Actions → **AUR Daily Sync** → Run workflow

或本地模拟：

```bash
curl -s https://pypi.org/pypi/chinese-calendar/json | jq .info.version
```

## AUR 推送

已启用 `Push to AUR` 步骤，需仓库 **Settings > Secrets** 配置 `AUR_SSH_PRIVATE_KEY`（本机 `~/.ssh/id_ed25519` 已自动写入）。

## 本地维护结构

```
aur/
  python-chinese-calendar/PKGBUILD / .SRCINFO
  .github/workflows/aur-sync.yml
```
