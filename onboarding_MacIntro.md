# Onboarding: Setting Up Your Mac for Computational Biology

## Administrative

- Probably the best thing to start with is figuring out access to your email and myuniversity, you'll need that to do some of the tasks. The login is with p number and password plus two factor authentication, so you need to install the Google Authenticator app ([MFA setup instructions](https://www.rug.nl/society-business/centre-for-information-technology/security/multi-factor-authentication?lang=en)) on your phone.
- Sandra Haan (our secretary, secbc@rug.nl) can be contacted for administrative onboarding and obtaining a university card. Also mail her to make sure you get added to the biotechnology email list. She will probably also give some forms to fill if they didn't yet, and she or Hugo can provide you with an office key.
- Get access to the peregrine high-performance cluster, by requesting here: [Habrok account request](https://rug.topdesk.net/tas/public/ssp/content/serviceflow?unid=84efb629e7484b728dbbf8dfef78fdad). In case that link doesn't work, log in to the [CIT help portal (IRIS)](https://iris.service.rug.nl/) and search for habrok, request account. In the form, just mention that you're a computational PhD doing MD simulations etc.
- Some software can be downloaded from the [university software portal](https://iris.service.rug.nl/tas/public/ssp/content/detail/service?unid=eaf8ada50c7846b792798f1addb69f8e&from=d74117e5-047a-4378-bb03-3510cf8871d1) but other software like Office and Adobe, you just download from their website directly after logging in simply using your @rug.nl email address. (This sometimes changes, again check IRIS for help if that doesn't work.)

## macOS Basics

Some instructions for making your Mac a productive environment. You may already know some of these things, sorry if they should be a bit basic.

- First familiarise yourself with macOS. You have the Dock in the bottom part with currently open and common programs. Probably you want to clean up there a bit and remove programs you're unlikely to use. Other programs you use often can be added to stay there permanently, for both actions just right-click on the icon. As in Windows, you can switch between open windows using `Cmd + Tab`, but you can also click `F3` to see all open windows. You can use "Spotlight" to search for any program or document, just click `Cmd + Space`. "Finder" corresponds to the Windows Explorer and works similarly. Check out the different views, e.g. list vs columns.
- Have a look at the System Settings (Apple symbol in the top left). The menu bar at the top of the screen is always the one for the currently active program (i.e. the program in the foreground).
- To install programs, download the installer for Mac, which comes either as an image file `.dmg` or a real installer. The latter works like on Windows (double click and it does the rest), the former works by opening the `.dmg` and it will show the content and sometimes a link to your Applications folder. All you need to do is drag the symbol of the program into the Applications folder. If the app folder isn't shown in the `.dmg`, just drag it into the Applications folder within Finder. Some programs can also be installed directly from the App Store.

## Terminal Setup

macOS is based on UNIX, and so shares the same core structure with Linux. Thus, you have access to a full-scale UNIX command line such as bash. You will be using a lot of command line in computational biology, so the terminal is one of the most important pieces of software you're going to use. Thus, it's worth to spend some time to get the most out of it. In the following, some hints on how to optimise the command line.

First of all, it's probably a good idea to install [iTerm2](https://iterm2.com/), a newer and modern version of the terminal program. Check out the settings and choose e.g. a style that you like.

## Homebrew

Next, you want to install [Homebrew](https://brew.sh/), which is a package manager that allows you to install nearly all other programs for Mac very easily. It will also install Xcode Command Line Tools for you, which unlocks a bunch of developer tools.

After brew is installed, it may show some messages saying that you have to add the location of the installed program to your PATH. This is a very common procedure you have to get used to on UNIX. The `PATH` environment variable (which you can look at by typing `echo $PATH` in the command line) informs the OS about all the locations where it should look for software.

> **Note:** Terminal commands are commonly shown in `monospace` or indicated using `backticks`. For bash, you often see a dollar sign to indicate a terminal prompt, e.g. `$ echo`.

Many command line programs will be installed as subfolders within the default locations, but others install in a separate folder and that folder then needs to be added to the PATH.

## Shell Configuration

To add something to your PATH, you can use the `export` command. To avoid having to do this every time you open a terminal, you can add the export command in a special file that is always executed when you open a new terminal — the files `~/.bashrc` and `~/.bash_profile`.

- `~` is a shortcut for your home folder, e.g. `/Users/max/`
- The `.` before the file/folder name makes it a hidden system file
- Nevertheless, they are just simple text files that you can edit

To edit text files, you have two options:

1. Use a simple graphical text editor (which you can fire up from the command line using `open -e <file>` — this is mostly the way for noobies)
2. Use a command line editor of varying sophistication. Unless you're already familiar with the "pro" editors like `vim`, you can start with `nano` (open with `nano <file>`). It looks like a regular editor and you can use the arrow keys to navigate the pointer, but it also has several keyboard shortcuts explained at the bottom. `^` indicates the Ctrl key, so to close the program e.g., you would press `Ctrl + X`.

Most people auto-load the file `.bashrc` by adding these three lines to the `.bash_profile`:

```bash
if [ -f $HOME/.bashrc ]; then
        source $HOME/.bashrc
fi
```

The rest of startup instructions you can now write in `.bashrc`, and for brew, open `.bashrc` and write on a separate line whatever brew suggested, e.g.:

```bash
export PATH=/opt/homebrew/bin:$PATH
```

Save the file (`Ctrl + X`, confirm with `y`), and source it via `source ~/.bashrc` (to make the effects come into effect in the current window). Now try and see if brew works, by typing `brew --version`, which should not yield an error if you set up the PATH correctly. If it works, install your first program, which is bash itself (it is an outdated version on new Macs):

```bash
brew install bash
```

Make bash your default shell (unless you really want to use zsh instead, that is) with this command:

```bash
chsh -s /bin/bash
```

### Customising Your Prompt

Regarding the `.bashrc`, there are many other useful things you would want to write in this file as well. Generally you'd find out about them by googling a certain issue or something that annoys you. Nearly everything can also be customised. For instance, the prompt (the text that precedes every line in the command line) can be changed to always show the user name and the current folder. You can also add colors there.

> **Example prompt:** The prompt shows time, username, and the current directory in green.

You can achieve that by having this line in `.bashrc`:

```bash
export PS1='\t [\u@home] \[\e[0;32m\]/\W/\[\e[m\] $ '
```

If you google / ChatGPT something like "unix default prompt", you'll find more info on that.

### Tab Completion

You can also modify the way the tab completion works. Tab completion is the most useful trick for efficient navigation in the command line: pressing the `Tab` key auto-completes file, folder, and program names.

For instance, if you want to use `nano`, you could just type `na` and press `Tab`, it will autocomplete to `nano`, unless you have another program installed that starts with "na". Should that be the case, the terminal behaves in one of several ways: it will either complete the letters up to the point where the names differ, or it will show the ambiguous programs (sometimes for that to happen you have to press `Tab` twice), or it will autocomplete to the first one, and if you hit `Tab` again, it will go to the second, etc.

I like the cycling option and configure that so:

```bash
# show ambiguous file names upon tab completion, don't complete
#[[ $- = *i* ]] && bind 'set show-all-if-ambiguous on'

# cycle through files upon ambiguous tab completion
[[ $- = *i* ]] && bind TAB:menu-complete

# tab completion: ignore case
[[ $- = *i* ]] && bind 'set completion-ignore-case on'
```

Note that all lines starting with `#` are comments and have no effect.

The rest of my bashrc has some custom variables and shortcuts, you will probably come up with your own in due time. There are a lot of useful other suggestions for customisation here, just click through the alias, export, and function files and copy paste as you see fit. There are also a lot of useful things in the setup file, here, rather than adding those lines to the bashrc, you'd execute them just once on the terminal directly.

## macOS Defaults & Tweaks

Some commands I usually run on a new Mac (googling / ChatGPT-ing the command should yield more info):

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

Change an iTerm setting to allow scrolling in `nano` by changing the setting as described here: [Enable trackpad scrolling in nano (Stack Overflow)](https://stackoverflow.com/questions/50026644/how-to-use-the-trackpad-to-scroll-in-nano-using-iterm2-the-way-it-works-in-term).

By the way, do you already know about stackoverflow? It's your BEST FRIEND to answer any question about absolutely anything related to IT/programming. Just google your problem, and in 95% of cases you'll find the answer on stackoverflow. As mentioned, chatgpt is a useful alternative, I use it now quite a lot for questions or copy paste-ready complex command lines.

## HPC Cluster Access

Lastly, as soon as you have access to the habrok cluster confirmed by email, you can log in with `pnumber@login2.hb.hpc.rug.nl` and your normal password using `ssh` on the command line. Check the [Habrok documentation](https://wiki.hpc.rug.nl/habrok/start) here.

`ssh` is a program that allows you to connect to a different UNIX computer. As soon as you SSH somewhere, it's as if you were now sitting on a keyboard on that other computer (or server), everything you type now happens on that remote system. Your other friends to interact with remote systems are `scp` and `rsync` (to transfer files).

You'll probably need to find out your own hostname — type `hostname` and you'll see it. Now you can connect from any UNIX terminal in the world to your own Mac with SSH: just type `ssh username@hostname`, replacing `username` with your username (e.g. `max` in my case, check the folder name in `~`), and replacing `hostname` with the output of the `hostname` command. Because of university security settings, this only works if you use a VPN to connect to the university network. Check out [how to set up VPN](https://www.rug.nl/society-business/centre-for-information-technology/helpdesk/it-self-service/vpn/?lang=en).

## Closing

Ok that's it for the moment, should get you going. Don't hesitate to ask or spend some time looking up details about some of these commands and settings. You'll spend a lot of time with these tools, and the more you become familiar with them at the beginning, the more time it will save you later.
