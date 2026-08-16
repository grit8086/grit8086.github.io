+++
title = "Unauthenticated Indirect Object Reference in the Department of Defense"
date = 2026-03-24
draft = false
tags = ["web"]
description = "How I found a broken access control bug in a Department of Defense subdomain that allowed anyone to view private files without authentication."
+++

# I Found an Unauthenticated Indirect Object Reference Vulnerability in the Department of Defense
While hunting on the Department of Defense bug bounty program, I came across a simple but impactful vulnerability, a broken access control bug that allowed anyone to view files they didn't own, without even being logged in. Here's how it went.

### What is IDOR?
IDOR, or Insecure Direct Object Reference, is a type of broken access control vulnerability where an attacker can access or modify resources that belong to another user, simply by changing a reference to that resource, usually something visible like an ID in a URL or request parameter.Think of it like a hospital that gives each patient a folder numbered by their ID. You are patient 112, so your records are in folder 112. But nothing is stopping you from walking over and opening folder 113, which belongs to someone else entirely.

## Finding a Target
I've always wanted to hunt on NASA, but I decided to start with the US Department of Defense instead. Fair warning, this is a late write-up, and I don't actively hunt much anymore, but I figured this one was worth documenting.

## Reconnaissance
Reconnaissance isn't just about subdomain enumeration or directory brute-forcing. A big part of it is checking application functionality and identifying potentially violated constraints. One of my favorite things to test is whether I can access an object that belongs to another user, something I clearly shouldn't own.

While exploring a Department of Defense subdomain, I came across an attachment endpoint on the user's home page:

```
/BugReport/Admin/Attachment/{id}
```

The `{id}` parameter determines which file is being viewed. The immediate question was: what happens if I swap that ID for one that doesn't belong to me?

```
GET /BugReport/Admin/Attachment/1568600
```

It returned a file I had no business seeing. I reported it to the Department of Defense immediately, and earned a badge for it.

But here's what made it even more impactful: when I stripped the `Cookie` header from the request entirely, the file was still accessible. This elevated it from a standard broken access control bug to an **unauthenticated** broken access control vulnerability, no login required whatsoever.

## Mitigations
- **Enforce access control server-side.** Never rely on client-side checks alone, they're trivially bypassed.
- **Verify ownership before serving resources.** Before returning any file or object, confirm that the requesting user actually owns or has permission to access it.
- **Use unpredictable identifiers.** Sequential numeric IDs like `1568600` make enumeration trivial. Random UUIDs make it significantly harder for an attacker to guess valid resource identifiers.

You can read the full disclosed report on HackerOne: [Report #3259610](https://hackerone.com/reports/3259610)

Hope u learned something, happy hunting! :D
