# debug a ngninx ingress on k8s

Lattely I was stuck in a ingress host clonflict.  
As you may know you have the ingress controller, in charge of doing the job
and the ingress ressources to describe the routing expected.

Each ingress ressource must have a hostname.

You may have more than one ingress ressource cluster wide and even in given namespace for example to describe the backend routing on one file and the frontend routing on another one.  
Here I strongly advise to have dedicated hostname for both. e.g. : bo.myhost.dev and b2c.myhost.dev

It is possible but not recommended to split for a same hostname the config in two files.  
But doing so all the global annotation may be in conflict, and the oldest ingress win.
see official doc here [on collision](https://docs.nginx.com/nginx-ingress-controller/configuration/handling-host-and-listener-collisions/#:~:text=If%20multiple%20resources%20contend%20for,the%20oldest%20resource%20will%20win).

That was my case with a very old ingress remaining in the corner of my K8S. 
To debug this, I take a look at the Nginx generated conf (see below)
and I use my dear [dumpK8s](https://github.com/MathFrenchToast/dumpk8s) script, so I was able to make a full text search on all the ressources to identify where the value was declared.

Here is a bunch of usefull kubectl/nginx requests:

given the Ingress Controller namespace (choosen during installation of the nginx helm chart)  
`NGINXNS=ingressctrl`

get the first pod name  
`NGINXPODNAME=$(kubectl -n $NGINXNS get pods -o json | jq -r .items[0].metadata.name)`

and check the Ingress Controller Logs  
`kubectl logs -n $NGINXNS $NGINXPODNAME`

check the Nginx Configuration  
`kubectl exec -it -n $NGINXNS $NGINXPODNAME -- cat /etc/nginx/nginx.conf`
