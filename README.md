
# Monitaramento do WILDFLY e Tomcat com ZABBIX usando Zorka

## O Zorka

É um agente de monitoramento para aplicações JAVA.
O mesmo possui suporte a diversos tipos de aplicações: Tomcat, JBoss, Jetty, IBM Webshere, Mule ESB etc.

## O WILDFLY

É um servidor de aplicações Java EE Open Source.

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

```
----------------------------------------------------------------------------------------------------------
```
## O TOMCAT

È um servidor web Java, mais especificamente, um container de servlets.

**Instalando Tomcat:**

*- Pre requisitos: jdk - 6,7 ou 8*

*Download pacote.*

```
cd /opt

wget http://ftp.unicamp.br/pub/apache/tomcat/tomcat-7/v7.0.90/bin/apache-tomcat-7.0.90.tar.gz

```

***Descompactando pacote***

```
tar -xvf  apache-tomcat-7.0.90.tar.gz

```
***Renomeando pacote***

```
mv apache-tomcat-7.0.90.tar.gz tomcat

```
*Criando usuario e atribuindo permissoes ao diretorio Tomcat.*
```
sudo groupadd tomcat
sudo useradd -M -s /bin/nologin -g tomcat -d /opt/tomcat tomcat

```
***Criando arquivo no systemd***

```
sudo vi /etc/systemd/system/tomcat.service

# Systemd unit file for tomcat
[Unit]
Description=Apache Tomcat Web Application Container
After=syslog.target network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/bin/kill -15 $MAINPID

User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target


```

***Reload no Systemd***

```
sudo systemctl daemon-reload

```
***Start o Tomcat***

```
sudo systemctl start tomcat

```
***Verificando status Tomcat***

```
sudo systemctl status tomcat

```
***Habilite serviço na inicialização***

```
sudo systemctl enable tomcat

```

**Intalação Zorka Agent**

*Download pacote*

http://zorka.io/

***Descompate o pacote no diretorio /opt/tomcat***

*Configure o Zorga e o Zabbix-server editando o arquivo zorka.properties*

```
scripts = jvm.bsh, zabbix.bsh, apache/tomcat.bsh
-XX:-UseSplitVerifier
zabbix.active = yes
zabbix.active.server.addr = ipzabbix_server:10051
zabbix.server.addr = ipzabbix_server
zabbix.listen.port = 10055
zorka.hostname = tomcat
http.trace.exclude = ~.*.png, ~.*.gif, ~.*.js, ~.*.css, ~.*.jpg, ~.*.jpeg, ~.*favicon.ico

#Se JDK for 6,7 atribua o paramentro -XX:-UseSplitVerifier, se JDK for 7ou+ atribua o paramentro #zorka.spy.compute.frames = yes
```

*Edite o arquivo Systemd do Tomcat e atribua CATALINA_OPTS="$CATALINA_OPTS -javaagent:<zorka-home>/agent.jar=<zorka-home>"*

```
Environment='CATALINA_OPTS=-javaagent:/opt/tomcat/zorka/zorka.jar=/opt/tomcat/zorka'
```
***Reload no Systemd***

```
sudo systemctl daemon-reload


```

***Stop no Tomcat***

```
sudo systemctl stop tomcat

```
***Start o Tomcat***

```
sudo systemctl start tomcat

```

***Verifique se as portas dos serviços estão em Listen***

```
netstat -ltpn

tcp        0      0 0.0.0.0:10055           0.0.0.0:*               OUÇA       1712/java           
tcp        0      0 tomcatserver:8080   	    0.0.0.0:*               OUÇA       1712/java  

```


FIM ......................................... ;)
