# Docker registry

Deploys a docker registry using the [registry:2](https://github.com/docker/distribution/blob/master/docs/deploying.md) docker image to a `centOS 7` host.

Tested on google cloud stable release of `centOS 7`

# provision

Create a file named `hosts` in the root and add
```
[registry]
<ip or domain name of your server>
```

Add a disk to it at /dev/sdb (default on google cloud) (it will be mounted at /data and used for storing images).

Make sure port `5000` is open on the host. You can run `tcpdump -i eth0 port 5000` on the host and curl it from your env to see if its open.

Create a private key `domain.key` without password and save it together with the certificate `domain.crt` in `roles/provision/files`.

Create a htpasswd file and store it in the same place. These will be used as your login credentials.
(cert and htpasswd files are gitignored)

`docker run --entrypoint htpasswd registry:2 -Bbn testuser testpassword > roles/provision/files/htpasswd`

`ansible-playbook -i hosts provision.yml -u <user>`


# deploy

`ansible-playbook -i hosts deploy.yml -u <user>`

# login

You can now login with `docker login <myhost>:5000`

# Push pull

Tag an image with `docker tag <myhost>:5000/<repo>/<name> <imageid>`. The tag decides which registry is used.
You can now push the tag with `docker push <tag>` and then pull it with `docker pull <tag>` from somewhere else.
