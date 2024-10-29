### You can then upgrade NixOS to the latest version in your chosen channel by running

```bash
sudo nixos-rebuild switch --upgrade
```

### Rollbacks
```bash
nixos-rebuild switch --rollback
```

### Safe to test changes

```bash
nixos-rebuild test
```

### list all available generations
```bash
sudo nix-env -p /nix/var/nix/profiles/system --list-generations
```
#### delete all old generations
```bash
nix-collect-garbage  --delete-old
```

#### For specific generations (did not work)

```bash
nix-collect-garbage  --delete-generations 1 2 3
```

### recommeneded to sometimes run as sudo to collect additional garbage
```bash
sudo nix-collect-garbage -d
```

### As a separation of concerns - you will need to run this command to clean out boot
```bash
sudo /run/current-system/bin/switch-to-configuration boot
```

### unused packages can be deleted safely by running the garbage collector:

```bash
nix-collect-garbage
```
**This deletes all packages that aren’t in use by any user profile or by a currently running program.**

### remove unneeded parts of gnome: [apps list](https://wiki.postmarketos.org/wiki/GNOME_apps)
```nix
environment.gnome.excludePackages = with pkgs.gnome; [
baobab # disk usage analyzer
epiphany # web browser
simple-scan # document scanner
totem # video player
yelp # help viewer
evince # document viewer
geary # email clientw
pkgs.gedit # text editor
gnome-calculator
gnome-contacts
gnome-logs
gnome-maps
gnome-music
gnome-calendar
gnome-screenshot
gnome-system-monitor
pkgs.gnome-connections
pkgs.gnome-console
]; 
```
**OR**

```nix
{
  environment.gnome.excludePackages = (with pkgs; [
    # for packages that are pkgs.*
    gnome-tour
    gnome-connections
  ]) ++ (with pkgs.gnome; [
    # for packages that are pkgs.gnome.*
    epiphany # web browser
    geary # email reader
    evince # document viewer
  ]);
}
```

### Excluding applications from the default Plasma 6 install: [apps list](https://github.com/NixOS/nixpkgs/blob/nixos-unstable/nixos/modules/services/desktop-managers/plasma6.nix#L136-L149)

```nix
environment.plasma6.excludePackages = with pkgs.kdePackages; [
  plasma-browser-integration
  konsole
  elisa
];
```


### Running Unfree packages in nix shell
```nix
nixpkgs.config.allowUnfree = true;
``` 
```bash
mkdir -p ~/.config/nixpkgs
```
```bash
echo “{ allowUnfree = true; }” >> ~/.config/nixpkgs/config.nix
```

### LocalSend
you need to open the port to send and receive files
```nix
# Open ports in the firewall.
  networking.firewall.allowedTCPPorts = [ 53317 ];
  networking.firewall.allowedUDPPorts = [ 53317 ];
```

### Keyboard Layout Customization

```nix
 # Configure keymap in X11
  services.xserver.xkb = {
    layout = "us,ara";
    variant = ",";
  };
```
and restart

Don't forget to set shortcut for switching layouts in the settings

or you can add **(NOT Working)**
```nix
xkbOptions = "grp:alt_shift_toggle";  # Alt + Shift
xkbOptions = "grp:win_space_toggle";  # Win + Space
```