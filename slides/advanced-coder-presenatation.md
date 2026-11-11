# **Advanced Coder.ddev.com Usage**

![DDEV Logo](images/ddev-logo.svg)

Cloud DDEV workspaces in your browser

Note:
Agenda: what coder.ddev.com is, Drupal contribution flow, ways to get into a workspace, freeform template, Claude Code, resources.

---

## What is coder.ddev.com?

* Free, experimental cloud DDEV service built on [Coder](https://coder.com)
* Each workspace: isolated container, Docker-in-Docker (Sysbox), DDEV, VS Code
* Ready in about a minute, no local install
* Sign in with GitHub at [coder.ddev.com](https://coder.ddev.com/login)
* Start page: [start.coder.ddev.com](https://start.coder.ddev.com)
* Docs: [docs.coder.ddev.com](https://docs.coder.ddev.com)

Note:
Experimental: no guarantees of data retention or uptime. Keep work in Git.

---

## WHY coder.ddev.com?

* Original gitpod/drupalpod replacement for contribution
* External machine, more flexibility, not dependent on laptop
* Sharing and collaborating with others
* AI in completely isolated environment
* Claude can run so many ways
* Geographical and network issues

---

## Getting Access

Sign in with GitHub. You get in if you're a member of:

* An organization that [sponsors DDEV](https://ddev.com/sponsor/) at $100+/month
  * All members of org can sign in

Otherwise, request access:

* Open an issue at [github.com/coder-ddev-com/access-requests](https://github.com/coder-ddev-com/access-requests)
* Include your GitHub username and how you'll use it

Note:
Once approved you're added to the coder-ddev-com org and can sign in. Coder gets read-only access to email, public profile, and org membership; no code access.

---

## Repo and Docs

* Code: [github.com/ddev/coder-ddev](https://github.com/ddev/coder-ddev)
  * Templates: `freeform`, `drupal-core`, `drupal-contrib`
  * Shared image in `image/`, deploy via `Makefile`
* Docs: [docs.coder.ddev.com](https://docs.coder.ddev.com/)
  * Getting started, Freeform, Drupal quickstart
  * Workspaces: lifecycle, CLI, editors and SSH, Claude Code
  * Self-hosting your own instance

Note:
It's all open source. Anyone can run their own DDEV-capable Coder instance using the self-hosting docs.

---

## Three Templates

| Template | Use it for |
| -------- | ---------- |
| `drupal-core` | Core issues, clean core checkout |
| `drupal-contrib` | Any contrib module or theme |
| `freeform` | Your own DDEV projects, one or many |
<!-- .element: style="font-size: 0.8em" -->

Note:
drupal-core uses amateescu/ddev-drupal-dev; drupal-contrib uses ddev/ddev-drupal-contrib. Freeform is the general-purpose one.

---

# Drupal Contribution

Replacing Gitpod / DrupalPod

---

## The Drupal Issue Picker

[start.coder.ddev.com/drupal-issue](https://start.coder.ddev.com/drupal-issue)

1. Paste a drupal.org issue URL, issue number, or project name
2. Picker detects core vs. contrib and the Drupal version
3. Issue fork branch is checked out
4. Composer deps installed, site installed
5. Open **DDEV Web** or **VS Code**

Note:
Demo: <https://www.drupal.org/project/drupal/issues/3580705> (core) or <https://www.drupal.org/project/languages_dropdown/issues/3581165> (contrib).
First creation takes several minutes for Drupal templates; later starts are faster.
Don't click Cancel at "Confirm and Create" — start over in the picker instead. Watch the Logs tab.

---

## Why This Replaces DrupalPod

* Hand someone an issue URL, they get a working site with the branch ready
* Good for contribution days: no local Docker, no setup
* Full DDEV, so the same commands as local
* Free for contributors, run by the DDEV project

---

## Inside a Drupal Workspace

* Core in `~/drupal-core/`, contrib in `~/<project_name>/`
* Site login: `admin` / `admin`

```bash
ddev drush uli
ddev phpunit core/modules/node
ddev phpcs   # also phpstan, eslint
git push issue HEAD
```

Note:
To push, add your workspace key (`coder publickey` or coder.ddev.com/settings/ssh-keys) to your drupal.org account.

---

# Getting Into a Workspace

---

## Web-Based Access

From the workspace page in the dashboard:

* **VS Code** (VS Code for Web, in a browser tab)
* **Terminal** (web terminal)
* **DDEV Web**, **Mailpit**, **xhgui** app buttons
* Start / Stop / Delete, Settings → Parameters, Logs

Note:
Demo the dashboard. Show the app buttons and the logs tab.

---

## VS Code for Web

* Full VS Code in the browser, nothing installed locally
* Integrated terminal: `ddev`, `docker`, `git`, `claude`
* Install extensions from the Extensions view; they persist
* Choose default extensions in workspace settings

---

## Local VS Code

* Dashboard: **VS Code Desktop** button
  * Opens local VS Code via the Coder extension
* Or from CLI: `coder open vscode my-site`
* Or Remote-SSH to host `my-site.coder`
* Xdebug works as with local DDEV
* JetBrains: Toolbox Coder plugin or Gateway over SSH

Note:
Local VS Code is what I use for daily work. Keybindings, settings, and extensions are yours.

---

## Coder CLI: Setup

```bash
brew install coder            # or curl -L https://coder.com/install.sh | sh
coder login https://coder.ddev.com
coder list
coder create --template freeform my-site
coder stop my-site
coder start my-site
```

Note:
Windows: winget install Coder.Coder. `coder whoami` to check login.

---

## Coder CLI: SSH

```bash
coder ssh my-site                       # interactive shell
coder ssh my-site -- ddev list          # one command
coder ssh -A my-site                    # forward your SSH agent

coder config-ssh --yes                  # write ~/.ssh/config entries
ssh my-site.coder
scp dump.sql.gz my-site.coder:~/my-site/
rsync -av ./files/ my-site.coder:~/my-site/web/sites/default/files/
```
<!-- .element: style="font-size: 0.45em" -->

Note:
Host names are `<workspace>.coder`. Re-run `coder config-ssh` after creating new workspaces.
Port forward: coder port-forward my-site --tcp 3306:32789

---

## Example: Import a Database

```bash
scp ~/Downloads/dump.sql.gz my-site.coder:~/my-site/
coder ssh my-site -- env -C /home/coder/my-site \
  ddev import-db --file=dump.sql.gz
```

And back out:

```bash
scp my-site.coder:~/my-site/backup.sql.gz .
```

---

# The Freeform Template

Any DDEV project, as many as you want

---

## A Basic Website

Create a workspace named `hello` from [the freeform template](https://coder.ddev.com/templates/coder/freeform/workspace?mode=manual), then:

```bash
mkdir -p ~/hello/public && cd ~/hello
echo '<h1>Hello from coder.ddev.com</h1>' > public/index.html
ddev config --project-name=hello --project-type=php --docroot=public
ddev coder-setup
ddev start
```

Site: `https://hello--hello--<username>.coder.ddev.com`

Note:
Click the "hello" app button or run `ddev launch`. Note: *.ddev.site URLs don't work in the cloud.

---

## Bring Your Own Project

```bash
git clone https://github.com/your-org/my-site.git ~/my-site
cd ~/my-site
ddev config --project-name=my-site
ddev coder-setup
ddev start
```

Works with anything DDEV supports: Drupal, WordPress, Laravel, TYPO3, Craft, Node, static…

---

## Freeform: Special Setup

* **DDEV project names** parameter: `shop,blog`
  * Must exactly match each DDEV project name
  * Each gets an app button + Mailpit + xhgui
* Run `ddev coder-setup` once per project
* **Public Sharing**: off by default; on = anyone with the link
* Change later: Settings → Parameters, then restart

Note:
coder-setup configures routing through ddev-router; each project gets its own hostname, no port numbers. It warns if the project isn't in the project-names list.
For CLI create with commas, use --rich-parameter-file params.yaml.

---

## Freeform: Timeouts and Lifecycle

* Auto-stop after inactivity (freeform default: 24h)
  * `coder schedule show my-site`
  * `coder schedule extend my-site 2h`
* Idle 7 days → warning email; 7 more days → **deleted**
* Stop keeps `/home/coder`, databases, Docker volumes, Homebrew
* Stop loses processes, `/tmp`, `apt-get` packages
* `~/.coder-startup.sh` runs on every start

Note:
After a start, DDEV projects aren't restarted automatically. Use a startup hook:
cd ~/my-site && ddev start
Install tools with brew or npm -g so they persist.

---

# Claude Code in a Workspace

---

## Four Ways to Use Claude Code

| Where | Setup |
| ----- | ----- |
| Terminal (`claude`) | None, already installed |
| VS Code extension | Install "Claude Code" by Anthropic |
| Remote control (claude.ai/code, mobile) | Workspace setting |
| Self-hosted runner (freeform only) | Workspace setting + secret |
<!-- .element: style="font-size: 0.7em" -->

Note:
Login once with `claude`; it persists in home directory. Pro/Max/Team/Enterprise or ANTHROPIC_API_KEY. Remote control needs a subscription.

---

## Claude as a Remote Environment

* Claude runs *in the workspace*, with its Docker and DDEV
* Your laptop isn't involved: close the lid, keep going
* Use `tmux new -s claude` so sessions survive a closed tab
* Or `coder ssh my-site`, then `cd ~/my-site && claude`
* Add a `CLAUDE.md`: run PHP/Composer/Drush via `ddev`

---

## Remote Control

* Enable **Claude Code remote control** in workspace settings, restart
* First use: **Claude Code** app button, log in, trust workspace
* Session appears on [claude.ai/code](https://claude.ai/code) under the workspace name
* `claude-here` in a project directory switches to that project
* Optional: skip permission prompts (`--dangerously-skip-permissions`)

Note:
Disconnects after about 10 minutes offline; restarts on workspace start or by clicking the app button.

---

## Self-Hosted Runner (Freeform)

* claude.ai/code sessions run in your freeform workspace
* Create a self-hosted environment on claude.ai, save its secret:

```bash
mkdir -p ~/.claude-runner
cat > ~/.claude-runner/environment-secret   # paste, Enter, Ctrl-D
chmod 600 ~/.claude-runner/environment-secret
```

* 1–4 concurrent sessions; `claude-sessions` lists them
* Dedicate the workspace to the runner

Note:
Can't be combined with remote control. Runner rewrites ~/.gitconfig before each session. Runner logs in tmux session claude-runner and `/tmp/claude-runner-N.log`.

---

## Resources

* Start: [start.coder.ddev.com](https://start.coder.ddev.com)
* Dashboard: [coder.ddev.com](https://coder.ddev.com)
* Docs: [docs.coder.ddev.com](https://docs.coder.ddev.com/)
* Repo and issues: [github.com/ddev/coder-ddev](https://github.com/ddev/coder-ddev)
* Announcement: [ddev.com/blog/coder-ddev-com-announcement](https://ddev.com/blog/coder-ddev-com-announcement/)
* Access requests: [github.com/coder-ddev-com/access-requests](https://github.com/coder-ddev-com/access-requests)
* Coder CLI: [coder.com/docs/install](https://coder.com/docs/install)
* Help: [DDEV Discord](https://ddev.com/s/discord), Drupal Slack `#ddev`
<!-- .element: style="font-size: 0.75em" -->

---

# Questions?

![DDEV Logo](images/ddev-logo.svg)
