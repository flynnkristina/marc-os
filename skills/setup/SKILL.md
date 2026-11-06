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
2. Whoever runs Part 1 is signed in as a Google super admin, in a clean Chrome profile, with their phone handy for two-step codes. Google Cloud tends to switch to the wrong account: check the account at the top right on every new page.

If anything ever sends you to "domain-wide delegation" in the Admin console, stop. This setup doesn't use it.

## Part 1: the Google side (skip if Kristina says it's done)

These need his Google super admin account. Kristina did every one of them on her own test setup, so she knows each screen.

**1. Check he's an admin.** Open admin.google.com. *Check:* it opens the Admin console, not an "access denied" page.

**2. Check the Drive settings.** In the Admin console: Apps, Google Workspace, Drive and Docs, Sharing settings. Two things must be allowed: creating shared drives, and sharing outside the organisation. The connection accounts have addresses ending `iam.gserviceaccount.com`, which is outside his domain, and Google doesn't let that address be added to an approved list, so outside sharing has to be on. If it's off, turning it on affects the whole company, so that's Marc's call. *Check:* both are allowed. If his plan has no shared drives at all: Kristina, over to you.

**3. Make the two Shared Drives.** In drive.google.com: Shared drives, New (or right-click Shared drives, New). Use the names Kristina gives you. Both are new and empty, never a drive the company already works in.
- **The library drive**: the library itself.
- **The private drive**: for what the team must never see. The team's connection is never added to this one.

The organisation owns both, so no separate account is needed. **Kristina's login is the one person member of both, as Manager,** so there's always a way in. Create the drives signed in as Kristina if you can, since whoever creates a drive starts as its Manager. If Marc's login creates them, add Kristina as Manager and then remove Marc. *Check:* both open, empty, with Kristina as Manager.

**4. Make the Cloud project.** Open console.cloud.google.com. In the project picker: New project, name it "Library Connector", and set the location to his organisation (if you can't see his organisation, you're in the wrong account). Create, then Select project. From here on, check the project name at the top left before each step. *Check:* the project is selected at the top of the page.

**5. Turn on its three connections.** APIs and Services, Library. Search for and Enable each, one at a time: **Google Drive API**, **Google Docs API**, **Google Sheets API**. *Check:* all three show as enabled.

**6. Make the two connection accounts.** IAM and Admin, Service accounts, Create service account: name `library-writer`, Create and continue, skip the optional steps, Done (it needs no roles). Again for `library-reader`. *Check:* both appear, each with an email ending `iam.gserviceaccount.com`.

**7. Add them to the drives.** Copy each account's email address.
- **The library drive**, Manage members: library-writer as **Content manager**, library-reader as **Viewer**, notifications off.
- **The private drive**, Manage members: library-writer as **Content manager**. Not the reader.

*Check:* the members lists match. If either account is refused, step 2's outside sharing isn't on yet: Kristina, over to you.

## Part 2: connect this Mac

**8. Make the key, on this Mac.** First, if someone else's login created the Cloud project, they give Marc's account the roles **Owner** and **Service Account Key Admin** on it (IAM and Admin, IAM, Grant access), so he can make the key himself. Then, on this Mac, signed in as Marc, in the Library Connector project: Service accounts, open `library-writer`, Keys, Add key, Create new key, JSON, Create. It downloads once, to his Downloads folder. Tell him not to open, move, email or upload it. Only the writer key is made here; the reader key is made later, on a team member's computer.

Newer Google organisations block keys by default, so expect a pop-up the first time. With the Library Connector project selected: click **Fix access** in Google's pop-up, then Manage policy, Override parent's policy, Add rule, Enforcement off, Set policy. Wait a few minutes and try the key again. Still blocked: search the console for Organisation policies, filter on "disable service account", and check the policy changed is the one named in the error, since there are look-alikes. If a test key was made anywhere other than this Mac, delete that file and delete that key on the Keys tab. *Check:* his browser shows the download finished. Still blocked after that: Kristina, over to you.

