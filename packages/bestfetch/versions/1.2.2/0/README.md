# bestfetch

![GitLab Last Commit](https://img.shields.io/gitlab/last-commit/Maxb0tbeep%2Fbestfetch?logo=git)
![Gitlab Pipeline Status](https://img.shields.io/gitlab/pipeline-status/Maxb0tbeep%2Fbestfetch?logo=gitlab)
![AUR Version](https://img.shields.io/aur/version/bestfetch?logo=archlinux)
![AUR -git Version](https://img.shields.io/badge/latest-blue?label=aur-git&logo=archlinux)

a customizable, beautiful, and blazing fast system fetch, powered by nim

## Installing

### Arch Linux

bestfetch is available on the AUR as [bestfetch](https://aur.archlinux.org/packages/bestfetch) (recommended), or [bestfetch-git](https://aur.archlinux.org/packages/bestfetch-git)

### Building manually

1. make sure nim (>2.0.8) is installed 
2. clone this repository and enter the directory
3. `$ nimble install -y`
4. `$ nimble build`
5. the compiled binary will be located at `./build/bestfetch`

## Help

### Arguments

```
Non-Parameter Arguments:
--help, -h          print this dialogue
--version, -v       print the version number
--clear, -c         clear the terminal before printing the fetch
--reset, -R         reset the configuration file to the default

Boolean Arguments:
--icons, -i         (true/false) enable icons instead of labels
--box, -b           (true/false) draw a box around the fetch
--boxLogo, -B       (true/false) draw a box around the logo (requires box to be true)
--roundCorners, -r  (true/false) use round corners (╭) instead of right-angle (┌) corners
--login, -L         (true/false) print your login (user@hostname) in the fetch
--gibibytes, -g     (true/false) round bytes to 1024 instead of 1000 to be more accurate

String Arguments:
--logo, -l          (string) which logo to draw (see all in config file)
--colorSymbol, -C   (string) what to print the colors as (● is the default)
--titleColor, -tc   (string) what color to print the titles as (see all colors in config file)
--dataColor, -dc    (string) what color to print the data as (see all colors in config file)
--otherColor, -oc   (string) what color to print other stuff as (see all colors in config file)
```

### Config

The configuration file for bestfetch is stored at `~/.config/bestfetch/config.yaml` and contains the same string and boolean options shown in the arguments above

## Thanks
- [neofetch](https://github.com/dylanaraps/neofetch) - for starting it all for me
- [fastfetch](https://github.com/fastfetch-cli/fastfetch) - for being neofetch but good
- [nitch](https://github.com/ssleert/nitch) - for inspiring me to learn nim, and making me love nerd fonts & boxes
- [pfetch](https://github.com/dylanaraps/pfetch/) - for inspiring some ascii logos (however, none were used directly)

---

![bestfetch](bestfetch-small.png)