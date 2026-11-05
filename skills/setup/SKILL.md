---
name: setup
description: Guided setup for Marc OS - walks Marc through creating his library on Google, connecting it to this Mac, and checking it works, one step at a time, with Kristina on the call. Also checks or disconnects the library. Use when Marc says "set up my library", "set up Marc OS", "connect my library", "my library isn't connected" or "disconnect my library", right after installing Marc OS, or when the library skill reports the library isn't set up.
---

# Marc OS: setup

This walks Marc through setting up his library and connecting it to this Mac. It's run live, with Kristina on the call to help if something sticks.

## How to run it

- One step at a time. Say what the step is for in one sentence, give the clicks, then ask him to tell you when it's done. Confirm each step's check before moving on.
- If a step doesn't behave as described, **stop and say "Kristina, over to you"**. Don't improvise around a failure; the step numbers match Kristina's run sheet so she can take it from exactly there.
- Plain words. He should feel he's setting up his own system, not watching machinery.
- Never ask him to paste, read out, open, email or upload a key file, and never show its contents.

**The one rule for the key:** it lives in exactly one place, this Mac's Keychain. Not in a password manager, not on the Desktop (that syncs to iCloud), not in email or chat. If it's ever lost, the fix is a new key from Google Cloud, never a backup copy.

## Before starting

Call `library_status`.
- **Connected:** say which library and role it's using, list the top-level folders, and stop. Nothing to set up.
- **Set up, but not reachable:** say what went wrong in one plain sentence, then go to Part 2.
- **Not set up:** ask Kristina one question: "Is the Google side already done, or are we doing it now?" Done: go to Part 2. Not done: start at Part 1.

Then check, and don't continue until each is true:
1. He's in Cowork or the Code tab of the Claude desktop app, on his Mac. Plain chat can't run the library connection.
2. He's signed in to his Google admin account in his browser, with his phone handy for two-step codes.

## Part 1: the Google side (skip if Kristina says it's done)

These need his Google super admin account. Kristina has done every one of them on her own test setup, so she knows each screen.

**1. Check he's an admin.** Open admin.google.com. *Check:* it opens the Admin console, not an "access denied" page.

**2. Make the library its own Google account.** The library belongs to its own account, not to any person, so nobody's Drive holds it. Admin console, Directory, Users, Add new user: first name "Library", email `library@` his domain. It takes one paid seat. Set a password, turn on two-step verification, and save the login in his own password manager (that's an account login, so it belongs there; the key in Part 2 never does). *Check:* the user shows in the list.

**3. Make the library's Shared Drive.** In a private browser window, sign in to drive.google.com as the library account. Shared drives, New, name it "Library". The only member is the library account, as Manager. *Check:* the empty drive opens. If there's no "Shared drives" option, his Google plan doesn't include them: Kristina, over to you.

**4. Let the connection accounts in.** The accounts that connect the library have addresses ending `iam.gserviceaccount.com`, which is outside his domain, so his Drive sharing settings may block them. In the Admin console, under Apps, Google Workspace, Drive and Docs, Sharing settings, allow sharing with that domain. Kristina will point to the exact switch. *Check:* Kristina confirms the setting.

**5. Make the Cloud project.** Open console.cloud.google.com. In the project picker: New project, name it "library", under his organisation, Create. Then APIs and Services, Library, find "Google Drive API" and Enable. *Check:* the Drive API shows as enabled for the "library" project.

**6. Allow a key for this one project.** Newer Google organisations block connection keys by default. In the Cloud console, IAM and Admin, Organisation policies, find the policy that disables service account key creation, and set an exception for the "library" project only. This may first need him to give himself the Organisation Policy Administrator role. This is the step most likely to need help: Kristina, over to you if anything looks different. *Check:* the policy shows as not enforced for "library".

**7. Make the two connection accounts.** IAM and Admin, Service accounts, Create service account: name `library-writer`, no roles, Done. Again for `library-reader`. *Check:* both appear, each with an email ending `iam.gserviceaccount.com`.

**8. Add them to the library.** Back in the private window, signed in as the library account: open the "Library" Shared Drive, Manage members, add the library-writer email as **Content manager** and the library-reader email as **Viewer**, with notifications off. *Check:* both show in the member list. If either is refused, step 4 isn't through yet: Kristina, over to you.

