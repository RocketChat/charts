# Rocket.Chat

[Rocket.Chat](https://rocket.chat/) is free, unlimited and open source. Replace email, HipChat & Slack with the ultimate team chat software solution.

## Notes on installation and recommended settings

- This chart installs rocketchat chart (stable/rocketchat)
- This chart installs mongodb chart (stable/mongodb) 
- Authentication for mongodb is enabled by default (usePassword : true)
- Replication (oplog) for mongodb is enabled by default (and mandatory for Rocket.Chat versions > 1), by default only one mongodb member (primary pod) is deployed.

### Install Rocket.Chat chart and configure mongodbUsername, mongodbPassword, mongodbDatabase and mongodbRootPassword:
```bash
$ helm install --set mongodb.mongodbUsername=rocketchat,mongodb.mongodbPassword=changeme,mongodb.mongodbDatabase=rocketchat,mongodb.mongodbRootPassword=root-changeme --name my-rocketchat stable/rocketchat
```
- mongodbUsername: This user will have access to the rocketchat database and is authenticated using mongodbDatabase
- mongodbPassword: Password for accessing your Rocket.Chat data
- mongodbDatabase: Database used to store Rocket.Chat application and authenticate the mongodbUsername
- mongodbRootPassword: The password for the root user, administrator of the mongodb statefulset and authenticated using the admin database

Is possible to check both password, mongodbPassword and mongodbRootPassword, in mongodb secret, use `kubectl get secrets`

After both charts are installed execute these steps to finish configuring access for the Rocket.Chat app to mongodb:

- Exec to you mongodb primary pod, for example:
```bash
$ kubectl exec -ti my-rocketchat-mongodb-primary-0 -- /bin/bash
```
- Give read access to db local mongodbUsername:
```bash
I have no name!@my-rocket-mongodb-primary-0:/$ mongo admin --uroot -p<mongodbRootPassword>

rs0:PRIMARY> use <mongodbDatabase>
switched to db <mongodbDatabase>
rs0:PRIMARY> db.grantRolesToUser("<mongodbUsername>",[{ role: "read", db: "local" }])
rs0:PRIMARY> quit()

```
- You may need to delete rocket.chat pod to restart Rocket.Chat:
```bash
$ kubectl get pods
$ kubectl delete pod my-rocket-rocketchat-66785bc56-fmpkp
```

### If you would like to install other image than default for this chart, add repository and the image you would like to install to the command:
```bash
$ helm install --set mongodb.mongodbUsername=<your_username_here>,mongodb.mongodbPassword=<your_password_here>,mongodb.mongodbDatabase=rocketchat,mongodb.mongodbRootPassword=root-changeme,repository=<your_image> --name my-rocketchat stable/rocketchat
```

### And if you only would like to install another version of rocket.chat image, add tag value to the command:
```bash
$ helm install --set mongodb.mongodbUsername=<your_username_here>,mongodb.mongodbPassword=<your_password_here>,mongodb.mongodbDatabase=rocketchat,mongodb.mongodbRootPassword=root-changeme,tag=0.74.2 --name my-rocketchat stable/rocketchat
```

### Check rocketchat values.yaml file for more details and adjust to your needs, after you can install Rocket.Chat chart using this command:
```bash
$ helm install --name my-rocketchat -f values.yaml stable/rocketchat
```

Enjoy !
