# pacman

Habilite as opções `Color` e `VerbosePkgLists`.

```bash
sudo sed -i 's/^#\(Color\|VerbosePkgLists\)/\1/' /etc/pacman.conf
```

## Cheat Sheet

```bash
du -hs /var/cache/pacman/pkg/   # Verifica o espaço consumido pelo cache dos pacotes

sudo pacman -Rs pacote          # Remove um pacote e suas dependências não exigidas por outros pacotes
sudo pacman -Rns pacote         # Remove também os arquivos de configuração

sudo pacman -S pacote           # Instala um pacote
sudo pacman -Sc                 # Remove do cache versões antigas dos pacotes instalados
sudo pacman -Scc                # Remove todos os pacotes do cache
pacman -Ss pacote               # Busca um pacote no repositório remoto
sudo pacman -Syu                # Atualiza o sistema e todos os pacotes

pacman -Qs pacote               # Lista um pacote instalado
pacman -Qdqt                    # Lista dependências que não são requeridas por nenhum pacote
sudo pacman -R $(pacman -Qdqt)  # Remove os pacotes listados acima
```
