# Skills Assessment - SQL Injection Fundamentals

* **Target:** `154.57.164.82:30381`
* **Módulo:** HTB Academy

---

## 1. What is the password hash for the user 'admin'?

**Respuesta:** `$argon2i$v=19$m=2048,t=4,p=3$dk4wdDBraE0zZVllcEUudA$CdU8zKxmToQybvtHfs1d5nHzjxw9DhkdcVToq6HTgvU`

#### Procedimiento:
1. Abrimo Burp Suite y nos vamos a Proxy>Intercept>Open Browser

2. Escribimos en el chromium el objetivo (target): `https://154.57.164.82:30381` (en mi caso)
Es https (con la `s` al final)


![BurpSuite](imgs/burp.png)

3. Le damos en "create account" para crear una cuenta:
En mi caso yo pondre los datos:
username=fabric047
password=Test12345%^6789
invitationCode=aaaa-bbbb-1111

![CreateAccount](imgs/create_account.png)

pero nos aparece el mensaje: "Invalid invitation code" ya que no existe ese codigo de invitacion (porque lo inventamos)

![InvalidInvitationCode](imgs/invalid_invitationcode.png)

4. Usemos el Burp Suite:

En el burp activamos el intercept (intercept on) en Proxy>Intercept
Con el intercept on, entramos a la pagina y ponemos los datos de nuevo
username=fabric047
password=Test12345%^6789
invitationCode=aaaa-bbbb-1111


5. Al darle click en "create acount", notaremos que la pagina queda cargando

Entraremos al burp de nuevo y tendremos el method post. Luego le damos en "send to repeater":

![Send_to_Repeater](imgs/sendtorepeater.png)

6. Nos vamos al repeater:

![Repeater](imgs/repeater1.png)

Abajo tenemos esto:
```
username=fabric047&password=Test12345%25%5E6789&repeatPassword=Test12345%25%5E6789&invitationCode=abcd-efgh-1234
```

Le agregamos `'or'1'='1` y quedara asi:

```
username=fabric047&password=Test12345%25%5E6789&repeatPassword=Test12345%25%5E6789&invitationCode=abcd-efgh-1234'or'1'='1
```

## Porque agregamos `'or'1'='1`?

Supongamos que la aplicación comprueba el código de invitación con una consulta parecida a:
```
SELECT * FROM invitations
WHERE code = 'aaaa-bbbb-1111';
```
Si tú introduces simplemente: `aaaa-bbbb-1111` la aplicacion ejecuta `WHERE code = 'aaaa-bbbb-1111'` y no lo acepta ya que ese codigo no existe

En cambio, si pones `aaaa-bbbb-1111' or '1'='1`, se ejecuta algo asi:
```
WHERE code = 'aaaa-bbbb-1111'     <-- Eso es Falso
    OR
'1' = '1'                         <-- Eso es Verdadero (1 es igual a 1)
```
Sabemos que Falso OR Verdadero es igual a Verdadero

7. Le damos en "send":

![Repeater2](imgs/repeater2.png)

y en el Response>Pretty ( a la derecha ) obtendremos: Location: /login.php?s=account+created+successfully!


8. Entramos a la pagina y le damos en login para iniciar sesion con el usuario y password con el que nos registramos

![Login](imgs/login.png)

9. A partir de aqui, nos concentramos solo en el cuadro de busqueda (search)

Por ejemplo, intentemos con `admin') union select 1,2,3-- -`: No obtenemos nada (error)
Ahora, intentemos con `admin') union select 1,2,3,4-- -` y tenemos esto:


![primero](imgs/primero.png)

Entonces, sabemos que solo la tercer columna sera mostrada.

10. Ahora si viene lo interesante! La pregunta nos pide hallar el password hash del admin.

Primero debemos conocer el nombre de la base de datos:

```
admin') union select 1,2, database(),4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

![databasename](imgs/databasename.png)

`database()` devolvió `chattr`, es decir, esa es la base de datos que está usando la aplicación.

Ahora necesitamos saber las tablas existentes dentro de la base de datos chattr

```
admin') union select 1,2, TABLE_NAME,4 from INFORMATION_SCHEMA.TABLES where table_schema='chattr'-- -
```

![tablesinchattr](imgs/tablesinchattr.png)

Tenemos 3 tablas: Users, InvitationCodes y Messages.
La tabla Users parece que tiene informacion interesante.

Vamos a ver sus columnas: 

```
xd') union select 1,2,COLUMN_NAME,4 from INFORMATION_SCHEMA.COLUMNS where table_name='Users'-- -
```

![Columnas](imgs/columnas.png)

Ahora sabemos que la tabla Users es algo asi:


| UserID | Username | Password | InvitationCode | AccountCreated
| --- | --- | --- | --- | --- |
| ... | ... | ... | ... | ... |
| ... | ... | ... | ... | ... |

Por fin!!! Obtengamos la password hash del admin:
```
xd') union select 1,2,Password,4 from Users where Username="admin"-- -
```

![passwordhash](imgs/passwordhash.png)

---

## 2. What is the root path of the web application?

**Respuesta:** `/var/www/chattr-prod`

#### Procedimiento:
Para descubrir la ruta raíz:
* Forzamos un error en la base de datos o leímos un archivo del sistema usando `LOAD_FILE()`:
  ```sql
  ' UNION SELECT 1, LOAD_FILE('/var/www/html/config.php'), 3-- -
  ```

---

## 3. Achieve remote code execution, and submit the contents of /flag_XXXXXX.txt below.
**Respuesta:** `061b1aeb94dec6bf5d9c27032b3c1d8d`

