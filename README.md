# Backup-banco-de-dados-via-Telegram





No tutorial já estamos supondo que seu BOT no Telegram esteja criado, não tem segredo, pesquisada rapida no google vai explicar.
Futuramente vou ensinar aqui a como criar. 
Obs.: Estamos suponde que o bot do telegram esteja dentro de um grupo, pois vamos pegar o ID do chat do grupo.






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
wget https://raw.githubusercontent.com/T3uZ/Backup-banco-de-dados-via-Telegram/main/telegram -O /bin/telegram
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

Bot no telegram tudo pronto, vamos criar o script para pegar o backup do banco de dados.

```bash
nano /etc/backups/backup_enviar_telegram.sh
```
Altere as variáveis informando usuário/senha do seu banco de dados bem como ID do telegram.

SCRIPT MYSQL DIRETO NA VPS

```bash
#!/bin/bash
# Autor: remontti.com.br
# Ajudante: Matheus
# --------------------------------------
# USUARIO  DO BANCO DE DADOS
USER_DB='root'
# SENHA DO BANCO DE DADOS
SENHA_DB=''
# NOME DO BANCO DE DADOS
NOME_BANCO='press-ticket'
# NOME PARA O BACKUP
NOME_DO_BKP='Backup Press Ticket'
# ID TELEGRAM
ID_TELEGRAM='-000000000'
# --------------------------------------
DATABKP=`date +%Y-%m-%d`
NOME_BD="/tmp/${NOME_BANCO}.${DATABKP}.tar.gz"
mysqldump -h 127.0.0.1 -u ${USER_DB} -p${SENHA_DB} -B ${NOME_BANCO} > /tmp/${NOME_BANCO}.${DATABKP}.sql 
/bin/telegram -f "${ID_TELEGRAM}" "/tmp/${NOME_BANCO}.${DATABKP}.sql" "${NOME_DO_BKP}" "${NOME_BANCO}" &>/dev/null
rm -f /tmp/*.sql &>/dev/null
```
SCRIPT POSTGRESQL DIRETO NA VPS

```bash
#!/bin/bash
# Autor: remontti.com.br
# Ajudante: Matheus
# --------------------------------------
# USUARIO  DO BANCO DE DADOS
USER_DB='root'
# SENHA DO BANCO DE DADOS
SENHA_DB=''
# NOME DO BANCO DE DADOS
NOME_BANCO='press-ticket'
# NOME PARA O BACKUP
NOME_DO_BKP='Backup Press Ticket'
# ID TELEGRAM
ID_TELEGRAM='-000000000'
# --------------------------------------
DATABKP=`date +%Y-%m-%d`
NOME_BD="/tmp/${NOME_BANCO}.${DATABKP}.tar.gz"
pg_dump "host=localhost port=5432 dbname=${NOME_BANCO} user=${USER_DB} password=${SENHA_DB}" > /tmp/${NOME_BANCO}.${DATABKP}.sql 
/bin/telegram -f "${ID_TELEGRAM}" "/tmp/${NOME_BANCO}.${DATABKP}.sql" "${NOME_DO_BKP}" "${NOME_BANCO}" &>/dev/null
rm -f /tmp/*.sql &>/dev/null
```
SCRIPT POSTGRESQL EM DOCKER

```bash
#!/bin/bash
# Autor: remontti.com.br
# Ajudante: Matheus
# --------------------------------------
# USUARIO  DO BANCO DE DADOS
USER_DB='nsi'
# SENHA DO BANCO DE DADOS
SENHA_DB=''
# NOME DO BANCO DE DADOS
NOME_BANCO='nsi'
# NOME PARA O BACKUP
NOME_DO_BKP='nsi-mix'
# ID TELEGRAM
ID_TELEGRAM='-00000000'
# --------------------------------------
DATABKP=`date +%Y-%m-%d`
NOME_BD="/tmp/${NOME_BANCO}.${DATABKP}.sql"
NOME_BD_COMPACTADO="/tmp/${NOME_BANCO}.${DATABKP}.tar.gz"

# Usando a variável de ambiente PGPASSWORD para passar a senha
export PGPASSWORD=${SENHA_DB}
docker exec e575470dee4c pg_dump -h localhost -p 5432 -U ${USER_DB} -d ${NOME_BANCO} > ${NOME_BD}

# Compactando o arquivo de backup
tar -czf "${NOME_BD_COMPACTADO}" -C /tmp "${NOME_BANCO}.${DATABKP}.sql"

/bin/telegram -f "${ID_TELEGRAM}" "${NOME_BD_COMPACTADO}" "${NOME_DO_BKP}" "${NOME_BANCO}" &>/dev/null

# Adicionando um atraso de 5 minutos antes de remover os arquivos
sleep 300

# Removendo arquivos temporários
rm -f ${NOME_BD} &>/dev/null
rm -f ${NOME_BD_COMPACTADO} &>/dev/null
```

Dando permissão para o arquivo que acabamos de criar.

```bash
chmod +x /etc/backups/backup_enviar_telegram.sh
```

Agora vamos criar e configurar o backup para rodar em 10 em 10 Minutos.

```bash
crontab -e
```
A primeira vez que você rodar o comando será solicitado qual editor você deseja, vou selecionar o nano “1”.

Na ultima linha coloca o seguinte comando:

```bash
*/10 *  *   *   *     /etc/backups/backup_enviar_telegram.sh
```
