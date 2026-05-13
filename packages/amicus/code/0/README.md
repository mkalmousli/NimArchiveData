# Amicus

2026 Update: *Onbox is currently undergoing a rewrite (from scratch), which means that as of now, this library won't be updated until Onbox reaches the point where its database layer is relatively stable*

Social networking library powering [Onbox](https://codeberg.org/onbox/onbox)

Documentation and usage instructions coming some day hopefully.

## What?

[Onbox](https://codeberg.org/onbox/onbox) is a lightweight microblogging server, which implements the [MastoAPI](https://docs.joinmastodon.org/client/intro/) interface. Amicus contains the database logic that powers Onbox. It's the foundational layer that Onbox is built upon. 

You should note that using this library locks you into providing the same features that Onbox provides, this is a heavily opinionated library and using it is not recommended unless you're forking Onbox for a minor change or implementing something extremely similar.

## Why?

Separating a program's logic into many small, reusable and auditable bits is a good thing,
Onbox's database logic has been growing non-stop, and it felt awkward to navigate through the entire structure.

So, to hopefully encourage better coding practices and to make the codebase for the entire project a bit more approachable and maintainable,
I've split up the database logic that used to be in the Onbox repository into its own library, this is that.

## Versioning? Documentation?

Documentation is hopefully coming some day, as for versioning, Amicus will follow the exact same release schedule as Onbox, it is not a standalone library.

## Licensing?

Given that this is Onbox's database logic separated into a library, I'm required by law to license it under the same license as Onbox. Which is the GNU Affero General Public License version 3 or later.

In theory re-licensing is possible, but you'd have to get in touch with not only me, but the original founder of the project and any future contributors.
All the contact info you need can be found in the copyright header for every module.

## Migrations?

Oh I don't even know where to start...

Right now, Onbox is in the "move fast and break stuff" phase, nothing is stable except for MastoAPI itself.
Amicus doesn't use an ORM, everything is initialized from an (idempotent) SQL schema file. (in `src/assets/`),
and in that schema file lies a "database version" value that can be checked, modified and whatnot.

we're probably gonna end up using a system where that "database value" is checked and migrations are applied using mere SQL files.  
But there's probably better ideas out there.
