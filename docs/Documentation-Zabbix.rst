=====================
Documentation Zabbix
=====================




Backup 
============

systemctl stop zabbix-server

sudo pg_dump -U zabbix -h localhost -F c -f zabbix_backup.sql zabbix

mkdir /opt/zabbix-backup/
cp /etc/zabbix/zabbix_server.conf /opt/zabbix-backup/
cp /etc/apache2/conf-enabled/zabbix.conf /opt/zabbix-backup/ #pas nécessaire si pas d'installation Apache2

cp -R /usr/share/zabbix/ /opt/zabbix-backup/
cp -R /usr/share/zabbix-* /opt/zabbix-backup/