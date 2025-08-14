# Build OpenWrt on Mac Apple Silicon (ARM64)

![GitHub](https://img.shields.io/badge/license-MIT-blue.svg) ![GitHub last commit](https://img.shields.io/github/last-commit/hhai93/Build-Openwrt-With-Mac-Apple-Silicon)

## 1. Create Ubuntu VM with OrbStack
+ Install [OrbStack](https://orbstack.dev/download/stable/latest/arm64) or using Homebrew:
```bash
brew install orbstack
```
+ Create a new Ubuntu VM 24.04 with APPLE CPU (Should be at least 8GB of RAM to prevent OOM)
+ Run Terminal within the VM
---

## 2. Prepare the VM

### Install dependencies
```bash
sudo apt update -y && sudo apt full-upgrade -y && \
sudo apt install -y \
ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential \
bzip2 ccache clang clangd cmake cpio curl device-tree-compiler ecj fastjar flex gawk gettext \
git golang-go gperf haveged help2man intltool libelf-dev libglib2.0-dev \
libgmp3-dev libltdl-dev libmpc-dev libmpfr-dev libncurses-dev libreadline-dev \
libssl-dev libtool lld lldb lrzsz genisoimage msmtp nano ninja-build p7zip p7zip-full patch pkgconf \
python3 python3-pip python3-ply python3-docutils qemu-utils re2c rsync scons squashfs-tools subversion swig \
texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev
```
You can choose 'yes' or 'no' when AppArmor check appears.

*For x86/64 target:*
```bash
sudo apt install -y \
gcc-multilib-i686-linux-gnu gcc-multilib-s390x-linux-gnu gcc-multilib-x86-64-linux-gnu gcc-multilib-x86-64-linux-gnux32 \
g++-multilib-i686-linux-gnu g++-multilib-s390x-linux-gnu g++-multilib-x86-64-linux-gnu g++-multilib-x86-64-linux-gnux32 \
libc6-dev-i386-amd64-cross libc6-dev-i386-cross libc6-dev-i386-x32-cross
```
### Check Go GOROOT path (needed for bootstrap fix)
```bash
go env GOROOT
```
Example: `/usr/lib/go-1.22`
**Use this path later**

---

## 3. Get OpenWrt source & sync feeds
```bash
git clone https://github.com/openwrt/openwrt.git
cd openwrt
git checkout v24.10.2   # or other stable branch/tag
./scripts/feeds update -a
./scripts/feeds install -a
```

---

## 4. Configure build
```bash
make menuconfig
```
- **Target System** → select your SoC
- **Target Profile** → select your router model
- **Languages → Go → golang Configuration → External bootstrap Go root directory** = *`<PATH>` from `go env GOROOT`*
- Others: Customize to your needs
---

## 5. Build
```bash
make download -j$(nproc) && make -j$(nproc) V=s
```
Output firmware is in:
```
bin/targets/<target>/<subtarget>/
```

*The process will take a long time, so make sure your Mac does not go to sleep by opening a new Terminal window and running `caffeinate` command
