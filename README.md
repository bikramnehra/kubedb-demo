# kubedb-demo
This repo contains kubedb demo code

## Bootstrap:

1. Setup CAP on a Kube cluster(for this demo we used EKS)

2. Install [appscode/service-broker](https://github.com/appscode/service-broker) using the latest helm charts:

   https://appscode.com/products/service-broker/0.3.1/setup/install/#install-service-broker
   
   **Note:** We are using a customized version present in this repo:
   
   ```
   helm install <path to helm charts> --name appscode-service-broker --namespace appscode
   ```
  
3. Install the `kubedb-operator` and `kubedb-catalog`(v0.11):

   https://github.com/kubedb/cli/blob/0.11.0/docs/setup/install.md#helm

## Steps:

1. Register [appscode/service-broker](https://github.com/appscode/service-broker) with `cf` and verify:
   
   ```
   cf create-service-broker appscode-service-broker user pass https://appscode-service-broker.appscode.svc
   cf service-brokers
   ```
   
2. Enable `mongodb` service access and verify:

   ```
   cf enable-service-access mongodb
   cf marketplace
   ```
   
3. Create and verify `mongodb` service instance and verify:

   ```
   cf create-service mongodb demo-mongodb mongodb-svc
   cf services
   ```
   
4. Push `mongoblog` app to `cf`:
    
   ```
   cd <path to mongoblog app>
   cf push --no-start
   ```
   
5. Bind the service to app and verify:

   ```
   cf bind-service mongoblog mongodb-svc
   cf env mongoblog
   cf start mongoblog
   ```

6. Create a `cf` SecurityGroup to whitelist mongodb port:
  
   ```
   cf create-security-group mongodb-sg mongodb-rules.json
   cf bind-security-group mongodb-sg org space
   ```
  

## Workaround:


For the purpose of this demo we need to manually create a database named `blog` after deploying `mongodb-svc`

Log into mongo shell:

```
kubectl exec -it <db-pod> -n default sh
mongo admin
db.auth("root","***")
```

To create `blog` table(optional):

```
use blog
db.movie.insert({"name":"batman"});
show dbs
```

To create user in db `blog`:

```
use blog
db.createUser({user: "root", pwd: "***", roles: [ { role: "readWrite", db: "blog" }]})
```


## Tests

To test the app make following post request:

```
curl -L -vv --fail http://mongoblog.cap.bsingh.aws.howdoi.website/post/new --data 'post[title]=desired-title' --data 'post[body]=desired-body'
```

To check the data in the `blog` table:

```
db.posts.find()
```
