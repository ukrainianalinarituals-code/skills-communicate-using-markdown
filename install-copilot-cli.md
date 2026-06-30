# Installing GitHub Copilot CLI

<img alt="CoderCat (Octodex)" src="https://octodex.github.com/images/codercat.jpg" width="120" align="right">

GitHub Copilot CLI brings AI-powered assistance directly to your terminal. Use it to explain shell commands, suggest commands, and get help with your workflow — all from the command line.

## Prerequisites

Before installing, make sure you have:

- [ ] An active [GitHub Copilot subscription](https://github.com/features/copilot)
- [ ] Node.js 22 or later (for npm installation)
- [ ] PowerShell v6 or higher (Windows only)
- [ ] Organization admin has enabled Copilot CLI (if using a GitHub organization)

## Installation Methods

### Using npm (all platforms)

```bash
npm install -g @github/copilot
```

If you have `ignore-scripts=true` in your `~/.npmrc`, use:

```bash
npm_config_ignore_scripts=false npm install -g @github/copilot
```

### Using Homebrew (macOS and Linux)

```bash
brew install --cask copilot-cli
```

### Using WinGet (Windows only)

```bash
winget install GitHub.Copilot
```

### Using the install script (macOS and Linux)

```bash
curl -fsSL https://gh.io/copilot-install | bash
```

## After Installation

Once installed, launch Copilot CLI and authenticate:

1. Open your terminal and run `copilot`
1. On first launch, run `/login` to authenticate
1. Follow the prompts to sign in with your GitHub account

## Next Steps

- [ ] Explore `copilot explain` to get explanations for shell commands
- [ ] Try `copilot suggest` to get command suggestions
- [ ] Review the [full Copilot CLI documentation](https://docs.github.com/en/copilot/how-tos/copilot-cli)
