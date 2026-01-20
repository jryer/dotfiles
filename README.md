Repo for setting up new devices since I always forget what I like to have

# Setting Up New Device

## Git

### Install

Run `git --version` -- Follow directions to install Xcode Command Line Tools

### SSH Keys (if needed)

adding-a-new-ssh-key-to-your-github-account

- Generate ssh key: `ssh-keygen -t ed25519 -C "jonathan.ryer@gmail.com"`
- Start ssh-agent: `eval "$(ssh-agent -s)"`
- Create ssh-agent config: `touch ~/.ssh/config`
- Add contents into created config file:

```
Host github.com
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id_ed25519
```

- Add provide key to ssh-agent: `ssh-add --apple-use-keychain ~/.ssh/id_ed25519`
- Copy public key to clipboard: `pbcopy < ~/.ssh/id_ed25519.pub`
- Go to Settings > Access > SSH and GPG keys > New SSH key

References

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/

### Rebase Editor

https://github.com/sjurba/rebase-editor (requires yarn or npm)

## Homebrew

- `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
- `(echo; echo 'eval "$(/opt/homebrew/bin/brew shellenv)"') >> "$HOME/.zprofile"`
- `eval "$(/opt/homebrew/bin/brew shellenv)"`

References https://brew.sh

## Terminal

### iTerm2

https://iterm2.com
Add Default.json to profiles

### Powerline Patched Font

Follow directions here: https://github.com/powerline/fonts?tab=readme-ov-file#quick-installation

### Oh my zsh

https://ohmyz.sh/#install
https://github.com/agnoster/agnoster-zsh-theme?tab=readme-ov-file
ZSH_THEME="agnoster"

### Node, npm, yarn

- https://nodejs.org/en
- npm install --global yarn
- Add to zshrc export PATH="$(yarn global bin):$PATH"

## Languages

### Java

JDK https://www.oracle.com/java/technologies/downloads/
Visual Code Extension Pack https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack
echo export "JAVA_HOME=\$(/usr/libexec/java_home)" >> ~/.zshrc

### Python

uv https://docs.astral.sh/uv/getting-started/installation/

### Podman

https://podman.io - Follow directions

### Postgres

Create volume

```
podman volume create pg-local-data
```

Create pod

```
podman pod create \
  --name pg-local-pod \
  -p 127.0.0.1:5432:5432
```

Generate credentials and put them in a file

```
mkdir -p ~/.config

umask 077
cat > ~/.config/pg-local.env <<EOF
POSTGRES_USER=dev
POSTGRES_PASSWORD=$(openssl rand -base64 32)
POSTGRES_DB=dev
EOF
```

Run Postgres inside pod

```
podman run -d \
  --name pg-local \
  --pod pg-local-pod \
  --env-file ~/.config/pg-local.env \
  --mount type=volume,source=pg-local-data,target=/var/lib/postgresql/data \
  --health-cmd "pg_isready -U dev -d dev" \
  --health-interval 5s \
  --health-timeout 3s \
  --health-retries 10 \
  postgres:16
```
