Welcome to the llama.cpp wiki!

## Install

### [ArchLinux](https://aur.archlinux.org/packages/llama-cpp)

```sh
yay -S llama-cpp
yay -S llama-cpp-cuda
yay -S llama-cpp-opencl
```

### Nix

```sh
nix run github:ggerganov/llama.cpp
nix run 'github:ggerganov/llama.cpp#opencl'
```

### NixOS

```nix
{ config, pkgs, ... }:
{
  nixpkgs.config.packageOverrides = pkgs: {
      llama-cpp = (
        builtins.getFlake "github:ggerganov/llama.cpp"
      ).packages.${builtins.currentSystem}.default;
    };
  };
  environment.systemPackages = with pkgs; [ llama-cpp ]
}
```

### Android Termux

Wait <https://github.com/termux/termux-packages/pull/17457>.

```sh
apt install llama-cpp
```

### Windows Msys2

```sh
pacman -S llama-cpp
```

### Debian (Ubuntu)

```sh
git clone --depth=1 https://github.com/ggerganov/llama.cpp
cd llama.cpp
cmake -Bbuild
cmake --build build -D...
cd build
cpack -G DEB
dpkg -i *.deb
```

### Redhat

```sh
git clone --depth=1 https://github.com/ggerganov/llama.cpp
cd llama.cpp
cmake -Bbuild
cmake --build build -D...
cd build
cpack -G RPM
rpm -i *.rpm
```