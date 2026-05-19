# [Nimduvo](https://nimble.directory/pkg/nimduvo)
An asynchronous Nim wrapper for the Luduvo API.
> **Disclaimer**: This is an unofficial package community-maintained and as such it is not affiliated with Luduvo nor its developers.
# Installation
```
nimble install nimduvo
```
# Quickstart
```nim
import Nimduvo/users
import std/[asyncdispatch, json]

let users = waitFor getUser(88)
echo $users
```

```yaml
users: {
  user_id: 88,
  username: "sed",
  member_since: 1775081519,
  last_active: 1777803798,
  displayName: "sed",
  banner_url: "https://assets.luduvo.com/banners/e522faa9-fa64-47d9-a913-9965d6b5bbf8.png",
  status: "",
  bio: "The cat, the rat, and Lovell our dog, rule all England under a hog.",
  accent_colour: "#8C945C",
  avatar: {
    head_color: "#C8C8C8",
    torso_color: "#23590A",
    left_arm_color: "#C8C8C8",
    right_arm_color: "#C8C8C8",
    left_leg_color: "#D9544D",
    right_leg_color: "#D9544D"
  },
  equipped_items: [
    {
      item_id: 248,
      name: "Spiraling",
      category_slug: "faces",
      thumbnail_url: "https://assets.luduvo.com/item_previews/248/08693364df1c565b73a5b0bd9cf3f9f456c113ddf4a61ba81afe9a646c159568.png",
      slots: ["face_decal"]
    },
    {
      item_id: 183,
      name: "Pasta Strainer",
      category_slug: "hats",
      thumbnail_url: "https://assets.luduvo.com/item_previews/183/a5240317dcfe99ffeaf5d52c748c7ab0c5a45db4f0d801425c4218dab3b4378d.png",
      slots: ["hat"]
    },
    {
      item_id: 17,
      name: "Luduvo Default Bundle",
      category_slug: "bundles",
      thumbnail_url: "https://assets.luduvo.com/item_previews/17/f7296b9891e37959eb23f84af40205ff52ce65a30e2a13ee1ae19f4840f8921a.png",
      slots: ["head", "left_arm", "left_leg", "right_arm", "right_leg", "torso"]
    },
    {
      item_id: 131,
      name: "cube tee",
      category_slug: "flat-shirts",
      thumbnail_url: "https://assets.luduvo.com/item_previews/131/5730446850cfcaac94ba314f9d0d3faf26c6c1c3f403cae79be9bd6092eebc07.png",
      slots: ["shirt"]
    }
  ],
  badges: [],
  friend_count: 6,
  place_count: 0,
  item_count: 0,
  allow_joins: true,
  is_owner: false
}
```
