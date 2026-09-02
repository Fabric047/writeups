# Skills Assessment - SQL Injection Fundamentals

* **Target:** `154.57.164.82:30381`
* **Módulo:** HTB Academy

---

### 1. What is the password hash for the user 'admin'?

**Respuesta:** `$argon2i$v=19$m=2048,t=4,p=3$dk4wdDBraE0zZVllcEUudA$CdU8zKxmToQybvtHfs1d5nHzjxw9DhkdcVToq6HTgvU`

#### Procedimiento:
1. Abrimo Burp Suite y nos vamos a Proxy>Intercept>Open Browser

2. Escribimos en el chromium el objetivo (target): `https://154.57.164.82:30381` (en mi caso)
Es https (con la `s` al final)

4. 

![Captura del hash](img/admin-hash.png)

---

### 2. What is the root path of the web application?

**Respuesta:** `/var/www/chattr-prod`

#### Procedimiento:
Para descubrir la ruta raíz:
* Forzamos un error en la base de datos o leímos un archivo del sistema usando `LOAD_FILE()`:
  ```sql
  ' UNION SELECT 1, LOAD_FILE('/var/www/html/config.php'), 3-- -
  ```

---

### 3. Achieve remote code execution, and submit the contents of /flag_XXXXXX.txt below.
**Respuesta:** `061b1aeb94dec6bf5d9c27032b3c1d8d`

