# docker-dokuwiki-pi
Docker dokuwiki for raspbian with support for SSL based on https://bitbucket.org/mprasil/docker_dokuwiki

# SSL

Two .pem files are required for SSL: lighttpd.pem and fullchain.pem. The lighttpd.pem file should contain the site certificate and private key, fullchain.pem should contain the CA-cert. If you use Let's Encrypt you can just copy fullchain.pem from /etc/letsencrypt/live/yoursite and you can concatenate the site certificate and key from the same directory into one file with:

```
cat cert.pem privkey.pem > lighttpd.pem
```

Place the two .pem files into the directory where the Dockerfile is before building a docker image.

# Building a docker image and running a container

Building:

```
docker build . -t my_dokuwiki
```

Running a container from the image:

```
docker run -p 443:443 --name my_dokuwiki_container my_dokuwiki
```

You can change port 443 to a port number that you'd like docker to expose to the container host.

If you'd like to create another container using the volumes from another one do:

```
docker run -p 443:443 --name my_dokuwiki_container_new --volumes-from my_dokuwiki_container my_dokuwiki
```

# Starting the container on boot

The container can be managed by systemd. Create a systemd unit (configuration) file /etc/systemd/system/docker-dokuwiki.service with the following contents:

```
[Unit]
Description=Dokuwiki container
Requires=docker.service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a my_dokuwiki
ExecStop=/usr/bin/docker stop -t 2 my_dokuwiki

[Install]
```

If you named your dokuwiki container differently, then replace "my_dokuwiki" with the name you've chosen.

To start the container on boot run:

```
systemct enable docker-dokuwiki
```