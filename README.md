En este apartado se incluyen instrucciones para que un
usuario administre el entorno de manera correcta. Se asume que el usuario con
el que se van a ejecutar los comandos no es root pero está en el grupo de
sudoers.

# Gestión de servidor bastión
## Acceso SSH
Debido al bastionado se ha cambiado la configuración por
defecto con ssh, se requiere autenticación por claves y se ha cambiado el
puerto de escucha por defecto, por lo que el comando debe tener una estructura
similar a la siguiente:

```bash
ssh -i ~/.ssh/id_ed25519 admrlozano@10.0.0.10 -p 2222
```

# Gestion del firewall
El tráfico se evalúa mediante zonas y prioridades de la red
de origen, la zona por defecto es “drop”, también se ha creado la zona
“servicios” para permitir únicamente lo servicios requeridos, por lo que si se
quiere añadir algún puerto se tiene que definir de la siguiente manera (el
segundo comando es para recargar la configuración del firewall).
 
```bash
sudo firewall-cmd --zone=servicios
--add-port=<puerto>/tcp –-permanent
sudo firewall-cmd --reload
```

# Gestión de SELinux
Este sistema de políticas
vigila el contexto de los puertos y los procesos. Es importante que si cambias
el puerto de un servicio no solo se cambie en el firewall si no también en SELinux.
Para gestionar el puerto de escucha de un
servicio se usa el siguiente comando:
 
```bash
sudo semanage port -m -t <tipo_de_puerto_t> -p
tcp <nuevo_puerto>
```

La sintaxis de este comando es: -m implica
modificar una regla ya existente, -t define el contexto del puerto, -p si es
protocolo tcp o udp.
Si se quiere desactivar o encenderlo para hacer
pruebas se usa “sudo setenforce 0/1”,
0 para apagarlo y 1 para que aplique las políticas.
 

# Gestión de fail2ban
Este es el sistema que se encarga de prohibir el acceso a
direcciones de las que se detectan ataques de fuerza bruta al acceso por ssh.
Para ver el log de acciones de este servicio se usa el siguiente comando:

```bash
sudo tail
-f /var/log/fail2ban.log
```

Para ver el estado de una jaula activa se puede usar este
comando:

```bash
sudo fail2ban-client status sshd 
```

Si se quiere desbanear alguna IP se usa este comando:

```bash
sudo fail2ban-client unban <dirección IP>
```

Por otro lado, si se quiere banear manualmente una dirección
el comando será el siguiente:

```bash
sudo fail2ban-client set sshd banip <dirección IP>
```

Al igual que la mayoría de los servicios el fichero de configuración se encuentra en /etc/
 

# Gestión de servidor Veeam y repositorio inmutable

El servidor que corre Veeam es un Windows server habitual, pero con Veeam instalado, para entrar en la consola de Veeam se tiene que pulsar en conectar “localhost” y en la pestaña de Login pulsar “sign as current user”, después de esto ya se pueden gestionar las copias de seguridad.

Al repositorio bastionado por otro lado solo se puede entrar por terminal local desde vCenter, tiene ssh desactivado porque solo se usa para vincularlo con el servidor de veeam.

# Gestión del entorno vSphere

En este apartado se agrupa la configuración tanto de los hosts como del vCenter, para gestionar estos equipos se tiene que acceder desde la interfaz gráfica, pero para las configuraciones más básicas se tiene que entrar en la terminal de la consola local o por ssh si está activado, para securizarlos un mínimo se ha dejado desactivado, se ha hecho lo mismo con la Shell de los hosts, para activar estas opciones se tiene que acceder a “troubleshooting options”, desde la consola local.

vCenter es indispensable gestionarlo desde la interfaz web, la consola local de este es hasta más básica que la de estos equipos, si se ha atascado algún servicio de este sistema y se tiene que reiniciar se accede a la misma IP en interfaz web, pero desde otro puerto. Ejemplo:

* **Acceso interfaz normal:** https://vcenter.tfg.lab
* **Acceso interfaz de gestión de vCenter:** https://vcenter.tfg.lab:5480

Después de cambiar el puerto aparecerá una pantalla de login, al loguearse se accede a la dicha interfaz de configuración. En esta interfaz en lugar de acceder con administrator@vsphere.local se accede con root
