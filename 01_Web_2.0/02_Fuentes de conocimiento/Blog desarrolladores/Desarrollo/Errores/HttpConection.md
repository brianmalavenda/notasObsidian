tipo::
tags::
fecha:: 2026-01-30
links::
links_externos::


docker-compose down # Esto limpia las redes huérfanas 
docker network prune -f 

sudo iptables -t nat -F 
sudo iptables -t filter -F

sudo systemctl restart docker

docker-compose up -d

---
#### Referencias

