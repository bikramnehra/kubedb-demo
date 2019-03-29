# kubedb-demo
This repo contains kubedb demo code

# Steps

Manually create a database named `blog` after deploying

`kubectl exec -it <db-pod> -n default sh`
`mongo admin`
`db.auth("root","rdXns2EsjYhwuVQN")`

To create `blog` table(optional):
`use blog`
`db.movie.insert({"name":"batman"});`
`show dbs`

To create user in db `blog`:
`use blog`
`db.createUser({user: "root", pwd: "***", roles: [ { role: "readWrite", db: "blog" }]})`

To check the data in the `blog` table:
`db.posts.find()`

Create a SecurityGroup in CF to whitelist mongodb port(27017):
`cf create-security-group mongodb-sg mongodb-rules.json`
`cf bind-security-group mongodb-sg org space`



`cf create-service-broker appscode-service-broker user pass https://appscode-service-broker.appscode.svc`
`cf service-brokers`

`cf enable-service-access mongodb`
`cf marketplace` or `cf service-access`

`cf create-service mongodb demo-mongodb mongodb-svc`
`cf services`

`cf bind-service mongoblog mongodb-svc`

To test the app:

`curl -L -vv --fail -X POST http://mongoblog.cap.bsingh.aws.howdoi.website/post/new --data 'post[title]=desired-title' --data 'post[body]=desired-body'`