## Part 2: connect this Mac

**9. Make the key, on this Mac.** In the Cloud console, Service accounts, open `library-writer`, Keys, Add key, Create new key, JSON, Create. It downloads to his Downloads folder. Tell him not to open, move, email or upload it. Only the writer key is made here; the reader key is made later, on a team member's computer. *Check:* his browser shows the download finished. If Google says key creation is disabled, step 6 isn't through yet: Kristina, over to you.

**10. Find the key.** Call `find_library_keys`. *Check:* it names one writer key. Several: list them by file name and account and ask which. None: ask where it downloaded and call `find_library_keys` again with that `folder`.

**11. Connect.** Say what's about to happen: the key is checked with Google, saved in the Keychain, read back to make sure, and only then the downloaded file is deleted. Ask for his yes, then call `connect_library` with that key file. *Check:* it reports connected, the "Library" drive and the writer role. Tell him:
- the key now lives only in this Mac's Keychain, under "StoryEQ library key";
- which files were deleted (`deletedCopies`);
- anything in `couldNotDelete` or `otherKeysStillOnThisMac`: name each and ask what he wants done.

If it asks to choose a drive, list the names, ask which is the library, and call `connect_library` again with `drive`. If it says the key works but no Shared Drive is visible, step 8 isn't done: Kristina, over to you. Nothing is deleted when connecting fails.

## Part 3: start the library

**12. Lay out the library.** Call `list_library`. If it's empty, say you'll add the starting layout, list the files below, wait for his yes, then write each with `write_library_file`. If anything is already there, write nothing and say what's there.

`README.md`:
```
# Library

This Shared Drive is Marc's library. Marc OS connects to it and runs the skills kept here, so the skills can improve without a plugin update.

| Folder | What's in it |
|---|---|
| skills/ | How to do each kind of work. skills/INDEX.md lists them. |
| judgments/ | Marc's rules, each in his own words, with his reason and its status. |
| raw-material/ | The sources the rules came from. |

Marc OS writes here through the library-writer account, and only after Marc says yes to each write. Anything else connected reads through library-reader, and Google refuses its writes.
```

`skills/INDEX.md`:
```
# Skills in this library

| Skill | Use when | File |
|---|---|---|

To add a skill: write its file in skills/, then add a row here.
```

`judgments/README.md`:
```
# How a rule is written

One rule per file, in a folder named for its area of work.

    # The rule, in one line

    Area: the area of work
    Status: confirmed | not yet confirmed
    His words: "the quote" (date, where it was said)
    Why: "his reason, quoted" | not given yet

    ## Examples
    - date: what happened, and what he chose

A rule is "confirmed" only when it's in his own words and he said yes to filing it.
```

`judgments/NOT_THIS.md`:
```
# Not this

Things Marc has said are not his rules: the date, what was proposed, and his reason. Nothing here is asked again.
```

`raw-material/README.md`:
```
# Raw material

The sources rules came from. One file per source, named by date and what it is.
```

*Check:* `list_library` shows `README.md`, `judgments`, `raw-material` and `skills`.

## Part 4: make it his

**13. See it work.** Ask him to say "what's in my library". *Check:* it answers from the library. If the skills list is still empty, say that's expected: Kristina adds his skills next, and they appear without him installing anything.

**14. Where things live.** Tell him, in a few lines:
- the key: only in this Mac's Keychain, "StoryEQ library key";
- which library this Mac uses: a small settings file in the hidden folder `~/.config/marc-os/`, with no key in it;
- the library itself: the "Library" Shared Drive, owned by the library account.

**15. How it stays current.** In Claude, Customize, Plugins: turn on **Sync automatically** for the Marc OS marketplace, so plugin updates arrive on their own. Changes to his skills need nothing at all; they live in the library.

Close with: "You're set up. Say 'what's in my library' any time."

## Disconnect

When he asks to take the library off this Mac, say what will happen: the key is removed from the Keychain and the settings file is deleted. Wait for his yes, then call `disconnect_library`. Then tell him the last step: delete that key in Google Cloud (Service accounts, library-writer, Keys), so it stops working everywhere.
