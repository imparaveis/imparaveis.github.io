Estava aqui pensando em oque fazer nessa tarde ensolarada de 24/01 e pensei, ou durmo na cadeira do trabalho ou brinco de docker...

Segui para um teste de docker com wildfly  + app 

Meu ambiente ambiente docker desktop de testes já configurado seguindo os links abaixo (inclussive já fiz as brincadeiras do site da microsoft)
(https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-10
https://www.spiria.com/en/blog/web-applications/enabling-windows-containers-windows-10/ ) 

Hoje testando docker com wildfly segui o plano da pagina principal para iniciar, e me deparei com algumas questoes pra logar na console e olhando uns blogs resolvi criar minha propria imagem.

links de referencia.
**https://hub.docker.com/r/jboss/wildfly/
** http://blog.arungupta.me/wildfly-admin-console-docker-image-techtip66/ 

Problemas que quis sanar. 
1 - usuario na console 
2 - Com um sample de app.  - https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/

Baixei na minha maquina o sample.war e coloquei ele na mesma pasta do dockerfile 

Configurei ele com os itens abaixo
<b>
FROM jboss/wildfly:latest
RUN /opt/jboss/wildfly/bin/add-user.sh admin Admin123# --silent
ADD your-awesome-app.war /opt/jboss/wildfly/standalone/deployments/
CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0"]
</b><br>

e quando subi, utilizei o export das portas tcp 8080 e 9990 (ambas default). 
No final, a app fica liberada no 127.0.0.1:8080/sample e a console abre com o usuario admin e senha Admin123# na 9990


Comando de build 
ref: https://docs.docker.com/engine/reference/commandline/build/
docker build -t wildfly:teste . 
docker image ls 
(pega o ID) 
docker run -p 8080:8080 -p 9990:9990 -d 


#run criando maquina

<b>PS D:\iso> docker build -t wildfly:teste . </b><br>
Sending build context to Docker daemon  3.188GB
Step 1/4 : FROM jboss/wildfly:latest
 ---> 2602b4852593
Step 2/4 : RUN /opt/jboss/wildfly/bin/add-user.sh admin Admin123# --silent
 ---> Running in 6ccd396a39d4
Removing intermediate container 6ccd396a39d4
 ---> c5df0f876435
Step 3/4 : COPY sample.war /opt/jboss/wildfly/standalone/deployments/
 ---> 5deed7a2b4ba
Step 4/4 : CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0"]
 ---> Running in 5c4428d5fab1
Removing intermediate container 5c4428d5fab1
 ---> 18f5620f5506
Successfully built 18f5620f5506
Successfully tagged wildfly:teste
SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host. All files and directories added to build context will have '-rwxr-xr-x' permissions. It is recommended to double check and reset permissions for sensitive files and directories.
PS D:\iso> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
wildfly             teste               18f5620f5506        10 seconds ago      675MB
teste               latest              85d518046953        39 minutes ago      675MB
<none>              <none>              7dbf416b1fff        About an hour ago   675MB
jboss/wildfly       latest              2602b4852593        13 days ago         675MB
PS D:\iso>

# run start maquina 

PS D:\iso> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
wildfly             teste               18f5620f5506        10 seconds ago      675MB
teste               latest              85d518046953        39 minutes ago      675MB
<none>              <none>              7dbf416b1fff        About an hour ago   675MB
jboss/wildfly       latest              2602b4852593        13 days ago         675MB
<b>PS D:\iso> docker run -p 8080:8080 -p 9990:9990 -d 18f5620f5506 </b><br>
65319609e8a0813d370d5543a4da8ae7b7ae36ede743d6b7ff9dacb9dfe9bf98
PS D:\iso>

# validando nossa app 

PS D:\iso> $WebResponse = Invoke-WebRequest "http://127.0.0.1:8080/sample/"
PS D:\iso> $WebResponse


StatusCode        : 200
StatusDescription : OK
Content           : Sample "Hello, World"...
RawContent        : HTTP/1.1 200 OK
PS D:\iso>


E segue o jogo. 

p.s.: Para esse teste, usei o docker para linux 