**9. Find the key.** Call `find_library_keys`. *Check:* it names one writer key. Several: list them by file name and account and ask which. None: ask where it downloaded and call `find_library_keys` again with that `folder`.

**10. Connect.** Say what's about to happen: the key is checked with Google, saved in the Keychain, read back to make sure, and only then the downloaded file is deleted. Ask for his yes, then call `connect_library` with that key file. It sees both drives and asks which is the library: call it again with `drive` set to the library drive's exact name, never the private one. *Check:* it reports connected, the library drive and the writer role. Tell him:
- the key now lives only in this Mac's Keychain, under "StoryEQ library key";
- which files were deleted (`deletedCopies`);
- anything in `couldNotDelete` or `otherKeysStillOnThisMac`: name each and ask what he wants done.

If it says the key works but no Shared Drive is visible, step 7 isn't done: Kristina, over to you. Nothing is deleted when connecting fails.

## Part 3: start the library

**11. Lay out the library.** Call `list_library`. If it's empty, say you'll add the starting layout, list the files below, wait for his yes, then write each with `write_library_file`. If anything is already there, write nothing and say what's there.

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

**12. See it work.** Ask him to say "what's in my library". *Check:* it answers from the library. If the skills list is still empty, that's expected until Part 5 brings his skills over. Then have him open the library drive in drive.google.com and find `README.md` from step 11: that's the proof a write went all the way to Google.

**13. Where things live.** Tell him, in a few lines:
- the key: only in this Mac's Keychain, "StoryEQ library key";
- which library this Mac uses: a small settings file in the hidden folder `~/.config/marc-os/`, with no key in it;
- the library itself: the library Shared Drive, owned by his organisation, with the private drive beside it for what stays his alone.

**14. How it stays current.** In Claude, Customize, Plugins: turn on **Sync automatically** for the Marc OS marketplace, so plugin updates arrive on their own. Changes to his skills need nothing at all; they live in the library.

## Part 5: bring his current setup over

Skip this part if Kristina says there's nothing to move.

**15. Find the move folder.** Kristina prepares a folder named `_library_move` inside his current engine folder, laid out exactly as it should sit in the library. Ask him where his current engine folder is on this Mac, then list what's in `_library_move`, folder by folder, with a file count for each. *Check:* the list matches what Kristina expects. If the folder isn't there, or Google Drive hasn't finished syncing it to this Mac: Kristina, over to you.

**16. Copy it into the library.** Say what's about to happen: every file in `_library_move` is copied into the library at the same path, and nothing in his current folder is changed or deleted. One yes from him covers the whole copy. Then, for each text file, read it and write it with `write_library_file` at its path inside `_library_move`. If a file already exists in the library from Part 3, the move folder's version replaces it. Skip anything that isn't text (images, spreadsheets) and list what was skipped. *Check:* `list_library` shows the same folders and file counts as step 15. Name anything that failed.

**17. Give "run my debrief" one home.** If an older debrief skill is installed in his Claude (Customize, Skills), turn it off now, so the phrase reaches only the library's debrief. *Check:* in a new session, "run my debrief" starts with "Using the debrief skill from your library."

**18. Retire the old folder.** His current engine folder stays exactly as it is, as a record of how things were. From now on, changes go to the library, so nothing is written to the old folder again. Kristina may rename it to make that obvious.

**Last: close up.** If Kristina's login was made a temporary Super Admin for Part 1, Marc removes that now: Admin console, Directory, Users, Kristina, Admin roles and privileges, Super Admin off, Save. Her login stays Manager of both drives. If Marc's own sign-in was shared instead, he changes his password and checks Your devices at myaccount.google.com.

Close with: "You're set up. Say 'what's in my library' or 'run my debrief' any time."

## Disconnect

When he asks to take the library off this Mac, say what will happen: the key is removed from the Keychain and the settings file is deleted. Wait for his yes, then call `disconnect_library`. Then tell him the last step: delete that key in Google Cloud (Service accounts, library-writer, Keys), so it stops working everywhere.
