# 我的設定檔收藏庫
## \[NixOS\] Easy Cloudflare Tunnel config with Dashboard (systemd service)
https://stackoverflow.com/questions/78691817/how-to-enable-cloudflared-on-nixos-using-configuration-nix

```nix
# /etc/nixos/cloudflared.nix
{ config, lib, pkgs, ... }:
{
  environment.systemPackages = [ pkgs.cloudflared ];
  users.groups.cloudflared = { };
  users.users.cloudflared = {
    group = "cloudflared";
    isSystemUser = true;
  };
  systemd.services.my_tunnel = {
    wantedBy = [ "multi-user.target" ];
    after = [ "network-online.target" "systemd-resolved.service" ];
    serviceConfig = {
      ExecStart = "${pkgs.cloudflared}/bin/cloudflared tunnel --no-autoupdate run --token=<myToken>";
      Restart = "always";
      User = "cloudflared";
      Group = "cloudflared";
    };
  };
}
```

```nix
# /etc/nixos/configuration.nix
{
  ...
  imports = [ 
    ./hardware-configuration.nix
    ./cloudflared.nix # add this line
  ];
  ...
}
```

```sh
sudo nixos-rebuild switch
```
