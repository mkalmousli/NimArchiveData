# Norg

A simple, portable, orchestration tool for the [borg backup](https://www.borgbackup.org) and [restic](https://restic.net) utilities written in Nim.  

Full documentation on the [Norg Backup Website](https://norgbackup.net/)

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
label = "MyBorgRepo"
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

You can then run the equivalent `borg` or `restic` command to init, create, list, mount and extract your backups.

**Using BorgBackup**
```sh
# Init your repository
norg -c myconfig.toml init

# Backup your data
norg -c myconfig.toml create

# List Archives
norg -c myconfig.toml list

# Mount an Archive
norg -c myconfig.toml mount -r MyBorgRepo -a pcname-2024-08-18T15:20:17773204 /home/me/mnt
# Unmount an Archive
norg -c myconfig.toml umount -r MyBorgRepo /home/me/mnt

# Extract an Archive
# You must be in an empty folder for this to work
norg -c myconfig.toml extract -r MyBorgRepo -a pcname-2024-08-18T15:20:17773204
 
# Or You must set the destination to an empty folder 
norg -c myconfig.toml extract -r MyBorgRepo -a pcname-2024-08-18T15:20:17773204 --destination /tmp/my_extracted_archive

# Prune all repos
norg -c myconfig.toml prune
# Or specify a particula repo
norg -c myconfig.toml prune -r MyBorgRepo

# Delete an Archive
norg -c myconfig.toml delete -r MyBorgRepo -a pcname-2024-08-18T15:20:17773204
```

**Using Restic** _New in v0.1.6_  
Add a repository with a `tool = "restic"` option.
```toml
[[repositories]]
label = "MyResticRepo"
path = "/my/restic/backup/location"
tool = "restic"
```

Then run the appropriate commands
```sh
# Init your repository
norg -c myconfig.toml init

# Backup your data
norg -c myconfig.toml backup

# List Snapshots
norg -c myconfig.toml snapshots

# Mount a Repo
norg -c myconfig.toml mount -r MyResticRepo /home/me/mnt

# Restore an Archive (restore destination must be empty)
norg -c myconfig.toml restore -a latest --destination /my/restore/location

# Prune a repo
norg -c myconfig.toml prune

# Forget a Snapshot
norg -c myconfig.toml forget -r MyBorgRepo -a a1b2c3d4
```

### Command line parameters

* `-c`, `--config`: The configuration file to use
* `-r`, `--repository`: The repository to work on
* `-a`, `--archive`: The Archive to operate on (snapshots for restic)
* `-d`, `--destination`: When extracting/restoring, the destination for the extracted files

## Build from Source
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

## System Support
Norg should work on any system that can compile `nim` code with `nimble`.
Tested on:
* Arch Linux
* Debian Linux
* AlmaLinux
* FreeBSD

But in general all Linux distributions and BSDs should work if borg and/or restic is installed.  
Windows support (Restic only until Borg support in Windows is available) is planned with the only real issue being finding the restic executable. Should be an easy fix.

## Naming. Why "Norg"?
Well, I don't know. I'm a Star Trek fan so obviously I wanted to keep something 
in line with the [Borg pseudo-species](https://memory-alpha.fandom.com/wiki/Borg) as the borg backup utility does.  
Also, sometimes I feel my code has elements of inexperience but loads of potential... which reminded me of [Nog](https://memory-alpha.fandom.com/wiki/Nog).  
So, simply put, `Norg` is an portmanteau of "Borg" and "Nog".

## Borg and Restic Notes
I love both Borg and Restic tools, they are both great and both have their pros and cons. As [BorgBase](https://borgbase.com) has repos for both, I felt it only sensible to 
provide a tool that can use both.  
Providing implementation for both means you could have duplicate backups using 
different tools which should provide a certain amount of protection over failures in 
a particular tool.
Caution should be taken when using additional flags when you have repositories of 
both types in the same configuration file. I have tried to cater for some common flags 
that will be converted to the correct type for a particular tool, but this may not always be the case. If in any doubt, it is advised to use the `--repository` flag for any borg/restic specific flags so as not to cause one the other tool to fail.  

Some different yet similar commands should be converted to the correct type. A table below shows some of these:  
| Borg Command | Restic Command             | Result                                     |
| ---          | ---                        | ---                                        |
| create       | backup                     | creates a backup                           |
| list         | snapshots                  | lists archives/snapshots                   |
| extract      | restore                    | restores/extracts a backup                 |
| delete       | forget                     | removes a archive/snapshot                 |
| prune        | forget (with --prune flag) | removes snapshots as per `--keep-*` config |

You may specify either command and it will work with both except the `forget` command. This will only forget a single snapshot in restic.  

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


