# ultimate guide video repo

## search for packages

https://search.nixos.org/packages

## initializing flake
```bash
$ cd /etc/nixos
$ sudo nix flake init --template github:vimjoyer/flake-starter-config
```

## rebuilding with flakes enabled

```bash
$ sudo nixos-rebuild switch --flake /etc/nixos/#default
```

## generating home.nix
```bash
$ nix run home-manager/master -- init && \\
  sudo cp ~/.config/home-manager/home.nix /etc/nixos/
```

## home-manager option
```nix
home-manager = {
  # also pass inputs to home-manager modules
  extraSpecialArgs = {inherit inputs;};
  users = {
    "username" = import ./home.nix;
  };
}
```

## example module / main-user.nix
```nix
{ lib, config, pkgs, ... }:

let
  cfg = config.main-user;
in
{
  options.main-user = {
    enable 
      = lib.mkEnableOption "enable user module";

    userName = lib.mkOption {
      default = "mainuser";
      description = ''
        username
      '';
    };
  };

  config = lib.mkIf cfg.enable {
    users.users.${cfg.userName} = {
      isNormalUser = true;
      initialPassword = "12345";
      description = "main user";
      shell = pkgs.zsh;
    };
  };
}
```

## example project structure

```
 flake.nix

 flake.lock

 modules/

   nixos/
    
     nvidia.nix

   home-manager/

     terminals/
      
       default.nix

       kitty.nix

       alacritty.nix

 hosts/

   default/

     configuration.nix

     hardware-configuration.nix

     home.nix
```
