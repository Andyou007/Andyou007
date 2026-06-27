// This file includes untranslated text (ja).

# Cheetsheet

- update_at: 2026-05

## setup

### Powershell

```powershell
# Excute Windows Powershell as Administrator

# Utility
winget update
winget upgrade winget
winget install gsudo bootandy.dust
winget upgrade --all
gsudo winget install Microsoft.PowerShell

# WSL
gsudo wsl --install --distribution Debian # select a distribution
gsudo wsl -d Debian
```

### Linux

```bash
# user と sudo password を作成

sudo apt update && sudo apt upgrade -y
mkdir -p w # make a working root (recommendation)

# purge when unuse cloud-init and vim
sudo apt purge cloud-init
sudo apt autoremove
sudo rm -rf /etc/cloud /var/lib/cloud
which nano && sudo apt purge vim-tiny vim
```

#### Utility

```bash
# ssh
ssh-keygen -t ed25519 # type "Enter" to continue
cat ~/.ssh/id_ed25519.pub # copy and paste to 3rd party inputs
chmod 600 ~/.ssh/id_ed25519

# tmux セッション永続化
sudo apt install tmux -y
tmux # detouch using Ctrl + b -> d & return using `tmux a`

# git
sudo apt update && sudo apt install git
git config --global user.name ""
git config --global user.email ""

# uv (Python library manager)
curl -LsSf https://astral.sh/uv/install.sh | sh

# claude code
curl -fsSL https://claude.ai/install.sh | bash

# build-essential
sudo apt install build-essential
```

#### Docker

- clean up: `sudo rm -rf /var/lib/docker/ /var/lib/containerd`
- [install](https://docs.docker.com/engine/install/debian)
- set up: `sudo usermod -aG docker $USER && newgrp docker && docker login -u "" -p ""`

note: containerd.io docker-ce docker-ce-cli docker-buildx-plugin for minimum install

## Utility

### apt

```bash
uname -a # linux kernel version
cat /etc/os-release # distribution version
sudo apt list --manual-installed
sudo apt autoremove

# apt
cat /etc/apt/sources.list # 追加済みリポジトリリスト
sudo rm -f /etc/apt/sources.list.d/{repogitory}.list # リポジトリ登録削除
```

### Git

```bash
# checkout aborting diffs
sudo chown 1000:1000 -R . && git clean -fd
```

### Docker

```bash
# requires at least 1 argument error when no container running
docker stop $(docker ps -q) && docker rm $(docker ps -aq)

docker system df             # disk usage
docker system prune          # delete unused 
sudo rm -rf /var/lib/docker/ # delete all
```

### national holidays (JP)

```bash
# "昭和30年（1955年）から令和9年（2027年）国民の祝日（csv形式：22KB）"
curl -o syukujitsu.csv "https://www8.cao.go.jp/chosei/shukujitsu/syukujitsu.csv"
iconv -f SHIFT-JIS -t UTF-8 syukujitsu.csv | awk -F, '$1 ~ /^2026/ {print $1 "\t" $2}' > national_holidays_2026.tsv
rm syukujitsu.csv
```

### Rust

```bash
# rustup: https://rust-lang.org/tools/install/
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh # WSL
# wasm-pack: https://wasm-bindgen.github.io/wasm-pack/installer/
curl https://wasm-bindgen.github.io/wasm-pack/installer/init.sh -sSf | sh
```

```toml
# Cargo.toml
[package]
name = ""
version = "0.1.0"
edition = "2024"
authors = [""]
description = ""
license = "Apache-2.0"

[lib]
crate-type = ["rlib", "cdylib"] # C dynamic library

[dependencies]
libc = { version = "0.2", default-features = false }

[features]
default    = []
```

```rust
// #![no_std]
extern crate core;
extern crate libc;
extern crate alloc;
extern crate std;
use core::{
    primitive::{
        u8, u16, u32, u64, u128, usize,
        i8, i16, i32, i64, i128, isize,
        f32, f64,
        bool,
        char, str,
    },
    fm,
    result,
    panic::Panicinfo,
    ptr::null_nut,
};
use alloc::{
    string::String,
    vec::Vec,
    boxed::Box,
    sync::Arc,
};
use std::alloc::System;
use log::debug;

#[cfg(feature = "nightly")]
use core::intrinsics::abort;

#[global_allocator]
static GLOBAL: System = System;

macro_rules! debug_log {
    ($($arg:tt)*) => {{
        #[cfg(feature = "logging")]
        { log::debug!($($arg)*); }
    }};
}

#[panic_handler]
fn panic(info: &PanicInfo) -> ! {
    debug_log!("panic: {}", info);
    #[cfg(feature = "nightly")]
    unsafe { abort() };
    loop {}
}
```
