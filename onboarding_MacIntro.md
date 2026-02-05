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

To add something to your `PATH`, use the `export` command. To avoid repeating this every time you open a terminal, place the command in a shell startup file that runs automatically — such as `~/.bashrc` and `~/.bash_profile`.

- `~` is a shortcut for your home folder, e.g. `/Users/max/`
- The `.` prefix makes the file hidden — but it's still just a plain text file you can edit

To edit text files from the terminal, you have two options:

1. **Graphical editor:** Run `open -e <file>` to open it in TextEdit — easiest for beginners.
2. **Terminal editor:** Unless you're already familiar with `vim`, start with `nano` (run `nano <file>`). It works like a regular editor with arrow-key navigation. Keyboard shortcuts are listed at the bottom of the screen — `^` means the Ctrl key, so `^X` means `Ctrl + X` to exit.

Most people auto-load the file `.bashrc` by adding these three lines to the `.bash_profile`:

```bash
if [ -f $HOME/.bashrc ]; then
        source $HOME/.bashrc
fi
```

Any additional startup commands go in `.bashrc`. For example, to add Homebrew to your `PATH`, add the line it suggested:

```bash
export PATH=/opt/homebrew/bin:$PATH
```

Save the file (`Ctrl + X`, confirm with `y`) and reload it with `source ~/.bashrc` so the changes take effect in your current session. Verify that Homebrew works by running `brew --version`. If it prints a version number, you're all set. A good first package to install is an up-to-date version of bash (the one bundled with macOS is outdated):

```bash
brew install bash
```

To make bash your default shell (instead of zsh), run:

```bash
chsh -s /bin/bash
```

### Customising Your Prompt

There are many other useful things you can add to your `.bashrc`. You'll discover them over time as you search for solutions to minor annoyances — nearly everything is customisable. For instance, the prompt (the text before each command line) can show your username, current directory, and even colours.

> **Example prompt:** The prompt shows time, username, and the current directory in green.

You can achieve that by having this line in `.bashrc`:

```bash
export PS1='\t [\u@home] \[\e[0;32m\]/\W/\[\e[m\] $ '
```

Search for "customize bash prompt" to learn more about the available escape codes.

### Tab Completion

Tab completion is one of the most useful tricks for efficient terminal navigation: pressing `Tab` auto-completes file, folder, and program names. For example, type `na` and press `Tab` to complete it to `nano` — unless another program also starts with "na", in which case the shell will either complete up to the common prefix, show all matches (sometimes requiring a second `Tab` press), or cycle through them.

I prefer the cycling behaviour, which you can configure like this:

```bash
# show ambiguous file names upon tab completion, don't complete
#[[ $- = *i* ]] && bind 'set show-all-if-ambiguous on'

# cycle through files upon ambiguous tab completion
[[ $- = *i* ]] && bind TAB:menu-complete

# tab completion: ignore case
[[ $- = *i* ]] && bind 'set completion-ignore-case on'
```

Lines starting with `#` are comments and have no effect.

The rest of my `.bashrc` contains custom variables and aliases — you'll build up your own collection over time. There are many good "dotfiles" repositories online with useful suggestions for aliases, exports, and functions.

## macOS Defaults & Tweaks

Below are some `defaults` commands and other tweaks I typically run on a fresh Mac. You can look up any of them for more details.

```bash
# copy / paste from the command line without text formatting
defaults write com.apple.Terminal CopyAttributesProfile com.apple.Terminal.no-attributes

# silence an annoying warning on macOS to use zsh
export BASH_SILENCE_DEPRECATION_WARNING=1
```

### Installing Programs with Homebrew

```bash
# install important programs
brew install coreutils moreutils findutils htop ag tree exa cowsay bat bottom vim tldr jid jq go libpq hyperfine highlight nvim youtube-dl autojump httpie pyenv ripgrep git tmux kap

# you can also use brew to install common programs with graphical interface, e.g:
brew install --cask docker zoom vlc cleanmymac
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

## HPC Cluster Access

Once you receive confirmation that your Habrok account is active, you can log in using `ssh` on the command line:

```bash
ssh pnumber@login2.hb.hpc.rug.nl
```

See the [Habrok documentation](https://wiki.hpc.rug.nl/habrok/start) for full details.

`ssh` connects you to a remote UNIX computer — once connected, everything you type runs on that remote system. Other useful tools for working with remote machines are `scp` and `rsync` (for transferring files).

You can also find out your own Mac's hostname by running `hostname`, and then connect to your Mac remotely with `ssh username@hostname` (replacing both with your actual values). Note that this requires a [VPN connection to the university network](https://www.rug.nl/society-business/centre-for-information-technology/helpdesk/it-self-service/vpn/?lang=en) when off-campus.

## Closing

That's it for now — this should be enough to get you started. Don't hesitate to ask questions, and do spend some time exploring these commands and settings. The more comfortable you get with these tools early on, the more time you'll save down the road.
