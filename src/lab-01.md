# Lab 1: Git and GitHub

This session introduces version control with Git and GitHub. Setting up these tools first means you can track your work from day one.

```admonish tip title="Before week 2"
Install R and RStudio before next week's lab. Instructions are in [Lab 2: Install R and RStudio](lab-02.md).
```

---

## What is version control?

Version control tracks every change you make to your files. Instead of saving `report_v2_final_FINAL.docx`, you save a single file and Git remembers its entire history. You can go back to any previous version, see exactly what changed, and collaborate without overwriting each other's work.

**GitHub** is a website that hosts your Git repositories online. It serves as a backup and lets you share your work.

## Why bother?

- Your lab diary and final report will be easier to manage.
- You will never lose work (every version is saved).
- Employers value version control skills.
- It is the standard tool for reproducible research.

## Step 1: Create a GitHub account

1. Go to [https://github.com](https://github.com).
2. Click **Sign up** and follow the prompts.
3. Choose a username you are happy to use professionally (e.g., `jsmith-nz`, not `xXx_gamer_xXx`).
4. Verify your email address.

```admonish tip title="Student benefits"
Apply for the [GitHub Student Developer Pack](https://education.github.com/pack) with your university email. It includes free private repositories and other tools.
```

## Step 2: Install Git

### macOS

Open **Terminal** (search for it in Spotlight) and type:

```bash
git --version
```

If Git is not installed, macOS will prompt you to install the Xcode Command Line Tools. Follow the prompts.

### Windows

Download Git from [https://git-scm.com/download/win](https://git-scm.com/download/win). Run the installer and accept the defaults.

### Verify installation

Open a terminal (Terminal on macOS, Git Bash on Windows) and type:

```bash
git --version
```

You should see something like `git version 2.44.0`.

## Step 3: Configure Git

Tell Git your name and email (use the same email as your GitHub account):

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## Step 4: Create your first repository

1. Go to [https://github.com](https://github.com) and click the **+** button (top right), then **New repository**.
2. Name it `psych-434-lab` (or similar).
3. Add a description (e.g., "Lab diary for PSYCH 434").
4. Select **Private**.
5. Tick **Add a README file**.
6. Click **Create repository**.

## Step 5: Clone the repository to your computer

Cloning downloads a copy of the repository to your machine and links it to GitHub.

1. On your repository page, click the green **Code** button.
2. Copy the HTTPS URL.
3. Open a terminal and navigate to where you want to store your work:

```bash
cd ~/Documents
```

4. Clone the repository:

```bash
git clone https://github.com/YOUR-USERNAME/psych-434-lab.git
```

5. Move into the repository folder:

```bash
cd psych-434-lab
```

You now have a local copy linked to GitHub.

## Step 6: The basic Git workflow

The everyday workflow has three steps: **stage**, **commit**, **push**.

### 1. Make a change

Open the `README.md` file in any text editor and add a line, such as:

```
This is my lab diary for PSYCH 434 (2026).
```

Save the file.

### 2. Stage the change

Staging tells Git which changes you want to include in your next snapshot:

```bash
git add README.md
```

To stage everything at once, use `git add -A`.

### 3. Commit the change

A commit is a snapshot with a short message describing what you did:

```bash
git commit -m "update readme with course details"
```

### 4. Push to GitHub

Push sends your commits to GitHub so they are backed up online:

```bash
git push
```

```admonish note title="Authentication"
The first time you push, GitHub will ask you to authenticate. Follow the prompts to sign in through your browser.
```

## Step 7: Check your work

Go to your repository page on GitHub. You should see the updated README file with your changes.

## Quick reference

| Command | What it does |
|---|---|
| `git status` | Show which files have changed |
| `git add <file>` | Stage a file for the next commit |
| `git add -A` | Stage all changes |
| `git commit -m "message"` | Save a snapshot with a message |
| `git push` | Upload commits to GitHub |
| `git pull` | Download changes from GitHub |
| `git log --oneline` | Show commit history |

```admonish tip title="Workflow summary"
Edit files → `git add` → `git commit -m "message"` → `git push`. Repeat.
```

## Alternative: GitHub Desktop

If you prefer a graphical interface, download [GitHub Desktop](https://desktop.github.com/). It provides the same stage/commit/push workflow with buttons instead of terminal commands. Either approach is fine for this course.
