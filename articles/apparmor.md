# AppArmor

AppArmor (Application Armor) é um Linux Security Module (LSM) que permite restringir programas de acessar recursos e executar ações.  
O AppArmor baseia-se em profiles, cada perfil define exatamente o que um programa pode fazer.

```bash
sudo pacman -S --needed audit apparmor
```

Utilize `bootctl list` para identificar a forma de inicialização do _kernel_ e como editar o [kernel parameter](https://wiki.archlinux.org/title/Kernel_parameters).

Caso seja por Unified Kernel Image (UKI), edite o `/etc/kernel/cmdline` e adicione a seguinte instrução no final do arquivo.

```plaintext
lsm=landlock,lockdown,yama,integrity,apparmor,bpf
```

```bash
sudo mkinitcpio -P

sudo systemctl enable --now apparmor.service
sudo systemctl enable --now auditd

reboot
```

## Cheat Sheet

```bash
aa-enabled

sudo aa-status              # Lista os modos dos perfis e processos

sudo aa-complain programa
sudo aa-enforce programa

sudo aa-autodep programa    # verifica os binários do programa e cria um perfil sem regras
                            # Esse perfil é adicionado em complain mode automaticamente

sudo aa-logprof             # Com base nos logs de processos em complain mode, oferece um menu interativo para atualizar o profile do processo
                            # Logs são salvos em /var/log/audit/audit.log
```
