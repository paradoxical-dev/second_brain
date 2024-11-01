# Undeclared Programs

## Ollama

- The configuration of the service is included in configuration.nix but the initial bootstrap of the program needs to be done from the install link

> [!NOTE]
>
> Ollama needs to be installed from nix packages. I was wrong and this is the only way to support the latest versions.
>
> This repo is still a little behind the latest version, only supporting 3.12 while 3.14 is out, so I may need to look into manually upgrading at some point.

## Neovim

- Neovim is installed using home-manager and set to the default editor within it however, the configuration of neovim is still held within the .config/nvim

## Hyprland

- Hyprland is also included in the configuration.nix file but is configured from the default .config/hypr
- Same for waybar, hyprpaper, etc.

## Starship

- The initialization and installation is handled declaritively however the configuration is still stored in the starship.toml
