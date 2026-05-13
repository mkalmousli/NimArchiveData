# bestfetch

![Static Badge](https://img.shields.io/badge/%3E2.0.8-yellow?logo=nim&label=nim)
![GitLab Last Commit](https://img.shields.io/gitlab/last-commit/Maxb0tbeep%2Fbestfetch?logo=git)
![Gitlab Pipeline Status](https://img.shields.io/gitlab/pipeline-status/Maxb0tbeep%2Fbestfetch?logo=gitlab)
![AUR License](https://img.shields.io/aur/license/bestfetch?logo=gnu)

![AUR Version](https://img.shields.io/aur/version/bestfetch?logo=archlinux&link=https%3A%2F%2Faur.archlinux.org%2Fpackages%2Fbestfetch)
![AUR -git Version](https://img.shields.io/badge/latest-blue?label=aur-git&logo=archlinux&link=https%3A%2F%2Faur.archlinux.org%2Fpackages%2Fbestfetch-git)
![Static Badge](https://img.shields.io/badge/nimble%20package%20-%20black?logo=nim&link=https%3A%2F%2Fnimble.directory%2Fpkg%2Fbestfetch)

a customizable, beautiful, and blazing fast system fetch, powered by nim

![fetches](media/fetches/fetch.png)

*more distros and configuration options are supported!

<br> 

*Did I mention it's fast?*

| fetch          | execution time |
|----------------|----------------|
| pfetch         | ~362.4 ms      |
| neofetch       | ~354.8 ms      |
| fastfetch      | ~12.7 ms       |
| **bestfetch**  | ~12.4 ms       |

*mean value of 5 samples per fetch, on the computer shown on the fetches above

## Installing

- You should install bestfetch with your distro's package repository if possible
- If you use a distro that isn't listed here, you can install it with nimble
- You probably don't need to manually build it

### Arch Linux

bestfetch is available on the AUR as [bestfetch](https://aur.archlinux.org/packages/bestfetch) (recommended), or [bestfetch-git](https://aur.archlinux.org/packages/bestfetch-git)

If you use an AUR helper such as [paru](https://aur.archlinux.org/packages/paru), you can run
`$ paru -S bestfetch`

### Install with nimble

bestfetch can alternatively be installed using the nim package manager (nimble)

1. make sure nim (>2.0.8) is installed
2. `$ nimble install bestfetch`
3. make sure `~/.nimble/bin` is added to your `$PATH`

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

![bestfetch](media/distros/bestfetch.png)
![arch](media/distros/arch.png)
![debian](media/distros/debian.png)
![fedora](media/distros/fedora.png)
![ubuntu](media/distros/ubuntu.png)
![nixos](media/distros/nixos.png)
![endeavouros](media/distros/endeavouros.png)

All distro ascii art was made by me. You are allowed to use them in your own projects if you give credit.