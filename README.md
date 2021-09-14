Mixtape

This repo meant to help ramp up Accelerators new to Ruby & Padrino

Setup

git init -b main
brew install gh
gh repo create project-name

git clone 

Mongo Docker Container

```
cd mongo/_docker
docker-compose up -d

mongo --host localhost:27018
rs.initiate({_id:"rsm0", members: [{"_id":1, "host":"localhost:27018"}]})
```
