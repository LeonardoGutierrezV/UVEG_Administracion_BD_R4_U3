## R4. Respaldo, recuperación y migración de bases de datos.

En el presente repositorio se ejecuta un ejercicio de Creación de base de datos, Respaldo y reintegración en servidores de base de datos **MySQL** utilizando **Docker** para facilitar el proceso de montaje de servidores.

Se da por sentado que tenemos Docker instalado.

El repositorio encontramos el archivo **Script_escuela_reto4.sql** que contiene la información inicial de la base de datos que debe ser respaldada.

```sql
CREATE DATABASE Escuela;

USE Escuela;

CREATE TABLE `escuela`.`maestros` (
  `idmaestro` INT NOT NULL,
  `nombre` VARCHAR(45) NULL,
  `horas` INT NULL,
  `materia` VARCHAR(45) NULL,
  PRIMARY KEY (`idmaestro`));
  
  CREATE TABLE `escuela`.`alumnos` (
  `idalumno` INT NOT NULL,
  `nombre` VARCHAR(45) NULL,
  `semestre` INT NULL,
  `promediogeneral` DOUBLE NULL,
  PRIMARY KEY (`idalumno`));
  
INSERT INTO `escuela`.`alumnos` (`idalumno`, `nombre`, `semestre`, `promediogeneral`) VALUES ('1', 'Alonso Ramírez', '6', '9.8');
INSERT INTO `escuela`.`alumnos` (`idalumno`, `nombre`, `semestre`, `promediogeneral`) VALUES ('2', 'María Gómez', '4', '9.1');
INSERT INTO `escuela`.`alumnos` (`idalumno`, `nombre`, `semestre`, `promediogeneral`) VALUES ('3', 'Luis Barrón', '1', '7.5');
INSERT INTO `escuela`.`alumnos` (`idalumno`, `nombre`, `semestre`, `promediogeneral`) VALUES ('4', 'Georgina Suarez', '3', '8.2');
INSERT INTO `escuela`.`alumnos` (`idalumno`, `nombre`, `semestre`, `promediogeneral`) VALUES ('5', 'Javier Cortéz', '2', '7.0');
INSERT INTO `escuela`.`alumnos` (`idalumno`, `nombre`, `semestre`, `promediogeneral`) VALUES ('6', 'Alma Rodríguez', '6', '10.0');

INSERT INTO `escuela`.`maestros` (`idmaestro`, `nombre`, `horas`, `materia`) VALUES ('1', 'Julieta Rodríguez', '40', 'Matemáticas');
INSERT INTO `escuela`.`maestros` (`idmaestro`, `nombre`, `horas`, `materia`) VALUES ('2', 'Dylan Romero', '36', 'Historia Universal');
INSERT INTO `escuela`.`maestros` (`idmaestro`, `nombre`, `horas`, `materia`) VALUES ('3', 'Luis Valadez', '38', 'Geografía');
INSERT INTO `escuela`.`maestros` (`idmaestro`, `nombre`, `horas`, `materia`) VALUES ('4', 'Alfonso Alvarez', '40', 'Química');

CREATE USER 'consulta' IDENTIFIED BY '1234';
GRANT SELECT ON escuela.* TO 'consulta';

CREATE USER 'administrador_escuela' IDENTIFIED BY '5678';
GRANT ALL PRIVILEGES ON escuela.* TO 'administrador_escuela';

FLUSH PRIVILEGES;
```
Adicional se cuentan con un par de carpetas.

**Origen**: Que contiene el archivo docker-compose.yml para crear el servidor donde montaremos la base de datos original para realizar el ejercicio.

```yaml
version: '3.3'
services:
  db:
    container_name: 'UVEG_DBAdmin_R4_U3_Origen'
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: '12345'
      MYSQL_ROOT_HOST: '%'
    ports:
      # Configuración de puerto
      - '3306:3306'
    volumes:
    - ./sql-data/db:/var/lib/mysql
    - ./salida:/salida
```

**Destino**: Que contiene el archivo docker-compose.yml para crear el servidor donde reintegraremos la base de datos extraída del servidor origen.

```yaml
version: '3.3'
services:
  db:
    container_name: 'UVEG_DBAdmin_R4_U3_Destino'
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: '12345'
      MYSQL_ROOT_HOST: '%'
    ports:
      # Configuración de puerto
      - '3306:3306'
    volumes:
    - ./sql-data/db:/var/lib/mysql
    - ./entrada:/entrada
```

Se han dispuesto de esta forma para que cada carpeta pueda generar y contenedor los directorios mapeados de los contenedores.

Presionamos la tecla **windows+R** para lanzar la ventana de ejecución de comandos, en la cual escribiremos el siguiente comando.

```bash
cmd /k cd "C:\Users\projects\UVEG_Administracion_BD_R4_U3\origen"
```

Para acceder a la carpeta donde tenemos el archivo yml del servidor origen. A continuación, debemos lanzar el contenedor origen. En la ventana de CMD que se nos abrió, debemos escribir el siguiente comando.

```bash
docker-compose up -d
```
Esto generara la creación del contenedor con un servidor de MySQL completamente funcional.
Aunque el contenedor haya sido creado y nos muestre la leyenda de que ha sido iniciado el contenedor, debemos darle un par de minutos ya que al agregar **-d** a la ejecución del contenedor, la ejecución de **MySQl** se realiza en segundo plano por lo cual puede tomar unos minutos mas.

