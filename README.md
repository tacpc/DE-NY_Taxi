# DE-NY_Taxi

## TOOLS

### *Docker*

Docker allows you to put everything an application needs inside a container - sort of a box that contains everything: OS, system-level libraries, python, etc.
You run this box on a host machine. The container is completely isolated from the host machine env.
In the container you can have Ubuntu 18.04, while your host is running on Windows.
You can run multiple containers on one host and they won’t have any conflict.
An image = set of instructions that were executed + state. All saved in “image”
Installing docker: https://docs.docker.com/get-docker/

### *Postgres*

```
docker run -it \
-e POSTGRES_USER="root" \
-e POSTGRES_PASSWORD="root" \
-e POSTGRES_DB="ny_taxi" \
-v "./ny-taxi-volume:/var/lib/postgresql/data" \
-p 5432:5432 \
postgres:13
```

* Connecting with python

`pip install pgcli`

`pgcli -h localhost -p 5432 -u root -d ny_taxi`

`\dt`

```
from sqlalchemy import create_engine
engine = create_engine('postgresql://root:root@localhost:5432/ny_taxi')
```

`print(pd.io.sql.get_schema(df, 'yellow_taxi', con=engine))`

```
CREATE TABLE yellow_taxi (
        "VendorID" BIGINT,
        tpep_pickup_datetime TEXT,
        tpep_dropoff_datetime TEXT,
        passenger_count BIGINT,
        trip_distance FLOAT(53),
        "RatecodeID" BIGINT,
        store_and_fwd_flag TEXT,
        "PULocationID" BIGINT,
        "DOLocationID" BIGINT,
        payment_type BIGINT,
        fare_amount FLOAT(53),
        extra FLOAT(53),
        mta_tax FLOAT(53),
        tip_amount FLOAT(53),
        tolls_amount FLOAT(53),
        improvement_surcharge FLOAT(53),
        total_amount FLOAT(53),
        congestion_surcharge FLOAT(53)
)
```


Create table:

`df.head(0).to_sql('yellow_taxi', engine, if_exists='replace', index=False)`

Insert data:

`df.to_sql('yellow_taxi', engine, if_exists='append', index=False)`

### *pgAdmin*

```
docker run -it \
-e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
-e PGADMIN_DEFAULT_PASSWORD="root" \
-p 8080:80 \
```

However, this docker container can’t access the postgres container. We need to link them

**Docker network**
```
docker run -it \
-e POSTGRES_USER="root" \
-e POSTGRES_PASSWORD="root" \
-e POSTGRES_DB="ny_taxi" \
-v "./ny-taxi-volume:/var/lib/postgresql/data" \
-p 5432:5432 \
--name pgdatabase \
--net pg \
postgres:13
dpage/pgadmin4
```
```
docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -p 8080:80 \
  --name pgadmin \
  --net pg \
  dpage/pgadmin4
```

### Docker Compose

To avoid having 2 terminals open we can use docker compose.

Create new file `docker-compose.yaml`

```
services:
  pgdatabase:
    image: postgres:13
    restart: always
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root
      POSTGRES_DB: ny_taxi
    volumes:
      - "./ny-taxi-volume:/var/lib/postgresql/data:rw"
    ports:
      - "5432:5432"
  pgadmin:
    image: dpage/pgadmin4
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: root
    ports:
      - "8080:80"
```

And then
```
docker-compose up
```

Run in detached mode:

```
docker-compose up -d
```

Shutting it down:

```
docker-compose down
```
