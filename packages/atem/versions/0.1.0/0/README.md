# atem

CLI for managing Facebook Pages via the Meta Graph API. Post text, photos, links, schedule posts, list your pages — all from the terminal.

## Setup

You'll need a Meta App with Facebook Login enabled.

1. Create an app at [developers.facebook.com](https://developers.facebook.com)
2. Add "Facebook Login" and set `http://localhost:8910/callback` as a valid redirect URI
3. Copy `.env.example` to `.env` and fill in your app credentials

```
cp .env.example .env
```

## Install

```
nimble install atem
```

Or build from source:

```
nimble build
```

## Usage

Authenticate first — this opens your browser:

```
atem login
```

Then you're good to go:

```sh
# List your pages
atem pages

# Post something
atem publish --pageId=123456 --message="Hello from the terminal"

# Post a photo
atem photo --pageId=123456 --file=banner.jpg --caption="New banner"

# Share a link
atem link --pageId=123456 --url="https://example.com" --message="Check this out"

# Schedule a post for later
atem schedule --pageId=123456 --message="Good morning" --time=2026-04-15T09:00

# See recent posts
atem posts --pageId=123456

# Delete a post
atem delete --postId=123456_789012

# Page details
atem info --pageId=123456
```

## Permissions

The OAuth flow requests: `pages_manage_posts`, `pages_read_engagement`, `pages_manage_metadata`, `pages_read_user_content`, `pages_show_list`.

Your Meta App needs to have these permissions approved (or you need to be a test user/admin of the app).

## Token lifecycle

`atem login` gets a long-lived user token (60 days). Page tokens derived from it don't expire. You'll need to re-run `login` when the user token expires.
