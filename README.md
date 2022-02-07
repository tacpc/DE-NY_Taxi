# DE-NY_Taxi

## TOOLS

* *Docker*

Docker allows you to put everything an application needs inside a container - sort of a box that contains everything: OS, system-level libraries, python, etc.
You run this box on a host machine. The container is completely isolated from the host machine env.
In the container you can have Ubuntu 18.04, while your host is running on Windows.
You can run multiple containers on one host and they won’t have any conflict.
An image = set of instructions that were executed + state. All saved in “image”
Installing docker: https://docs.docker.com/get-docker/

* *Postgres*

```
docker run -it \
-e POSTGRES_USER="root" \
-e POSTGRES_PASSWORD="root" \
-e POSTGRES_DB="ny_taxi" \
-v "./ny-taxi-volume:/var/lib/postgresql/data" \
-p 5432:5432 \
postgres:13
```
