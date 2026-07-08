# Logs

O diretório `/var/log` tem como propósito armazenar _logs_ do sistema e de serviços.  
De maneira geral, os _logs_ no Linux são acessados por:

```bash
sudo dmesg                        # dmesg, display message, buffer de logs do kernel
sudo dmesg | grep -i "error"

journalctl                        # Ferramenta do systemd para visualizar logs
journalctl _UID=id                # Logs por ID de usuário

journalctl --disk-usage
sudo journalctl --vacuum-size=x   # Limpa os logs mais antigos excedentes ao tamanho x (500M, 1G...)
sudo journalctl --vacuum-time=x   # Limpa os logs mais antigos do que o período x (7d, 3month, 1h, 1y...)

journalctl -k                     # Equivalente ao dmesg
journalctl -p n                   # Logs por prioridade de 0 a 7
journalctl -u name.service        # Logs por serviço
```
