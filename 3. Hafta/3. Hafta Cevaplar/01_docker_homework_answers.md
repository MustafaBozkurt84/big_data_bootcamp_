# SORULAR
1. Çalışan konteynırları listeleyiniz.

2. Çalışan ve durmuş kontaynırları listeleyiniz.

3. httpd:alpine imajından detached bir konteynır yaratınız. Bu konteynırı durdurup kaldırınız.

4. httpd:alpine imajından yeni bir container yaratın, adı apache olsun, ana işletim sisteminizin tarayıcısı (örneğin chrome) üzerinden localhost:8082 adresinden erişecek şekilde port yönlendirmesi yapın. localhost:8082 adresten eriştiğinizde gördüğünüz sonucu not edin.

5. Az önce yarattığınız apache isimli container loglarını inceleyiniz.

6. Az önce yarattığınız apache container web arayüzünde docker volume kullanmadan Hi there. I have changed this. Well done to me :) yazdırınız.

7. Az önceki apache isimli containerı siliniz.

8. Ana bilgisayar tarayıcısından localhost:8082 adresine girdiğinizde Hi there. I have changed this. Well done to me :) ibaresini docker volume kullanarak gösteriniz.

9. Web sayfasında adınızı soyadınızı yazan bir flask uygulaması yazın, bu uygulamayı Docker file ile imaj haline getirin. Dockerhub'a imajınızı push edin. Bu imajı kullanarak container yaratın ve tarayıcıdan erişerek adınızı soyadınızı tarayıcıda görün. 

10. jupyter/datascience-notebook:python-3.8.5 imajından bir jupyter notebook container oluşturunuz. Browser üzerinden 8888 portundan bu jupyter arayüze erişiniz. Basit bir notebook oluşturunuz. Bu notebook'u saklayınız. Containerı siliniz. Linux terminalden python virtual environment'ı aktif hale gitiriniz. Jupyter çalıştırınız. Container içinde ürettiğiniz notebook'u burada kullanınız.


















# CEVAPLAR
1. Çalışan konteynırları listeleyiniz.

`docker ps`

2. Çalışan ve durmuş kontaynırları listeleyiniz.

`docker ps -a`

3. httpd:alpine imajından detached bir konteynır yaratınız. Bu konteynırı durdurup kaldırınız.
```
[train@localhost ~]$ docker run -d httpd:alpine

[train@localhost ~]$ docker stop 279

[train@localhost ~]$ docker rm 279
```

4. httpd:alpine imajından yeni bir container yaratın, adı apache olsun, ana işletim sisteminizin tarayıcısı (örneğin chrome) üzerinden localhost:8082 adresinden erişecek şekilde port yönlendirmesi yapın. localhost:8082 adresten eriştiğinizde gördüğünüz sonucu not edin.

```
[train@localhost ~]$ docker run --name apache -p8082:80 -d httpd:alpine
```
Sayfada (localhost:8082) görünen **It works!**

5. Az önce yarattığınız apache isimli container loglarını inceleyiniz.
```
[train@localhost play]$ docker logs apache
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
[Sun Nov 01 11:26:34.755287 2020] [mpm_event:notice] [pid 1:tid 139931406589256] AH00489: Apache/2.4.46 (Unix) configured -- resuming normal operations
[Sun Nov 01 11:26:34.755366 2020] [core:notice] [pid 1:tid 139931406589256] AH00094: Command line: 'httpd -D FOREGROUND'
10.0.2.2 - - [01/Nov/2020:11:26:45 +0000] "GET / HTTP/1.1" 200 45
10.0.2.2 - - [01/Nov/2020:11:26:45 +0000] "GET /favicon.ico HTTP/1.1" 404 196
10.0.2.2 - - [01/Nov/2020:11:27:37 +0000] "-" 408 -
```

6. Az önce yarattığınız apache container web arayüzünde docker volume kullanmadan Hi there. I have changed this. Well done to me :) yazdırınız.
```
[train@localhost play]$ nano index.html
[train@localhost play]$ cat index.html
<html><body><h1>Hi there. I have changed this. Well done to me :)</h1></body></html>
[train@localhost play]$ docker cp index.html apache:/usr/local/apache2/htdocs
```

7. Az önceki apache isimli containerı siliniz.

```

[train@localhost play]$ docker stop apache
apache
[train@localhost play]$ docker rm apache
apache
[train@localhost play]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

8. Ana bilgisayar tarayıcısından localhost:8082 adresine girdiğinizde Hi there. I have changed this. Well done to me :) ibaresini docker volume kullanarak gösteriniz. İşiniz bittiğinde containerı siliniz.
```
# create a volume
[train@localhost play]$ docker volume create v_apache
v_apache

# Learn volume path
[train@localhost play]$ docker volume inspect v_apache
[
    {
        "CreatedAt": "2020-11-01T14:43:11+03:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/v_apache/_data",
        "Name": "v_apache",
        "Options": {},
        "Scope": "local"
    }
]

