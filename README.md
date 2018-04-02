## wonderfall/isso

![](https://i.goopics.net/q1.png)

#### What is this?
Isso is a commenting server similar to Disqus. More info on the [official website](https://posativ.org/isso/).

#### Docker image
Docker Hub: [wonderfall/isso](https://hub.docker.com/r/wonderfall/isso/)

Docker Pull Command:  
`docker pull wonderfall/isso`

#### Features
- Based on Alpine Linux 3.3.
- Latest Isso installed with `pip`.

#### Build-time variables
- **ISSO_VER** : version of Isso.

#### Environment variables
- **GID** : isso group id *(default : 991)*
- **UID** : isso user id *(default : 991)*

#### Volumes
- **/config** : location of configuration files.
- **/db** : location of SQLite database.

#### Ports
- **8080** [(reverse proxy!)](https://github.com/hardware/mailserver/wiki/Reverse-proxy-configuration).
> `8080` is exposed in [this Isso Docker image](https://github.com/jsntn/docker-isso/blob/1f7f068e251a8817aae42c99e1e4184b416ee757/Dockerfile#L31).

#### Example of simple configuration
Here is the full documentation : https://posativ.org/isso/docs/

```
# cat /mnt/docker/isso/config/isso.conf
[general]
; database location, check permissions, automatically created if not exists
dbpath = /db/comments.db
; your website or blog (not the location of Isso!)
; you can add multiple hosts for local development
; or SSL connections. There is no wildcard to allow
; any domain.
host = https://www.example.com/
[server]
listen = http://0.0.0.0:8080/

# cat /etc/nginx/sites-available/isso
...
server {

  server_name comments.example.com; #serve the Isso system with a dedicated domain

    location / {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $host;
      proxy_set_header X-Script-Name /;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_pass http://localhost:8080;
    }

  ...

}
...
```

Run the Isso Docker Image as a Container:  
`docker run --name isso -v /mnt/docker/isso/config:/config -v /mnt/docker/isso/db:/db -p 8080:8080 --rm -d wonderfall/isso`

Test Isso:  
`https://comments.example.com/js/embed.min.js`

Paste the script code below in your website:
```
<script data-isso="//comments.example.com/"
data-isso-css="true"
data-isso-lang="zh"
data-isso-reply-to-self="false"
data-isso-require-author="true"
data-isso-require-email="true"
data-isso-max-comments-top="10"
data-isso-max-comments-nested="5"
data-isso-reveal-on-click="5"
data-isso-avatar="true"
data-isso-avatar-bg="#f0f0f0"
data-isso-avatar-fg="#9abf88 #5698c4 #e279a3 #9163b6 ..."
data-isso-vote="true"
data-vote-levels=""
src="//comments.example.com/js/embed.min.js"></script>
<section id="isso-thread"></section>
<noscript>请开启 JavaScript 查看 <a href="https://posativ.org/isso/" rel="nofollow">isso 评论系统的内容</a>。</noscript>
```

Done.

#### P.S.

We can also host Isso system in a subdirectory `isso` but not a dedicated domain, like `www.example.com/isso`, in this case, we need to update the Nginx configuration this way:

```
# cat /etc/nginx/sites-available/isso
...
server {

  server_name comments.example.com;

  location /isso {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $host;
    proxy_set_header X-Script-Name /isso;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass http://localhost:8080;
  }

}
...
```

And the script code:
```
<script data-isso="//www.apkjam.com/isso/"
data-isso-css="true"
data-isso-lang="zh"
data-isso-reply-to-self="false"
data-isso-require-author="true"
data-isso-require-email="true"
data-isso-max-comments-top="10"
data-isso-max-comments-nested="5"
data-isso-reveal-on-click="5"
data-isso-avatar="true"
data-isso-avatar-bg="#f0f0f0"
data-isso-avatar-fg="#9abf88 #5698c4 #e279a3 #9163b6 ..."
data-isso-vote="true"
data-vote-levels=""
src="//www.apkjam.com/isso/js/embed.min.js"></script>
<section id="isso-thread"></section>
<noscript>请开启 JavaScript 查看 <a href="https://posativ.org/isso/" rel="nofollow">isso 评论系统的内容</a>。</noscript>
```
