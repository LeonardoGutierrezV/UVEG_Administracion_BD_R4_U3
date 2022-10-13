## R4. Respaldo, recuperación y migración de bases de datos.

En el presente repositorio se ejecuta un ejercicio de Creación de base d edatos, Respaldo y reintegración en servidores de base de datos **MySQL** utilizando **Docker** para facilitar el proceso de montaje de servidores.

El el repositorio encontramos el archivo **Script_escuela_reto4.sql** que contiene la información inicial de la base de datos que debe ser respaldada.

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