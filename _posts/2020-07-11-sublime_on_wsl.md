---
layout: post
title: Launching Sublime Text from Ubuntu on Windows Subsystem for Linux
---

`subl` not working?

**What I had to do:**

1. ([Download Sublime Text](https://www.sublimetext.com/))
2. Add its installation directory to PATH:
    1. Type `env` into Start Search
    2. Click `Environment Variables...`
    3. Click `Path` in `User variables`, then `Edit...`
    4. Click `New`
    5. Add the address, in my case `C:\Program Files\Sublime Text 3\`
    6. Click `OK`
3. Restart Ubuntu
4. `subl.exe` now launches Sublime.

**Optionally, create an alias for `subl.exe`:**

1. In Ubuntu, `cd ~` to cd to your home directory
2. Enter `nano .profile`
3. Add `alias subl='subl.exe'` somewhere
4. Save and exit
5. Restart Ubuntu
6. `subl` now launches Sublime.

**Notes:**
- `cd` on its own also cds to your home directory
- You don't have to use Nano
- You don't have to put your alias in `.profile`, I only put it there because of [this](https://github.com/michaeltreat/Windows-Subsystem-For-Linux-Setup-Guide/blob/master/readmes/04_updating_terminal.md), which is excellent
- You don't have to use `subl` either. Call it whatever you want
- After adding Sublime's installation directory to PATH, `subl` worked for me in PowerShell but not Ubuntu, hence the need for an alias.

I may expand this post as I get to grips with Sublime.