```bash 
[+] Running 2/2
 - Network origen_default               Creat...                          1.2s
 - Container UVEG_DBAdmin_R4_U3_Origen  Started                           4.2s

C:\Users\projects\UVEG_Administracion_BD_R4_U3\origen>
```

después de unos minutos debemos poder trabajar con el servicio de MySQL, y entonces procedemos a cargar la base de datos original, para lo cual si clonaste o descargaste la el repositorio debemos poder continuar sin problema con la siguiente instrucción, la cual se puede escribir en la ventana CMD que se nos abrió desde el inicio de la práctica.

```bash
docker exec -i UVEG_DBAdmin_R4_U3_Origen sh -c "exec mysql -uroot -p12345" < Script_escuela_reto4.sql
```
Con esto integraremos el Script proporcionado al contenedor **Origen**. Al terminar nos lanzara el siguiente mensaje, esto debido a que estamos escribiendo la clave directamente sobre el comando de integración.

```bash
mysql: [Warning] Using a password on the command line interface can be insecure.
```

Procedemos a ingresar a nuestro contenedor para valida que se ha integrada o la base de datos, para lo cual ejecutamos el siguiente comando en la misma terminal.

```bash
docker exec -it UVEG_DBAdmin_R4_U3_Origen /bin/bash -l
```
Nos devolverá como resultado la siguiente leyenda:

```bash
root@7208031e9287:/#
```
Lo que indica que ya nos encontramos dentro de nuestro contenedor, ahora debemos conectarnos al servicio de MySQL.

```bash
mysql -u root -p
```
Recordemos que la clave es: **12345**

Consultamos las bases de datos montadas:

```bash
show databases;
````
Procedemos a acceder a la base de datos Escuela.

```bash
use Escuela;
```
Y mostramos las tablas montadas en esta base de datos.

```bash
show tables;
```

Ahora procedemos a realizar el respaldo de la base de datos que tenemos montada.

```bash
mysqldump -u root -p Escuela > /salida/Full_Bkp_Escuela-$(date +%F).sql
```
Nos pedirá ingresar nuestro pasword, y procederá a ejecutar el respaldo. A continuación procedemos a validar que el respaldo se ha creado en la ubicación que le indicamos.

```bash
ls salida
```
Y esto nos mostrara el archivo que se contiene en la carpeta indicada.
Escribimos **exit** en la ventana de CMD para salir del contenedor y procedemos a detener el contenedor origen.

```bash
docker stop UVEG_DBAdmin_R4_U3_Origen
```

Nos movemos a la carpeta del contenedor **Destino**

```bash
cd "c:\Users\projects\UVEG_Administracion_BD_R4_U3\destino\"
```

Y ejecutamos el siguiente comando para crear el contenedor destino.

```bash
docker-compose up -d
```

Recordemos que a pesar de terminar la creación del contenedor al utilizar **-d** la ejecución del servidor MySQL será en segundo plano por lo que debemos darle unos minutos hasta que termine.

Una vez que tengamos las carpetas creadas dentro de la carpeta del contenedor destino debemos copiar el archivo de respaldo de la carpeta **salida** del contenedor **origen** dentro de la carpeta **entrada** del contenedor **destino** para poder tenerla disponible dentro del contenedor. Y procedemos a entrar al contenedor **Destino**

```bash
docker exec -it UVEG_DBAdmin_R4_U3_Destino /bin/bash -l
```
Una vez dentro del contenedor procedemos a entrar al servicio de MySQL y crear la base de datos**Escuela**.

```bash
mysql> CREATE DATABASE Escuela;
Query OK, 1 row affected (0.19 sec)
```

Escribimos exit, y procedemos a integrar el respaldo que se extrajo del servidor origen.

```bash
 mysql -u root -p < entrada/Full_Bkp_Escuela-2022-10-14.sql Escuela
```
Nos solicitara ingresar la clave del usuario root. Y una vez que regrese al prompt, podemos validar la reintegración.

Una vez integrada la base de datos podemos proceder a crear los usuarios y sus permisos.

```bash
CREATE USER 'consulta' IDENTIFIED BY '1234';
GRANT SELECT ON escuela.* TO 'consulta';

CREATE USER 'administrador_escuela' IDENTIFIED BY '5678';
GRANT ALL PRIVILEGES ON escuela.* TO 'administrador_escuela';

FLUSH PRIVILEGES;
```
Ahora podemosconsultar los privilegios de cada usuario.

```bash
SHOW GRANTS FOR 'consulta';
mysql> SHOW GRANTS FOR 'administrador_escuela';
```
Para terminar validemos que se creó la estructura de la base de datos;

```bash
USE Escuela;
SHOW tables;
```

Lo que nos devolverá la siguiente información.

```bash
+-------------------+
| Tables_in_escuela |
+-------------------+
| alumnos           |
| maestros          |
+-------------------+
2 rows in set (0.00 sec)
```

Para terminar, detenemos el contenedor destino.

```bash
docker stop UVEG_DBAdmin_R4_U3_Destino
```

Nos colocamos en la carpeta principal de cada uno de los contenedores y ejecutamos el siguiente comando.

```bash
docker compose down -v
```
Y con esta instrucción estamos depurando los contenedores en desuso.
##### dbar4u3
