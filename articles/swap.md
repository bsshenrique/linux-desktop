# Swap

_Swap space_ destina-se a armazenar páginas de memória virtual ou permitir a suspensão para disco.

Quando utilizado para armazenar páginas de memória virtual, ele funciona como uma extensão da memória física, realizando _swapping_ quando não há mais RAM disponível.  
_Swapping_ é o processo em que o sistema operacional move temporariamente páginas de memória virtual inativas ou usadas com menos frequência da RAM para o local indicado como _swap_.

Existem diversas maneiras de uso de um _swap space_.  
Exemplos comuns:

```plaintext
swap partition    Partição
swap file         Arquivo em disco
zram              Módulo do kernel capaz de criar um dispositivo de bloco compactado em RAM
```

> [!IMPORTANT]
> Não configure _swap_ apenas por configurar, primeiro valide os pontos positivos, negativos e se realmente é necessário.
>
> Por exemplo, _swap_ em `zram` ocupa parte da RAM e pode elevar o consumo de CPU devido à compressão e gerar impacto em processadores menos potentes.  
> Já uma partição _swap_ pode nunca ser utilizada em sistemas com grande quantidade de RAM.

Se estiver com dúvidas, siga com uma abordagem de fácil configuração, como um [swap file](https://wiki.archlinux.org/title/Swap#Swap_file_creation) com algo como 4G.  
Configure também o [swappiness](https://wiki.archlinux.org/title/Swap#Swappiness).
