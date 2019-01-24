Estava aqui pensando em oque fazer nessa tarde ensolarada de 24/01 e pensei, ou durmo na cadeira do trabalho ou brinco de docker...

Segui para um teste de docker com wildfly  + app 

Meu ambiente ambiente docker desktop de testes já configurado seguindo os links abaixo (inclussive já fiz as brincadeiras do site da microsoft)<br>
(https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-10<br>
https://www.spiria.com/en/blog/web-applications/enabling-windows-containers-windows-10/ ) <br>

Hoje testando docker com wildfly segui o plano da pagina principal para iniciar, e me deparei com algumas questoes pra logar na console e olhando uns blogs resolvi criar minha propria imagem.<br>

links de referencia.<br>
**https://hub.docker.com/r/jboss/wildfly/<br>
** http://blog.arungupta.me/wildfly-admin-console-docker-image-techtip66/ <br>

Problemas que quis sanar. <br>
1 - usuario na console <br>
2 - Com um sample de app.  - https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/ <br>

Baixei na minha maquina o sample.war e coloquei ele na mesma pasta do dockerfile 

Configurei ele com os itens abaixo
<b><br>
FROM jboss/wildfly:latest<br>
RUN /opt/jboss/wildfly/bin/add-user.sh admin Admin123# --silent<br>
ADD your-awesome-app.war /opt/jboss/wildfly/standalone/deployments/<br>
CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0"]<br>
</b><br>

e quando subi, utilizei o export das portas tcp 8080 e 9990 (ambas default). 
No final, a app fica liberada no 127.0.0.1:8080/sample e a console abre com o usuario admin e senha Admin123# na 9990

<br>
Comando de build <br>
ref: https://docs.docker.com/engine/reference/commandline/build/<br>
docker build -t wildfly:teste . <br>
docker image ls <br>
(pega o ID) <br>
docker run -p 8080:8080 -p 9990:9990 -d <br>


------ run criando maquina

<b>PS D:\iso> docker build -t wildfly:teste . </b><br>
Sending build context to Docker daemon  3.188GB
Step 1/4 : FROM jboss/wildfly:latest
 ---> 2602b4852593
Step 2/4 : RUN /opt/jboss/wildfly/bin/add-user.sh admin Admin123# --silent
 ---> Running in 6ccd396a39d4
Removing intermediate container 6ccd396a39d4
 ---> c5df0f876435
Step 3/4 : COPY sample.war /opt/jboss/wildfly/standalone/deployments/
 ---> 5deed7a2b4ba<br>
Step 4/4 : CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0"]<br>
 ---> Running in 5c4428d5fab1
Removing intermediate container 5c4428d5fab1
 ---> 18f5620f5506
Successfully built 18f5620f5506
Successfully tagged wildfly:teste
SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host. All files and directories added to build context will have '-rwxr-xr-x' permissions. It is recommended to double check and reset permissions for sensitive files and directories.
PS D:\iso> docker image ls<br>
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE<br>
wildfly             teste               18f5620f5506        10 seconds ago      675MB<br>

------  run start maquina <br>
PS D:\iso> docker image ls<br><br>
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE<br>
wildfly             teste               18f5620f5506        10 seconds ago      675MB<br>
<b>PS D:\iso> docker run -p 8080:8080 -p 9990:9990 -d 18f5620f5506 </b><br>


------ validando nossa app 
<br>
PS D:\iso> $WebResponse = Invoke-WebRequest "http://127.0.0.1:8080/sample/"<br>
PS D:\iso> $WebResponse<br>
StatusCode        : 200<br>
StatusDescription : OK<br>
Content           : Sample "Hello, World"...<br>
RawContent        : HTTP/1.1 200 OK<br>
PS D:\iso>


E segue o jogo. 

p.s.: Para esse teste, usei o docker para linux 
