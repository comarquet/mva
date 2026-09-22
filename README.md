# MVA Course Notes

This repository contains course notes in the [Obsidian](https://obsidian.md/) format. They are designed to be read and browsed in Obsidian: links between notes, callout boxes, and mathematical formulas are more comfortable there than in a web browser.

This guide explains everything step by step. It assumes no prior knowledge of Git, GitHub, or the command line.

## What you need

- a Windows or macOS computer;
- an Internet connection;
- about five minutes to install two free applications: GitHub Desktop and Obsidian.

GitHub Desktop downloads a copy of the repository to your computer and lets you update it later. Obsidian is used to read the notes.

> **Tip — No programming required.**
> The steps below do not require typing commands in a terminal. Just follow the buttons described below.

## 1. Install GitHub Desktop and clone the repository

### On Mac

1. Open the official [GitHub Desktop download page](https://desktop.github.com/download/).
2. Download the version for your Mac: **Apple silicon** for most recent Macs, or **Intel** for older models.
3. Open the downloaded file, then drag the GitHub Desktop icon to the **Applications** folder if macOS asks you to do so.
4. Launch GitHub Desktop from Applications. Sign in to GitHub if the application asks; you can create a free account if you do not have one.

### On Windows

1. Open the official [GitHub Desktop download page](https://desktop.github.com/download/).
2. Click **Download for Windows**.
3. Open the downloaded file and follow the installation steps.
4. Launch GitHub Desktop from the Start menu. Sign in to GitHub if the application asks; you can create a free account if you do not have one.

### Download a copy of the repository

The following steps are the same on Mac and Windows.

1. In GitHub Desktop, open the **File** menu and click **Clone repository...**.
2. Select the **URL** tab.
3. Copy and paste this address into the **Repository URL** field:

```text
https://github.com/comarquet/mva.git
```

4. In **Local path**, choose a location that is easy to find again, such as your `Documents` folder.
5. Click **Clone**.

GitHub Desktop will create a folder called `mva`. This is your local copy of the repository: do not move its files one by one. If you need to store it elsewhere, move the entire `mva` folder.

> **Update the notes later.**
> Open GitHub Desktop, select this repository, then click **Fetch origin**. If the button changes to **Pull origin**, click it as well: new notes will then be downloaded to your computer.

## Alternative: use Git from the command line

GitHub Desktop is the easiest option for most people. The command line is an alternative if you prefer typing short commands. Git is the tool that tracks changes and downloads updates; a **terminal** is the application where you type those commands.

### Install Git and open a terminal

**On Mac**

1. Open **Terminal**: press `Command + Space`, type `Terminal`, then press Enter.
2. Type the following command and press Enter:

```text
git --version
```

3. If Git is not installed, macOS will offer to install the **Xcode Command Line Tools**. Choose **Install** and wait for it to finish. Run `git --version` again to check that it now displays a version number.

**On Windows**

1. Download and install [Git for Windows](https://git-scm.com/download/win). The default choices in the installer are suitable for most users.
2. Open the Start menu, search for **Git Bash**, and open it. Git Bash is the terminal used for the commands below.
3. Type the following command and press Enter:

```text
git --version
```

If the command displays a version number, Git is ready.

### Clone the repository

In Terminal on Mac or Git Bash on Windows, type the following commands one line at a time, pressing Enter after each line:

```text
cd Documents
git clone https://github.com/comarquet/mva.git
cd mva
```

The first line moves to your Documents folder. The second downloads the repository and creates the `mva` folder. The third enters that folder. You only need to clone the repository once.

> **Tip — What does `cd` mean?**
> `cd` means “change directory”. It moves the terminal into a folder. If you want to store the repository somewhere else, open that folder in your file browser first, then use its path instead of `Documents`.

### Check for updates

Each time you want to download the latest notes, open Terminal or Git Bash, enter the `mva` folder if necessary, then run:

```text
git pull origin main
```

To see whether you have local edits that have not been saved to Git, run:

```text
git status
```

Git will describe changed files in plain text. If you only read the notes and do not edit them, `git status` should say that the working tree is clean.

### Optional: save and share an edit

Only do this if you have permission to contribute to the repository. Work on a separate branch rather than directly on `main`:

```text
git switch -c my-note-change
git add path/to/changed-note.md
git commit -m "Describe the change briefly"
git push -u origin my-note-change
```

Replace `path/to/changed-note.md` with the actual file path. The command `git add` chooses exactly what will be included in the next save; avoid using broad commands such as `git add .` until you are comfortable reviewing every changed file. After pushing, open GitHub to create a pull request, or ask Corentin for help.

## 2. Install Obsidian

### On Mac

1. Open the official [Obsidian download page](https://obsidian.md/download).
2. In the **Mac** section, download the **Universal** version.
3. Open the downloaded file and place Obsidian in the **Applications** folder if macOS asks you to do so.
4. Launch Obsidian from Applications.

### On Windows

1. Open the official [Obsidian download page](https://obsidian.md/download).
2. In the **Windows** section, download the **Universal** version.
3. Open the downloaded file and follow the installation steps.
4. Launch Obsidian from the Start menu.

> **Tip — Obsidian is free for this use.**
> You can use the application without creating an account or enabling paid plans. An account is only useful for optional services, such as Obsidian's own sync service.

## 3. Open the notes in Obsidian

1. At first launch, select **Open folder as vault**. If Obsidian is already open, click the vault icon in the bottom-left corner, then select **Open another vault** and **Open folder as vault**.
2. Select the `mva` folder created by GitHub Desktop. If you cloned it into Documents, it will usually be `Documents/mva`.
3. Click **Open**.

The notes will appear in the left sidebar. Start with the `prerentree` folder, then select a subject and its `notes` folder.

> **Important — Choose the right folder.**
> Select the entire `mva` folder, not an individual Markdown file and not the hidden `.obsidian` folder. The `.obsidian` folder stores settings; do not open it on its own or delete it.

## Using the notes

- Click a blue or purple link to open another note.
- Use the search field in the top-left corner to find a word or concept.
- Files ending in `.md` are notes. They are plain text files, so you can read and edit them in Obsidian.
- If you edit notes and want to keep your changes on GitHub, ask someone familiar with GitHub Desktop for help before clicking **Push origin**. This helps avoid accidentally overwriting other people's work.

## Translating the courses with a coding agent

The notes are mainly written in French, but they can be translated easily with a **coding agent**. An agent can read the Markdown files, translate the text, and preserve the heading structure, Obsidian links, and mathematical formulas.

If you have created or would like to create a translation, contact Corentin at **cormarquet@gmail.com**. He can then add, or coordinate the addition of, English course notes to the repository.

> **Tip — A good prompt for a translation.**
> Explicitly ask the agent to preserve `[[...]]` links, code blocks, mathematical formulas, and the structure of every file. The translated version will then remain easy to browse in Obsidian.
