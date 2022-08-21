[
](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#)

# [Stake Con Raspberry Pi](https://docs.alvey.site/en/)

# Alvey en Raspberry Pi

1. [Conseguir una Stakebox](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Getting a Stakebox)
2. [Descargando Alvey Raspbian](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Downloading Alvey Raspbian)
3. [Capturas de pantalla de Alvey Raspbian](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Screenshots of Alvey Raspbian)
4. [Instalación de Alvey a través del repositorio Alvey Raspbian](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Installing Alvey via Alvey Raspbian repository) **(solo es necesario si no está utilizando una stakebox Alvey o no está utilizando la imagen Alvey Raspbian)**
5. [Raspberry Pi Zero](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Raspberry_Pi_Zero)
6. [Configurar un firewall en Raspbian](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Protecting access with a basic firewall)
7. [Lanzamiento del Alvey daemon ](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Launching Alvey daemon)
8. [Encryptacion de billetera ](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Encrypting wallet)
9. [Staking](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#Staking)
10. [Apoyo](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/#How to backup to a separate device)

### Conseguir una Stakebox

Hay varias formas de ejecutar Alvey en una Raspberry Pi, quizás la forma más fácil es obtener una ** Alvey Stakebox **, puede ordenarla desde aquí:

https://www.stakebox.org/products/alvey-stakebox

### Descargando Alvey Raspbian

Si no desea comprar o ya posee una stakebox y solo desea obtener la última versión de Raspbian con Alvey preinstalada con el repositorio oficial de Alvey, puede descargar las imágenes de Raspbian usted mismo:

# ¡Nuevo lanzamiento de Alvey Raspbian!

## Esta nueva versión trae grandes cambios, incluidas versiones optimizadas para Raspberry Pi 4 y Raspberry Pi Zero.

### Registro de cambios:

- Actualice a la versión Debian Buster 10.9
- Alvey 0.20.2 - listo para el hardfork de 32s 

### Descargar enlaces:

#### Alvey Raspbian 64bit (Recomendado de Raspberry Pi 2 - Raspberry Pi 4)

https://raspbianimages.s3.amazonaws.com/image_2021-04-06-Alvey-Raspbian.zip

https://raspbianimages.s3.amazonaws.com/image_2021-04-06-Alvey-Raspbian-lite.zip

### "Grabando" la imagen Alvey Raspbian en tu tarjeta SD

Por favor Mire este video tutorial que muestra cómo descargar, grabar y usar su imagen Alvey Raspbian

https://www.youtube.com/watch?v=0W6NlIk7Tgw&t=0s

### ¿Qué es SWAP de todos modos?

SWAP enables "virtual memory", it uses a portion of your disk to store data that cannot be stored in RAM, this helps devices like the Pi Zero to continue running without crashing even if the applications are using more than the 512MB RAM included with the Pi Zero.

### ¿Cómo habilitamos SWAP?

Habilitar SWAP en Pi  es extremadamente fácil:

1. Abra una terminal como se muestra en la siguiente imagen 

![2zero](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/2zero.png)

Dentro de la terminal, escriba "sudo dphys-swapfile setup" y presione enter

Verá aparecer un texto y una confirmación de que se está generando su archivo SWAP de 2 gb.

1. Aún dentro de la misma terminal, escribe "sudo dphys-swapfile swapon" y presiona enter. Esto no te dará ninguna confirmación, sin embargo, ¡tu archivo SWAP ha sido configurado y activado !. Solo necesita hacer esto una vez, el Pi activará su archivo SWAP en caso de reinicio / apagado.
   
   ![3zero](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/3zero.png)

2. Aquí podemos ver que el archivo SWAP está activo, lo que nos da un total de 2,42 GB de RAM (SWAP y ZRAM incluidos)
   
   ![4zero](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/4zero.png)

### ¡NOTA IMPORTANTE!

**Alvey raspbian ** el usuario predeterminado es ** alvey ** y la contraseña predeterminada es ** alvey1234 **. Deberá ingresarlos en el primer inicio de sesión y el sistema de inicio de sesión le pedirá que cambie su contraseña de inmediato, ¡asegúrese de usar una contraseña segura!

### Capturas de pantalla de Alvey Raspbian

![alveyrasp1](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/alveyrasp1.png)

Para iniciar Alvey, debemos ir al menú e ir a internet -> Alvey

![alveyrasp2](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/alveyrasp2.png)

Una vez que haga clic en él, verá la siguiente pantalla que menciona algunos detalles sobre el uso del disco y el espacio disponible en su Raspberry Pi.

![alveyrasp3](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/alveyrasp3.png)

¡Después de hacer clic en Aceptar, su Raspberry Pi comenzará a sincronizarse!

![alveyrasp4](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/alveyrasp4.png)

Sincronizar su raspberry puede tomar desde un par de horas hasta un día, ten paciencia.

![alveyrasp5](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/alveyrasp5.png)

La imagen raspbian de Alvey también tiene algunos fondos de pantalla geniales para elegir:

![wallpaper1](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/wallpaper1.png)

![wallpaper2](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/wallpaper2.png)

![wallpaper3](https://docs.alvey.site/en/Alvey-on-Raspberry-Pi/wallpaper3.png)

## Instalación de Alvey a través del repositorio Alvey Raspbian

Si está utilizando una instalación raspbian ** "normal" **, puede agregar el repositorio Alvey para instalar Alvey y mantener las actualizaciones fácilmente.

### Instalar dirmngr y apt-transport https

```
sudo apt install -y dirmngr apt-transport-https
```

### Agregar clave pública alvey

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BF5B197D
```

### Agregar repositorio a sus fuentes APT

`sudo su - logueate como root primero

```
echo "deb https://repo.alveyscan.com/apt/raspbian/ jessie main" >> /etc/apt/sources.list` or`echo "deb https://repo.alveyscan.com/apt/raspbian/ stretch main" >> /etc/apt/sources.list
```

Esto agregará el repositorio a su archivo de fuentes APT.

### Actualizar fuentes APT e instalar Alvey

```
sudo apt update && sudo apt install alvey
```

Al hacer esto, actualizaremos nuestras fuentes e instalaremos Alvey en nuestra Raspberry Box, que ahora puede actuar como un servidor / nodo de staking.

## Cambiar contraseña predeterminada

**TENGA EN CUENTA: solo necesitará hacerlo si está utilizando una imagen raspbian "limpia"; no necesitará hacer esto si está utilizando un Stakebox o el Alvey Raspbian oficial**

Esta opción se recomienda por razones de seguridad, la contraseña predeterminada en el pi es bien conocida, se recomienda cambiarla al iniciar sesión por primera vez.

Para cambiar simplemente escriba: `passwd`

El mensaje le pedirá que escriba y repita la nueva contraseña para confirmar.

## Protección del acceso con un firewall básico

Bueno, nuestra raspberry es solo para staking, no es necesario tener todos esos puertos abiertos, cerremos todo lo que no necesitamos y solo permitamos el acceso a los servicios necesarios.

Primero, instalemos UFW (firewall sin complicaciones) que es una interfaz fácil de usar para iptables

```
sudo apt install ufw
```

Una vez que esté instalado, procedemos con los permisos de acceso, definiremos qué puertos serán accesibles. Veamos primero qué está abierto:

`sudo ufw status` Esto debería mostrar algo como esto:

```
Status: active
To Action From
-- ------ ----
22 ALLOW Anywhere
```

#### Ok, es hora de comenzar a cerrar el acceso, escriba lo siguiente:

```
sudo ufw default deny incoming
sudo ufw allow 3888/tcp
```

Aquí hemos definido los conceptos básicos, cerrando todo excepto los puertos 3888 y 3889 que Alvey utiliza para funcionar.

Si usa SSH, se recomienda permitir solo el acceso desde la red local.

```
sudo ufw allow from 196.168.0.0/24 to any port 22
```

## Binarios disponibles en Raspberry Pi

- alveyd
- alvey-cli
- alvey-qt 
- alvey-tx

## Lanzamiento Alvey daemon

Todo lo que necesitamos hacer para iniciar el daemon Alvey es escribir: `alveyd -daemon`

Tan pronto como escriba esto, la billetera creará el archivo wallet.dat entre otros archivos (si aún no están allí). La billetera se ejecutará y comenzará a sincronizarse instantáneamente desde los otros nodos de Blockchain, esto puede demorar algunas horas en completarse asi que puede tomar un café y dejar que se sincronice.

## Lanzamiento de Alvey-Qt

Si está utilizando la interfaz de escritorio de Raspberry Pi, todo lo que necesita hacer es navegar al menú de aplicaciones-> otro-> alvey-qt

## Cifrando billetera

Podemos encriptar la billetera en cualquier momento, es mejor hacerlo antes de continuar.

Para hacer esto, escriba lo siguiente en la línea de comando:

```
alvey-cli encryptwallet yourpassword
```

Esto encriptará la billetera que a su vez cierra el daemon, verá el siguiente mensaje:

`billetera encriptada; El servidor Alvey se detiene, reinicie para ejecutar con la billetera cifrada. Si ya realizó una copia de seguridad antes de cifrar, ** necesita hacer una nueva copia de seguridad.

`alvey-cli getaccountaddress` "" -> Justo después de iniciar el daemon, puede obtener la dirección de su billetera escribiendo esto.

Puede enviar monedas Alvey a la dirección que acabamos de obtener del daemon, recuerde que esas transacciones requieren al menos más de 500 confirmaciones antes de que sean lo suficientemente maduras para el staking.

## Staking

Ahora que hemos esperado hasta tener al menos 501 confirmaciones en nuestra transaccion entrante, ya podemos participar en staking. Sin embargo, si nuestra billetera esta cifrada (la cual ciframos por razones de seguridad), no podremos participar en staking. Abramos nuestra billetera para participar en staking utilizando la linea de comandos!.

```
alvey-cli walletpassphrase password 999999999 true
```

¡El comando anterior desbloqueará la billetera por 31.6 años! eso debería ser suficiente por ahora. Tenga en cuenta que esto no desbloqueará su copia de seguridad, solo la billetera que se está ejecutando en este momento.

Ahora que hemos desbloqueado nuestra billetera, debemos esperar hasta que tengamos más de 501 confirmaciones para ser elegibles para el staking, si ya lo hacemos, es cuestión de tiempo que variará dependiendo del peso de la red frente al peso de su billetera.

## Verificación de saldo

Para verificar su saldo, escriba alvey-cli getinfo, esto mostrará información general, incluido su saldo disponible y el saldo en staking

## Verificar transacciones

Para verificar sus transacciones (entrantes y salientes) escriba alvey-cli listtransactions

## Verificar información de staking

To check Alvey's staking information, type alvey-cli getstakinginfo

## Consejos de staking

Staking realmente depende del peso de la red frente al peso de su billetera, que se basa en la cantidad de monedas que tiene, un mayor peso aumenta sus posibilidades de alcanzar un bloque.

Si tiene una gran cantidad de monedas, es una buena idea dividirlas en transacciones separadas, por ejemplo, si tiene 10,000 ALVEY, es mejor enviar 10 transacciones de 1000 ALVEY cada una a su billetera, cada una genera una entrada UTXO que participará en el staking. Esto optimiza el proceso de replanteo y funciona mucho mejor que solo una gran entrada de 10.000 ALVEY.

Si desea dividir sus monedas en diferentes direcciones dentro de su billetera Rasbperry Pi, escriba lo siguiente para obtener nuevas direcciones dentro de su billetera: alvey-cli getnewaddress Cada vez que escriba esto, obtendrá una nueva dirección, ALVEY puede generar cualquier cantidad de las direcciones que desea, pero tenga en cuenta que si supera las 100 direcciones nuevas, es posible que desee hacer una nueva copia de seguridad de su billetera.

## Actualizando billetera

Siempre estamos lanzando nuevas actualizaciones, a veces es para agregar nuevas funciones o corregir errores. En cualquier caso, la actualización es muy fácil, todo lo que tiene que hacer es escribir `sudo apt update && sudo apt upgrade -y`

## Cómo hacer una copia de seguridad en un dispositivo separado

Hacer una copia de seguridad en Raspberry es simple, solo necesita copiar el archivo wallet.dat, pero ¿cómo exporta esto a otro dispositivo?

Primero, descargaremos Filezilla, que es un servidor FTP / SFTP seguro y fácil de usar.

[https://filezilla-project.org](https://filezilla-project.org/)

![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla1.png)

La instalación es como cualquier otra aplicación de Windows.

![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla2.png)

Cuando finaliza el instalador, iniciamos Filezilla y somos recibidos con esta pantalla, procedamos y agreguemos nuestra clave ssh previamente creada

Entramos en Editar -> Configuración -> SFTP. Esto nos dará la siguiente pantalla en la que podremos importar nuestra clave SSH.

![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla3.png)

Tenga en cuenta que Filezilla solo acepta la clave privada que se creó cuando se generó la clave ssh.

![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla4.png)Aquí ya hemos agregado la clave ssh, ahora podemos iniciar sesión en nuestro servidor

![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla5.png)

ingresamos nuestra dirección IP de Raspberry Pi + nombre de usuario (root en este caso), dejamos una contraseña en blanco porque estamos usando ssh-key para iniciar sesión![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla6.png)

Simplemente presione ok cuando se le solicite, y podrá iniciar sesión.![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla8.png)

![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla9.png)

Aquí podemos ver la carpeta / root / de nuestra Raspberry Pi, aquí es donde se ejecuta nuestra billetera y tiene la billetera almacenada en /root/.alvey, podemos continuar y hacer doble clic en la carpeta que nos mostrará lo siguiente:![img](https://docs.alvey.site/en/How-to-Stake-ALVEY-using-a-Linux-Virtual-Private-Server/filezilla10.png)

Ahora todo lo que tenemos que hacer es desplazarnos hacia wallet.dat, hacer clic derecho y seleccionar descargar de la lista. Esto descargará el archivo wallet.dat a nuestra computadora, ¡hemos respaldado con éxito nuestra billetera Alvey !.
