
# Monitaramento do WILDFLY com ZABBIX usando Zorka

## O WILDFLY

É um servidor de aplicações Java EE Open Source.


## O Zorka

É um agente de monitoramento para aplicações JAVA.
O mesmo possui suporte a diversos tipos de aplicações: Tomcat, JBoss, Jetty, IBM Webshere, Mule ESB etc.


**Instalando WildFly:**

*- Pre requisitos: jdk - 7 ou 8*

*Download pacote.*
```
cd /opt

wget http://download.jboss.org/wildfly/10.1.0.Final/wildfly-10.1.0.Final.zip

```
***Descompactando pacote***

```
unzip wildfly-10.1.0.Final.zip .

```
***Renomeando pacote*** 

```
mv wildfly-10.1.0.Final wildfly

```
***Definindo variaveis de ambiente no arquivo standlone.conf :***

```
vim wildfly/bin/standalone.conf

JBOSS_HOME="/opt/wildfly"

vim /etc/profile

JBOSS_HOME="/opt/wildfly"

```
***Alterações de rede no arquivo standalone.xml de 127.0.0.1 para ip_do_servidor.***

```
vim /opt/wildfly/standalone/configuration/standalone.xml

		<wsdl-host>${jboss.bind.address:ip_do_servidor}</wsdl-host>
        <inet-address value="${jboss.bind.address.management:ip_do_servidor}"/>
        <inet-address value="${jboss.bind.address:ip_do_servidor}"/>

```
**Intalação Zorka Agent**

*Download pacote*

http://zorka.io/

***Descompate o pacote no diretorio /opt/wildfly***

*Configure o Zorga e o Zabbix-server editando o arquivo zorka.properties* 

```
scripts = jvm.bsh, zabbix.bsh, jboss/jboss7.bsh
zorka.spy.compute.frames = yes
zabbix.active = yes
zabbix.active.server.addr = ipzabbix_server:10051
zabbix.server.addr = ipzabbix_server
zabbix.listen.port = 10055
zorka.hostname = wildfy
http.trace.exclude = ~.*.png, ~.*.gif, ~.*.js, ~.*.css, ~.*.jpg, ~.*.jpeg, ~.*favicon.ico


```
*Salve o arquivo e inicie o Wildfly*

```
/opt/wildfly/bin/standalone.sh &

```
***Verifique se as portas dos serviços estão em Listen***

```
netstat -ltpn

tcp        0      0 0.0.0.0:10055           0.0.0.0:*               OUÇA       1712/java           
tcp        0      0 ipwildfly:8080   	    0.0.0.0:*               OUÇA       1712/java  

```

FIM ......................................... ;)



