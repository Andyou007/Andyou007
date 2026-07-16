// This file includes untranslated text (ja).

# Cheetsheet

- update_at: 2026-07

## Powershell

```powershell
# Excute Windows Powershell as Administrator

winget update && winget upgrade winget
winget install gsudo bootandy.dust
gsudo winget install Microsoft.PowerShell
gsudo winget update && gsudo winget upgrade --all

# add WSL
wsl --list --verbose
wsl --install --distribution Debian
wsl -d Debian
```

- Select Linux Distribution:

| Image | Update cycle |
|-|-|
| Debian stable slim | 2 years |
| Ubuntu non-LTS | 6 months |

```bash
# update distribution
sudo apt update && sudo apt upgrade -y
sudo apt dist-upgrade -y
sudo do-release-upgrade
```

## Apt

```bash
# --- apt ---
sudo apt update && sudo apt upgrade -y
# exclude recommend dependencies when apt install
echo 'APT::Install-Recommends "false";' | sudo tee /etc/apt/apt.conf.d/99no-recommends

mkdir -p w # make a working root

# purge when unuse cloud-init and vim
sudo apt purge cloud-init
sudo apt autoremove
sudo rm -rf /etc/cloud /var/lib/cloud
which nano && sudo apt purge vim-tiny vim

# get informations
uname -a # linux kernel version
cat /etc/os-release # distribution version

sudo apt list --manual-installed

cat /etc/apt/sources.list
sudo rm -f /etc/apt/sources.list.d/{repogitory}.list

# --- ssh ---
sudo apt install ssh
ssh-keygen -t ed25519 # type "Enter" to continue
cat ~/.ssh/id_ed25519.pub # copy and paste to 3rd party inputs
chmod 600 ~/.ssh/id_ed25519

# --- tmux ---
sudo apt install tmux -y
tmux # detouch using Ctrl + b -> d & return using `tmux a`

# --- git ---
sudo apt update && sudo apt install git
git config --global user.name ""
git config --global user.email ""
git diff --numstat branch1 branch2 # to confirm branches diff counts

# checkout aborting diffs
sudo chown 1000:1000 -R . && git clean -fd

# --- uv (Python library manager) ---
curl -LsSf https://astral.sh/uv/install.sh | sh

# --- claude code ---
curl -fsSL https://claude.ai/install.sh | bash

# --- build-essential ---
sudo apt install build-essential

# --- Docker ---
# requires at least 1 argument error when no container running
docker stop $(docker ps -q) && docker rm $(docker ps -aq)

docker system df             # disk usage
docker system prune          # delete unused 
sudo rm -rf /var/lib/docker/ # delete all
```

## Docker

- clean up: `sudo rm -rf /var/lib/docker/ /var/lib/containerd`
- [install](https://docs.docker.com/engine/install/debian)
- set up: `sudo usermod -aG docker $USER && newgrp docker && docker login -u "" -p ""`

note: containerd.io docker-ce docker-ce-cli docker-buildx-plugin for minimum install

```powershell
# optimize vhdx
Optimize-VDisk -Path "Local\Docker\wsl\ext4.vhdx" -Mode Full
gsudo pwsh -NoProfile -Command "Optimize-VHD -Path 'C:AppData\Local\wsl\{token}\ext4.vhdx' -Mode Full"
```

## national holidays (JP)

```bash
# "昭和30年（1955年）から令和9年（2027年）国民の祝日（csv形式：22KB）"
curl -o syukujitsu.csv "https://www8.cao.go.jp/chosei/shukujitsu/syukujitsu.csv"
iconv -f SHIFT-JIS -t UTF-8 syukujitsu.csv | awk -F, '$1 ~ /^2026/ {print $1 "\t" $2}' > national_holidays_2026.tsv
rm syukujitsu.csv
```

## Rust

- [rustup: managemer](https://rust-lang.org/tools/install/)
- [cargo docs: toml specs](https://doc.rust-lang.org/cargo/reference/manifest.html)
- [wasm-pack: wasm builder](https://wasm-bindgen.github.io/wasm-pack/installer/)

## Github Actions

- [Github: workflow syntax](https://docs.github.com/ja/actions/reference/workflows-and-actions/workflow-syntax)
- [Github: runners' spec](https://github.com/actions/runner-images)

```yaml
name: "workflow name"

on: # 実行条件
    workflow_dispatch: # manual excution
    release:
        types: [published] # when released
    push:
        tags:
            - "*.*.*" # タグ作成時実行 (リリース時実行と重複するのでどちらか選択)
        branches:
            - "main"

permissions: # 実行時の付与権限
    contents: "read"
    actions: "write" # github artifactの保存など

env: # 環境変数 (run: 内で$付きキー名で扱う定数)
    ENV_VAR:   ${{vars.ENV_VAR}} # Repository/Organization/Environment単位で設定可能
    SECRET_KEY: ${{secrets.SECRET_KEY}} # 機密情報用マスクフィールド

jobs:
    deploy: # 任意のjob名
        timeout-minutes: 30 # stepsごとにも設定可能
        runs-on: "ubuntu-latest" # 選ばれるCPUアーキテクチャはdocsで都度要確認
        environment:
            name: github-pages # Github Pages利用時のみ
            url: ${{ steps.deployment.outputs.page_url }}
        # needs, if, stratergy: jobs全体で実行制御可能。
        steps:
            - name: "git checkout" # 省略可
              id: "git_checkout" # 省略可。外部stepで出力を取得したい場合に使う
              uses: actions/checkout@v7

            - name: "upload artifact"
              # [Github: @actions/upload-artifact]にて現行メジャーバージョンを要確認・更新
              uses: actions/upload-artifact@v7
              with:
                name: "artifact_name"
                path: "./examples/target/"
                retention-days: 7

            - name: "setup Github Pages"
              uses: actions/configure-pages@v6

            - name: "upload artifact as Github Pages"
              uses: actions/upload-pages-artifact@v5
              with:
                path: "./examples"

            - name: "deploy to GitHub Pages"
              id: "deployment"
              uses: actions/deploy-pages@v5
```

## Github Dependabot

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/" # .github/workflows/*.yml
    schedule:
      interval: "weekly"
  - package-ecosystem: "cargo"
    directory: "/" # ./
    schedule:
      interval: "weekly"
```
