# Norg
A simple, portable, wrapper for the [borg backup utility](https://www.borgbackup.org) written in Nim  

<!--more-->
Inspired by [Borgmatic](https://torsion.org/borgmatic)

## Usage
Norg uses a `toml` based config file for configuration. An example configuration would look like this:
```toml
source_directories = [
    "/home/me/Music",
    "/home/me/Pictures"
]
[[repositories]]
label = "A Repository"
path = "/my/backup/location"

[[repositories]]
label = "Another Respository at BorgBase"
path = "ssh://1234abcd@1234abcd.repo.borgbase.com/./repo"

[encryption]
encryption_passphrase = "MyReallySecurePassword"

[actions]
before_actions = ["echo before actions"]
after_actions = ["echo after actions", "echo actions completed"]
before_backup = ["echo before backup", "date"]
after_backup = ["echo after backup","echo backup completed"]

[uptimekuma]
base_url = "https://uptime.kuma.url/api/push/1234abcd"
states = ["Success","Failure", "Running"]
```

You can then run the equivalent `borg` command to init, create, list, mount and extract your backups.

```sh
# Init your repository
norg -c myconfig.toml init

# Backup your data
norg -c myconfig.toml create

# List Archives
norg -c myconfig.toml list

# Mount an Archive
norg -c myconfig.toml mount pcname-2024-08-18T15:20:17773204 /home/me/mnt
# Unmount an Archive
norg -c myconfig.toml umount /home/me/mnt

# Extract an Archive
# You must be in an empty folder for this to work
norg -c myconfig.toml extract pcname-2024-08-18T15:20:17773204
 
# Or You must set the destination to an empty folder 
norg -c myconfig.toml extract pcname-2024-08-18T15:20:17773204 --destination /tmp/my_extracted_archive
```

# Build from Source
Download and build from source
```sh
git clone https://codeberg.org/pswilde/norgbackup
cd norgbackup
nimble install
```

or just install directly with `nimble` 

```sh
nimble install https://codeberg.org/pswilde/norgbackup
```


## Naming. Why "Norg"?
Well, I don't know. I'm a Star Trek fan so obviously I wanted to keep something 
in line with the [Borg pseudo-species](https://memory-alpha.fandom.com/wiki/Borg) as the borg backup utility does.  
Also, sometimes I feel my code has elements of inexperience but loads of potential... which reminded me of [Nog](https://memory-alpha.fandom.com/wiki/Nog).  
So, simply put, `Norg` is an portmanteau of "Borg" and "Nog".

## Why create this when Borgmatic exists?
`Borgmatic` is absolutely fantastic, and I love it dearly. I even implemented 
the `Uptime Kuma` hook that is in it. However, I got a little impatient waiting
for the version that included the Uptime Kuma hook to arrive in various distributions
package repositories so ended up building borgmatic from source on all computers.  
This was a lengthy process, and borgmatic isn't very portable; it requires installation of numerous python packages (and the entire rust language in FreeBSD).  
I wanted to make something that had to features I needed, in a single binary I 
could move around to whatever computer I needed it on.  
Norg was the outcome of this.  

## Work in Progress
Norg is still very much a work in progress, so there will be bugs. Please raise 
and issue, or create a pull request for any issues and resolutions you may have.

## Contact
For any issues, please raise an issue here. Otherwise, I can be contacted via 
the fediverse at [@paul@notnull.space](https://notnull.space/@paul).


