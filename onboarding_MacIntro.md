# Onboarding: Setting Up Your Mac for Computational Biology

## Administrative

- Start by getting access to your email and My University — you'll need both for many of the following tasks. You log in with your p-number, password, and two-factor authentication, so install the Google Authenticator app ([MFA setup instructions](https://www.rug.nl/society-business/centre-for-information-technology/security/multi-factor-authentication?lang=en)) on your phone first.
- Contact Sandra Haan (our secretary, secbc@rug.nl) for administrative onboarding and to obtain your university card. Ask her to add you to the biotechnology email list as well. She will also have some forms for you to fill in, and she or Hugo can provide you with an office key.
- Request access to the Habrok high-performance cluster here: [Habrok account request](https://rug.topdesk.net/tas/public/ssp/content/serviceflow?unid=84efb629e7484b728dbbf8dfef78fdad). If that link doesn't work, log in to the [CIT help portal (IRIS)](https://iris.service.rug.nl/) and search for "Habrok". In the form, mention that you're a computational PhD doing MD simulations.
- Some software can be downloaded from the [university software portal](https://iris.service.rug.nl/tas/public/ssp/content/detail/service?unid=eaf8ada50c7846b792798f1addb69f8e&from=d74117e5-047a-4378-bb03-3510cf8871d1). Other software like Office and Adobe can be downloaded directly from their websites after logging in with your @rug.nl email address. (This process changes occasionally — check IRIS for help if it doesn't work.)

## macOS Basics

Below are some instructions for making your Mac a productive environment. You may already know some of these — apologies if parts are a bit basic.

- **The Dock** sits at the bottom of the screen and shows running and pinned applications. Clean it up by removing apps you don't use (right-click the icon) and pin the ones you use often.
- **Switching windows:** Use `Cmd + Tab` to switch between apps, or press `F3` (Mission Control) to see all open windows at once.
- **Spotlight:** Press `Cmd + Space` to quickly search for any program, document, or setting.
- **Finder** is the macOS equivalent of Windows Explorer. Try the different view modes (list, columns, etc.).
- **System Settings** are found under the Apple menu in the top-left corner. The menu bar at the top of the screen always belongs to the currently active (foreground) application.
- **Installing apps:** Mac installers come in two forms. A `.dmg` disk image: open it, then drag the app icon into your Applications folder. A `.pkg` installer: double-click and follow the steps. Some apps are also available directly from the App Store.

## Terminal Setup

macOS is based on UNIX and shares the same core structure as Linux. This means you have access to a full UNIX command line. You'll use the terminal a lot in computational biology, so it's worth spending some time making it comfortable.

A good first step is to install [iTerm2](https://iterm2.com/), a modern replacement for the built-in Terminal app. Have a look at its settings and pick a colour scheme you like.

## Homebrew

Next, install [Homebrew](https://brew.sh/) — the package manager for macOS. It lets you install almost any command-line tool or desktop application with a single command. It will also install the Xcode Command Line Tools, which provide essential developer utilities.

After installation, Homebrew may tell you to add its directory to your `PATH`. This is a standard UNIX concept: the `PATH` environment variable (view it with `echo $PATH`) tells the OS where to look for programs.

> **Note:** In documentation, terminal commands are typically shown in `monospace` or `backticks`. A leading `$` indicates a shell prompt, e.g. `$ echo "hello"`.

Most command-line programs install into standard locations automatically, but some end up in a separate directory that you need to add to your `PATH` manually.

## Shell Configuration

### Bash vs. Zsh

Since macOS Catalina (2019), the default shell is **zsh**. The older default was **bash**. Both are excellent and share most concepts (PATH, aliases, environment variables, scripting basics). The main differences are in startup files and some syntax details. Pick whichever you prefer:

| | **Bash** | **Zsh** |
|---|---|---|
| Install latest | `brew install bash` | Built-in (or `brew install zsh`) |
| Set as default | `chsh -s /opt/homebrew/bin/bash` | `chsh -s /bin/zsh` (already default) |
| Config files | `~/.bash_profile`, `~/.bashrc` | `~/.zprofile`, `~/.zshrc` |
| Silence deprecation warning | `export BASH_SILENCE_DEPRECATION_WARNING=1` | N/A (zsh is already the default) |

> **Recommendation:** If you're new to the command line, sticking with **zsh** (the default) is the simplest option. If you prefer bash or need it for compatibility, install a modern version via Homebrew — the one bundled with macOS is very outdated (v3.2, from 2007) due to licensing.

### Editing Config Files

To add something to your `PATH`, use the `export` command. To avoid repeating this every time you open a terminal, place the command in your shell's startup file:

- **Zsh:** `~/.zshrc` (loaded for every interactive shell)
- **Bash:** `~/.bashrc` (you may need to source it from `~/.bash_profile` — see below)

A few notes on these files:

- `~` is a shortcut for your home folder, e.g. `/Users/max/`
- The `.` prefix makes the file hidden — but it's still just a plain text file you can edit

To edit text files from the terminal, you have two options:

1. **Graphical editor:** Run `open -e <file>` to open it in TextEdit — easiest for beginners.
2. **Terminal editor:** Unless you're already familiar with `vim`, start with `nano` (run `nano <file>`). It works like a regular editor with arrow-key navigation. Keyboard shortcuts are listed at the bottom of the screen — `^` means the Ctrl key, so `^X` means `Ctrl + X` to exit.

**If using bash**, most people auto-load `.bashrc` by adding these lines to `~/.bash_profile`:

```bash
if [ -f $HOME/.bashrc ]; then
        source $HOME/.bashrc
fi
```

**If using zsh**, you can put everything directly in `~/.zshrc` — no extra wiring needed.

Add the Homebrew PATH to your config file (e.g. `~/.zshrc` or `~/.bashrc`):

```bash
export PATH=/opt/homebrew/bin:$PATH
```

Save the file (`Ctrl + X`, confirm with `y`) and reload it so the changes take effect in your current session:

```bash
source ~/.zshrc    # or: source ~/.bashrc
```

Verify that Homebrew works by running `brew --version`. If it prints a version number, you're all set.

### Customising Your Prompt

The prompt (the text before each command line) can show your username, current directory, colours, and more.

**Bash** uses the `PS1` variable:

```bash
# In ~/.bashrc — shows time, username, and current directory in green
export PS1='\t [\u@home] \[\e[0;32m\]/\W/\[\e[m\] $ '
```

**Zsh** uses the `PROMPT` variable with its own escape codes:

```zsh
# In ~/.zshrc — shows username, current directory in green, and a % prompt
PROMPT='%n@%m %F{green}%1~%f %# '
```

Search for "customize bash prompt" or "customize zsh prompt" to learn more about the available escape codes.

### Tab Completion

Tab completion is one of the most useful tricks for efficient terminal navigation: pressing `Tab` auto-completes file, folder, and program names. For example, type `na` and press `Tab` to complete it to `nano` — unless another program also starts with "na", in which case the shell will either complete up to the common prefix, show all matches, or cycle through them.

**Bash** tab completion config (add to `~/.bashrc`):

```bash
# cycle through matches on ambiguous tab completion
[[ $- = *i* ]] && bind TAB:menu-complete

# tab completion: ignore case
[[ $- = *i* ]] && bind 'set completion-ignore-case on'
```

**Zsh** tab completion config (add to `~/.zshrc`):

```zsh
# enable completion system
autoload -Uz compinit && compinit

# case-insensitive completion
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Z}'

# cycle through matches with Tab
zstyle ':completion:*' menu select
```

Lines starting with `#` are comments and have no effect.

The rest of my config contains custom variables and aliases — you'll build up your own collection over time. There are many good "dotfiles" repositories online with useful suggestions for aliases, exports, and functions.

## macOS Defaults & Tweaks

Below are some `defaults` commands and other tweaks I typically run on a fresh Mac. You can look up any of them for more details.

```bash
# copy / paste from the command line without text formatting
defaults write com.apple.Terminal CopyAttributesProfile com.apple.Terminal.no-attributes
```

### Installing Programs with Homebrew

```bash
# essential command-line tools
brew install coreutils moreutils findutils git tmux vim nvim

# search and navigation
brew install ripgrep fd fzf zoxide eza bat tree ag

# system monitoring and utilities
brew install htop bottom hyperfine tldr jq httpie highlight cowsay

# programming tools
brew install go pyenv

# media
brew install yt-dlp
```

> **Note:** `exa` has been replaced by `eza` (actively maintained fork). `youtube-dl` has been replaced by `yt-dlp` (much faster, actively maintained). `autojump` is superseded by `zoxide` (faster, works with both bash and zsh).

Some useful GUI applications can be installed with `brew install --cask`:

```bash
brew install --cask iterm2 docker zoom vlc rectangle
```

### Screenshot Settings

```bash
# save screenshots to a dedicated folder
mkdir -p ~/Desktop/screenshots
defaults write com.apple.screencapture location ~/Desktop/screenshots
defaults write com.apple.screencapture type -string "png"

# Disable shadow in screenshots
defaults write com.apple.screencapture disable-shadow -bool true
```

### Finder & System Settings

```bash
# Enable subpixel font rendering on non-Apple LCDs
defaults write NSGlobalDomain AppleFontSmoothing -int 2

# Show extensions
defaults write NSGlobalDomain AppleShowAllExtensions -bool true

# Disable the warning before emptying the Trash
defaults write com.apple.finder WarnOnEmptyTrash -bool false

# Use list view in all Finder windows by default
# Four-letter codes for the other view modes: `icnv`, `clmv`, `Flwv`
defaults write com.apple.finder FXPreferredViewStyle -string "Nlsv"

# When performing a search, search the current folder by default
defaults write com.apple.finder FXDefaultSearchScope -string "SCcf"

# Disable the warning when changing a file extension
defaults write com.apple.finder FXEnableExtensionChangeWarning -bool false
```

### Dock Settings

```bash
# Show indicator lights for open applications in the Dock
defaults write com.apple.dock show-process-indicators -bool true

# Speed up Mission Control animations
defaults write com.apple.dock expose-animation-duration -float 0.1

# Remove the auto-hiding Dock delay
defaults write com.apple.dock autohide-delay -float 0

# Remove the animation when hiding/showing the Dock
defaults write com.apple.dock autohide-time-modifier -float 0.1

# Automatically hide and show the Dock
defaults write com.apple.dock autohide -bool true
```

### Activity Monitor

```bash
# Customise the Activity Monitor (like Task Manager in Windows)
defaults write com.apple.ActivityMonitor OpenMainWindow -bool true
defaults write com.apple.ActivityMonitor IconType -int 5
defaults write com.apple.ActivityMonitor ShowCategory -int 0
defaults write com.apple.ActivityMonitor SortColumn -string "CPUUsage"
defaults write com.apple.ActivityMonitor SortDirection -int 0
```

### iTerm2 Scrolling

To enable trackpad scrolling in `nano` inside iTerm2, follow [these instructions on Stack Overflow](https://stackoverflow.com/questions/50026644/how-to-use-the-trackpad-to-scroll-in-nano-using-iterm2-the-way-it-works-in-term).

**A note on finding answers:** [Stack Overflow](https://stackoverflow.com/) is an invaluable resource for programming questions — most problems you'll encounter have already been answered there. AI assistants like ChatGPT and Claude are also very useful for quick questions and generating ready-to-use commands.

## HPC Cluster Access (Habrok)

The university's HPC cluster is called **Habrok** (the previous cluster "Peregrine" has been retired). Once you receive confirmation that your account is active, you can log in using `ssh`:

```bash
ssh pnumber@login1.hb.hpc.rug.nl
```

> **Note:** Replace `pnumber` with your actual p-number (e.g. `p123456`). You may need to be on the [university VPN](https://www.rug.nl/society-business/centre-for-information-technology/helpdesk/it-self-service/vpn/?lang=en) if connecting from off-campus.

See the [Habrok documentation](https://wiki.hpc.rug.nl/habrok/start) for full details, including how to submit jobs with the SLURM workload manager.

### SSH Basics

`ssh` connects you to a remote UNIX computer — once connected, everything you type runs on that remote system. Other useful tools for working with remote machines are `scp` and `rsync` (for transferring files).

### SSH Config for Convenience

To avoid typing the full hostname every time, add an entry to `~/.ssh/config`:

```
Host habrok
    HostName login1.hb.hpc.rug.nl
    User pnumber
```

Now you can simply run `ssh habrok` to connect.

### Connecting to Your Own Mac Remotely

You can find your Mac's hostname by running `hostname`, and then connect remotely with `ssh username@hostname`. This requires a [VPN connection to the university network](https://www.rug.nl/society-business/centre-for-information-technology/helpdesk/it-self-service/vpn/?lang=en) when off-campus.

## Python Setup

Python is essential for computational biology. Here's how to set up a clean, manageable Python environment.

### Managing Python Versions with pyenv

[pyenv](https://github.com/pyenv/pyenv) lets you install and switch between multiple Python versions (it's already in the brew install list above):

```bash
brew install pyenv

# install a specific Python version
pyenv install 3.12

# set it as your default
pyenv global 3.12
```

Add the following to your shell config (`~/.zshrc` or `~/.bashrc`) so pyenv works automatically:

```bash
eval "$(pyenv init -)"
```

### Virtual Environments

Always use a **virtual environment** for each project to keep dependencies isolated:

```bash
# create a virtual environment in the current project folder
python -m venv .venv

# activate it
source .venv/bin/activate

# install packages (they stay inside .venv/)
pip install numpy pandas matplotlib

# deactivate when done
deactivate
```

### Conda as an Alternative

If you prefer conda (common in scientific computing), install [Miniforge](https://github.com/conda-forge/miniforge) rather than the full Anaconda — it's lighter and uses the community-driven conda-forge channel by default:

```bash
brew install miniforge
conda init "$(basename "$SHELL")"
```

### Key Packages for Computational Biology

```bash
pip install numpy scipy pandas matplotlib seaborn biopython MDAnalysis jupyter
```

Or with conda:

```bash
conda install numpy scipy pandas matplotlib seaborn biopython mdanalysis jupyter
```

### pip vs. conda

- **pip** installs from PyPI and works with virtual environments. Best for pure-Python packages and most general use.
- **conda** manages both Python packages and non-Python dependencies (C libraries, etc.). Useful when packages have complex compiled dependencies.
- **General advice:** Pick one approach per project and stick with it. Mixing pip and conda in the same environment can cause conflicts.

### Reproducibility

Track your project dependencies so others (and future-you) can recreate the environment:

- **pip:** `pip freeze > requirements.txt` → install with `pip install -r requirements.txt`
- **conda:** `conda env export > environment.yml` → create with `conda env create -f environment.yml`

## AI-Assisted Coding

AI tools have become genuinely useful for day-to-day programming. Here's a quick overview of what's available:

### Chat-Based Assistants

- **ChatGPT** / **Claude** — Great for coding questions, debugging, explaining error messages, and generating boilerplate code. Available as web apps and APIs.
- Use these when you need to understand a concept, write a regex, translate between languages, or debug an error message.

### Code Editors with AI

- **GitHub Copilot** — Inline code completion that runs inside VS Code (and other editors). Suggests code as you type. Free for students via [GitHub Education](https://education.github.com/).
- **Cursor** — A VS Code fork with deep AI integration (chat, inline edits, multi-file changes). Worth trying if you want AI woven into your editor workflow.

### CLI Tools

- **Claude Code** — A terminal-based AI coding assistant that can read your codebase, edit files, and run commands. Useful for larger refactoring tasks or exploring unfamiliar codebases.

### Practical Tips

- **AI excels at:** Boilerplate code, regular expressions, shell one-liners, explaining unfamiliar code, converting between file formats, writing tests.
- **Be cautious with:** Domain-specific science code (e.g. force field parameters, statistical methods), numerical accuracy, and anything where a subtle bug could silently produce wrong results. Always verify AI-generated scientific code.
- **General rule:** AI is a great assistant but not a replacement for understanding what the code does. Use it to speed up tasks you could do yourself, not to skip learning.

> **Note:** This field moves fast. New tools and models appear regularly — check what's current when you read this.

## Closing

That's it for now — this should be enough to get you started. Don't hesitate to ask questions, and do spend some time exploring these commands and settings. The more comfortable you get with these tools early on, the more time you'll save down the road.
