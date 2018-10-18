https://github.com/berndruecker/flowing-retail/tree/master/runner/docker-compose
and
https://github.com/berndruecker/flowing-retail 

install docker compose in VM:

sudo apt install docker-compose


docker-compose -f docker-compose-kafka-java-choreography-zeebe-track.yml up -d

docker-compose logs -f



Demo 1
------
Pure choreography

vagrant up
cd /vagrant
rm docker-compose.yml
cp docker-compose-kafka-java-choreography.yml docker-compose.yml
docker-compose up -d

this will start all containers

the following end points will now be available:

Monitor:
http://192.168.188.113:8095/

Checkout:
http://192.168.188.113:8090/

Docs:
http://192.168.188.113:8099/


- start the monitor (connect to web socket): http://192.168.188.113:8095/
- submit one order in http://192.168.188.113:8090/
- show the flow in the monitor: http://192.168.188.113:8095/; show the payload of the events


Demo 2
------
Decoupled, orchestration with Camunda as workflow evaluator/task dispenser

vagrant up
cd /vagrant
docker-compose down
rm docker-compose.yml
cp docker-compose-order-camunda.yml docker-compose.yml
docker-compose up -d

this will start all containers

the following end points will now be available:

Monitor:
http://192.168.188.113:8095/

Checkout:
http://192.168.188.113:8090/

Camunda:
http://192.168.188.113:8091 

Docs:
http://192.168.188.113:8099/

steps:
- start the monitor (connect to web socket): http://192.168.188.113:8095/
- submit one order in http://192.168.188.113:8090/
- show the flow in the monitor: http://192.168.188.113:8095/
- show in Camunda what the intended flow is: http://192.168.188.113:8091   (note: login with demo/demo) ; no running instances are visible
- stop the shipping microservice (docker stop vagrant_shipping_1)
- submit one order in http://192.168.188.113:8090/
- show the flow in the monitor: http://192.168.188.113:8095/
- show in Camunda that now there is a running instance, stuck at the task that the shipping microservice could perform
- start the shipping microservice (docker start vagrant_shipping_1)
- show in the monitor that the instance completes

To stop and remove containers:
docker-compose down

