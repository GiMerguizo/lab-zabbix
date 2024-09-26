# Instalação do Zabbix
[https://www.zabbix.com/download](https://www.zabbix.com/download)
<br>
**Versão:** 5.0 LTS
# Requisitos
## Hardware

| Hardware | Mínimo |
| -------- | ------ |
| Memória  | 128MB  |
| Disco    | 256MB  |


> Banco de Dados:
> Em ambientes grandes, é recomendado ter o servidor de banco de dados separado do Zabbix Server.

- Vamos utilizar a distribuição CentOS 8 (OracleLinux 8)

## Software
### Versões mínimas

| Software                                                                                       | Versão mínima |
| ---------------------------------------------------------------------------------------------- | ------------- |
| Apache                                                                                         | 1.3.12        |
| PHP com suporte ctype, bcmath, gettext, ldap, session, sockets, mbstring, xmlreader, xmlwriter | 7.2.0         |
| PHP GD                                                                                         | 2.0.28        |
| PHP libXML                                                                                     | 2.6.15        |
| MySQL                                                                                          | 5.5.62, 8.0.x |
| Oracle                                                                                         | 11.2          |
| PostgreSQL                                                                                     | 9.2.24        |
| TimeScaleDB                                                                                    | 1.0           |
| SQLite                                                                                         | 3.3.5         |

# Passos pré instalação

Conferir a configuração do SO
```
cat /etc/os-release 
df -h 
lvs
```
- Modelo:
```
opt    rootvg -wi-ao---- 1.00g 
root   rootvg -wi-ao---- 4.00g 
swap_0 rootvg -wi-ao---- 4.00g 
tmp    rootvg -wi-ao---- 2.00g 
var    rootvg -wi-ao---- 2.00g 
vlog   rootvg -wi-ao---- 2.00g
```

- Desativar o SELinux
- Desativar o firewalld
# Instalação
> Vamos fazer a instalação através de pacotes de distribuições

## Instalando o SGBD
> Vamos utilizar o MySQL

```
# dnf install mysql-server
# systemctl start mysqld
# systemctl enable mysqld
```
## Instalando o Zabbix

```
# rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/8/x86_64/zabbix-release-5.0-4.el8.noarch.rpm  
# dnf clean all
```
```
# dnf install zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf zabbix-agent
```

## Configuração do Banco de Dados

```
# mysql -uroot -p    
mysql> create database zabbix character set utf8 collate utf8_bin;  
mysql> create user zabbix@localhost identified by 'password';  
mysql> grant all privileges on zabbix.* to zabbix@localhost;  
mysql> set global log_bin_trust_function_creators = 1;  
mysql> quit;

# zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
```

## Configuração inicial
```
# vim /etc/zabbix/zabbix_server.conf
DBPassword=password
```
```
# vim /etc/php-fpm.d/zabbix.conf
php_value[date.timezone] = America/Sao_Paulo
```

```
# systemctl enable zabbix-server zabbix-agent httpd php-fpm
# systemctl start zabbix-server zabbix-agent httpd php-fpm
# systemctl status zabbix-server.service
```

## Interface Web
- Vamos acessar a nossa interface:
```
http://<ip_servidor>/zabbix
```
- Terminar a configuração conforme o solicitado e apertar em Finish

# Referências

📕Monitoramento com Zabbix
