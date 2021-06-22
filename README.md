# santander-dockercoins

```
docker image build --file hasher/Dockerfile --tag index.docker.io/sandalioorgaz/santander-dockercoins:test-hasher hasher/
docker image build --file rng/Dockerfile --tag index.docker.io/sandalioorgaz/santander-dockercoins:test-rng rng/
docker image build --file webui/Dockerfile --tag index.docker.io/sandalioorgaz/santander-dockercoins:test-webui webui/
docker image build --file worker/Dockerfile --tag index.docker.io/sandalioorgaz/santander-dockercoins:test-worker worker/

docker network create hasher-network
docker network create rng-network
docker network create redis-network

docker volume create redis-volume

docker container run --detach --entrypoint docker-entrypoint.sh --name redis --network redis-network --rm --volume redis-volume:/data --workdir /data index.docker.io/library/redis:6.2.4-alpine3.13@sha256:b7cb70118c9729f8dc019187a4411980418a87e6a837f4846e87130df379e2c8 redis-server 
# comprobamos los logs para ver si se ha creado de forma correcta
docker container logs redis


docker container run --detach --entrypoint irb --name hasher --network hasher-network --rm --volume ${PWD}/hasher/hasher.rb:/src/hasher.rb:ro --workdir /src/ index.docker.io/sandalioorgaz/santander-dockercoins:test-hasher hasher.rb

docker container logs hasher


docker container run --detach --entrypoint python3 --name rng --network rng-network --rm --volume ${PWD}/rng/rng.py:/src/rng.py:ro --workdir /src/ index.docker.io/sandalioorgaz/santander-dockercoins:test-rng rng.py

docker container logs rng


docker container run --detach --entrypoint node --name webui --network redis-network --rm --volume ${PWD}/webui/files/:/src/files/:ro --volume ${PWD}/webui/webui.js:/src/webui.js:ro --workdir /src/ index.docker.io/sandalioorgaz/santander-dockercoins:test-webui webui.js

docker container logs webui


docker container run --detach --entrypoint python3 --name worker --network hasher-network --rm --volume ${PWD}/worker/worker.py:/src/worker.py:ro --workdir /src/ index.docker.io/sandalioorgaz/santander-dockercoins:test-worker worker.py
# no se pueden dar de alta todas las redes, pero sí que se pueden añadir a postetiori
docker network connect redis-network worker
docker network connect rng-network worker

docker container logs worker

# borra todas las imágenes
docker rm -f $(docker ps -qa) 

# Ver la red del contenedor
docker container exec redis ip route


while true; do sleep 10 && docker container logs worker 2>&1 | grep "Coin found" && break; done
