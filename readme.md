# Mongodb external

Include the backup of your external mongodb database with your Kasten backup 

## How it works 

The usual way as described in the tutorial [art of blueprint](https://www.kasten.io/kubernetes/resources/how-to-guides/blueprints) : 

A secrets containing the informations in charge of making the backup will be annotated with the blueprint 
[mongodb-external-bp]((./mongodb-external-bp.yaml)). When Kasten backup, restore the namespace or delete the backup Kasten will detect this annotation and the corresponding backup, restore or delete action of the blueprint will be called, with the secret as the context of the backup.

## Install

Create the blueprint
```
kubectl create -f mongodb-external-bp.yaml
```

Create a secret with the necessary credentials to operate backup and restore to your external db. 

Change those variables according to your setting 

```
PORT=27027
ROOT_USERNAME=root
PASSWORD=p0A1effRBd
AUTH_DATABASE=admin
MONGO_HOST=my.mongodb-host.com
```

And create the secret 
```
kubectl create secret generic mongo-external-backup \
  --from-literal=port=$PORT \
  --from-literal=username=$ROOT_USERNAME \
  --from-literal=password=$PASSWORD \
  --from-literal=auth-database=$AUTH_DATABASE \
  --from-literal=mongo_host=$MONGO_HOST
```


Annotate the secret or use a blueprint binding
```
kubectl annotate secret mongo-external-backup kanister.kasten.io/blueprint=mongodb-external-bp
```

Now use kasten to launch a backup of the namespace that will backup also the external database.

When you'll restore a restorepoint it will restore the database a this time.