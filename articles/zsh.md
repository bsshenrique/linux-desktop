# ZSH

Instale o `zsh`.

```bash
sudo pacman -S --needed zsh
```

Instale o [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh).

> [!TIP]
> Ao usar o `Oh My Zsh` sempre realize a instalação de temas e _plugins_ por meio dele.
>
> É possível criar um script para atualizar os temas e _plugins_ instalados.  
> Basta um `git pull` nos diretórios em `~/.oh-my-zsh/custom/plugins/` e `~/.oh-my-zsh/custom/themes/`.

Instale:

- [Powerlevel10k](https://github.com/romkatv/powerlevel10k) e as fontes necessárias
- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

Edite o `.zshrc` e altere o comportamento de sugestão após colar.

```bash
# User configuration
ZSH_AUTOSUGGEST_CLEAR_WIDGETS+=(bracketed-paste)
```
