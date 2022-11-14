# Backup-banco-de-dados-via-Telegram





Criando Bot no telegram

Abra o Telegram e procure por @BotFather.
Inicie a conversa com ele.

## Screenshots






Alterando para root

```bash
sudo su root
```

Instalando algumas dependenticas necessarias


```bash
apt install curl wget zip unzip
```

Criando pasta onde vai ficar o script

```bash
mkdir /etc/backups/
```

Baixando o script

```bash
wget https://raw.githubusercontent.com/matheus1628/Backup-banco-de-dados-via-Telegram/main/telegram -O /bin/telegram
chmod +x /bin/telegram
```
Acessando o arquivo para modificar com os dados necessarios

```bash
nano /bin/telegram
```

Altere o TOKEN e o formato da compactação (zip ou tar)
Altere o token:

```bash
TOKEN="000000000:0000000000000-0000000000000000000000000000000"
```
Escolha o metodo de compactação tar/zip

```bash
COMPAC='tar'
```