# copy desired index.html file to volume
[train@localhost play]$ sudo cp index.html /var/lib/docker/volumes/v_apache/_data
[sudo] password for train:

# check if index.html copied
[train@localhost play]$ sudo ls /var/lib/docker/volumes/v_apache/_data
index.html

# Create container
[train@localhost play]$ docker run --name apache -p8082:80 -v v_apache:/usr/local/apache2/htdocs -d httpd:alpine
3c8f12c068e358984c6847fedd0ca767b498d0567e145bef5096e8a78df6c023
```

From browser see the result.

9. Web sayfasında adınızı soyadınızı yazan bir flask uygulaması yazın, bu uygulamayı Docker file ile imaj haline getirin. Dockerhub'a imajınızı push edin. Bu imajı kullanarak container yaratın ve tarayıcıdan erişerek adınızı soyadınızı tarayıcıda görün. 
- create a project file like following
```
[train@localhost play]$ tree homework-9-flask-app/
homework-9-flask-app/
├── app.py
├── Dockerfile
├── README.md
└── requirements.txt
```
- app.py
```
[train@localhost homework-9-flask-app]$ cat app.py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return "<h1>I am Erkan SIRIN</h1>"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8082)
```

- Dockerfile
```
[train@localhost homework-9-flask-app]$ cat Dockerfile
FROM python:3.6

COPY . /app

WORKDIR /app

RUN pip3 install --upgrade pip && pip3 install -r requirements.txt

EXPOSE 8082

CMD [ "python", "app.py" ]
```

- requirements.txt
```
[train@localhost homework-9-flask-app]$ cat requirements.txt
flask
```

- Build docker image  
`docker build -t erkansirin78/myname-flask:1.0 .`  

- Push image
`docker image push erkansirin78/myname-flask:1.0` 

- Pull your image from dockerhub and see yourname on browser.
```
[train@localhost homework-9-flask-app]$ docker run --rm --name myname-flask -p 8082:8082 erkansirin78/myname-flask:1.0
Unable to find image 'erkansirin78/myname-flask:1.0' locally
1.0: Pulling from erkansirin78/myname-flask
e4c3d3e4f7b0: Already exists
101c41d0463b: Already exists
8275efcd805f: Already exists
751620502a7a: Already exists
0a5e725150a2: Already exists
9ab4bf1101f3: Already exists
9ba108bf0aed: Already exists
eac75d8e87c4: Already exists
b7d6e626c70b: Already exists
d269e58fe1f6: Pull complete
e54270b2b34a: Pull complete
Digest: sha256:3922e4981fe044ecaf5d08e134452cea67b4d95172e71a3b3b49236d3a7cd3d2
Status: Downloaded newer image for erkansirin78/myname-flask:1.0
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:8082/ (Press CTRL+C to quit)
172.17.0.1 - - [06/Nov/2020 16:00:45] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [06/Nov/2020 16:00:46] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [06/Nov/2020 16:00:46] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [06/Nov/2020 16:00:46] "GET / HTTP/1.1" 200 -
```

10. jupyter/datascience-notebook:python-3.8.5 imajından bir jupyter notebook container oluşturunuz. Browser üzerinden 8888 portundan bu jupyter arayüze erişiniz. Basit bir notebook oluşturunuz. Bu notebook'u saklayınız. Containerı siliniz. Linux terminalden python virtual environment'ı aktif hale gitiriniz. Jupyter çalıştırınız. Container içinde ürettiğiniz notebook'u burada kullanınız.

```
# create a volume for jupyter container
[train@localhost play]$ docker volume create v_jupyter
v_jupyter

# create jupyter container
[train@localhost play]$ docker run --name jupyter -p 8888:8888 -v v_jupyter:/home/jovyan/work -d jupyter/datascience-notebook:python-3.8.5
b2dc1bd1143c9f7eae182019599ea1741c8ecf2bfe4e73eb76d81df8010d50f2

# Learn token from logs
[train@localhost play]$ docker logs jupyter
```
From browser reach jupyter paste token and create a simple notebook save it.
Then close jupyter then stop and remove jupyter container. 
```

[train@localhost play]$ docker stop jupyter
jupyter
[train@localhost play]$ docker rm jupyter
jupyter
```

```
# Check notebook is in volume path
[train@localhost play]$ sudo ls -l /var/lib/docker/volumes/v_jupyter/_data
[sudo] password for train:
total 4
-rw-r--r--. 1 train users 1106 Nov  1 15:25 my-example.ipynb

# Copy notebook to working dir
[train@localhost play]$ sudo cp /var/lib/docker/volumes/v_jupyter/_data/my-example.ipynb .

# Activate python virtualenv of out vm
[train@localhost play]$ source ~/venvspark/bin/activate

# Start jupyter notebook 
(venvspark) [train@localhost play]$ jupyter notebook
```

See the notebook and use it then close jupyter.  

Deactivate virtual environment.
```
(venvspark) [train@localhost play]$ deactivate
```