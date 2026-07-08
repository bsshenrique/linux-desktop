# Linux Desktop

Linux Desktop é um repositório descrevendo o que considero para um bom uso do Linux como desktop.

São exigência inegociáveis para a escolha de um sistema operacional:

- Liberdade
- Privacidade
- Segurança

Não aceite:

- Assistentes de IA obrigatórios
- Atualizações obrigatórias
- Políticas de empresas _slop_ que estragam qualquer coisa que tocam
- Porcarias inúteis como _bloatware_
- Telemetria obrigatória

Diante dessas considerações, a minha opção favorita é o [Arch Linux](https://archlinux.org/).

Se você se preocupa com os critérios acima, é bem provável que se identifique com o que está descrito nesse repositório.

Deixo também um alerta para que você sempre seja minimalista, atente-se a tudo e mantenha-se o mais simples possível.  
Por exemplo, existem desserviços ao ecossistema Linux, como esse [commit](https://github.com/systemd/systemd/pull/40954) que futuramente pode ser utilizado para fins autoritários.

Também incentivo a realizar doações para projetos open source, crie o hábito de ajudar quem te ajuda.

## Tópicos

- [Arch Linux](#arch-linux)
  - [Instalação](#instalação)
  - [Pós Instalação](#pós-instalação)
    - [Pacotes](#pacotes)
    - [Configuração](#configuração)
  - [Administração](#administração)

## Arch Linux

### Instalação

Por muito tempo realizei a instalação manual, porém, hoje prefiro a simplicidade oferecida pelo [archinstall](https://wiki.archlinux.org/title/Archinstall).

### Pós Instalação

#### Pacotes

Remova os pacotes desnecessários e instale os essenciais.

```bash
sudo pacman -Rs htop vim

sudo pacman -S --needed \
btop \
btrfs-progs \
curl \
fastfetch \
fwupd \
git \
gnome \
gnome-tweaks \
less \
lm_sensors \
nano \
noto-fonts \
noto-fonts-emoji \
pipewire \
pipewire-alsa \
pipewire-audio \
pipewire-jack \
pipewire-pulse \
power-profiles-daemon \
reflector \
smartmontools \
sudo \
timeshift \
wget \
which \
wireplumber \
xdg-utils \
zip

flatpak install flathub -y \
  com.github.tchx84.Flatseal \
  com.mattjakeman.ExtensionManager \
  io.missioncenter.MissionCenter \
  org.mozilla.firefox

sudo reflector \
  --country BR,CL \
  --latest 16 \
  --protocol https \
  --save /etc/pacman.d/mirrorlist \
  --sort rate \
  --verbose
```

#### Configuração

Instale as extensões:

- [Cliboard Indicator](https://extensions.gnome.org/extension/779/clipboard-indicator)
- [Removable Drive Menu](https://extensions.gnome.org/extension/7/removable-drive-menu)

Siga os artigos:

- [apparmor](./articles/apparmor.md)
- [nftables](./articles/nftables.md)
- [zsh](./articles/zsh.md)

Defina o `nano` como editor principal em `~/.zshrc`.

```plaintext
export EDITOR="nano"
export VISUAL="nano"
```

Configure o `~/.nanorc`.

```bash
cat << 'EOF' > ~/.nanorc
include /usr/share/nano/*.nanorc

set autoindent
set constantshow
set softwrap
set tabsize 4
set tabstospaces
EOF
```

Habilite o TRIM em unidades criptografadas com o LUKS2.  
Existe um possível risco de segurança associado [Discard/TRIM](<https://wiki.archlinux.org/title/Dm-crypt/Specialties#Discard/TRIM_support_for_solid_state_drives_(SSD)>) em partições criptografadas.

```bash
sudo fdisk -l
lsblk

sudo cryptsetup luksDump /dev/sdaX

sudo cryptsetup --allow-discards --persistent refresh root

# Valide se a flag foi aplicada
sudo cryptsetup luksDump /dev/sdaX
```

Edite o `/etc/fstab` e adicione flag `discard=async` em todos os _filesystem_ do tipo `btrfs`.

Instale o `podman`.

```bash
sudo pacman -S podman
```

Configure a busca de repositórios em `/etc/containers/registries.conf.d/10-unqualified-search-registries.conf`.

```plaintext
unqualified-search-registries = ["docker.io"]
```

Caso o áudio apresente pequenos estouros ou zumbidos, siga as instruções [aqui](https://wiki.archlinux.org/title/Advanced_Linux_Sound_Architecture#Cards_and_modules) para identificar o módulo de som em uso.  
Depois, siga as instruções [aqui](https://wiki.archlinux.org/title/Power_management#Audio) para identificar o parâmetro para desabilitar o _power saving_.  
E por fim, realize as alterações descritas [aqui](https://wiki.archlinux.org/title/Advanced_Linux_Sound_Architecture/Troubleshooting#Power_saving).

Após reiniciar, confirme a alteração.

```bash
cat /sys/module/snd_hda_intel/parameters/power_save
```

Detecte os sensores de módulos do _kernel_.

```bash
sudo sensors-detect --auto
```

### Administração

Consulte os artigos:

- [Logs](./articles/logs.md)
- [pacman](./articles/pacman.md)
