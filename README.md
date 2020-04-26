# My experiments with Cockroachdb!

# Install Cockroachdb with Helm

```
raj$ helm install my-release stable/cockroachdb
NAME: my-release
LAST DEPLOYED: Thu Apr 23 07:54:05 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
CockroachDB can be accessed via port 26257 at the
following DNS name from within your cluster:

my-release-cockroachdb-public.default.svc.cluster.local

Because CockroachDB supports the PostgreSQL wire protocol, you can connect to
the cluster using any available PostgreSQL client.

For example, you can open up a SQL shell to the cluster by running:

    kubectl run -it --rm cockroach-client \
        --image=cockroachdb/cockroach \
        --restart=Never \
        --command -- \
        ./cockroach sql --insecure --host=my-release-cockroachdb-public.default

From there, you can interact with the SQL shell as you would any other SQL
shell, confident that any data you write will be safe and available even if
parts of your cluster fail.

Finally, to open up the CockroachDB admin UI, you can port-forward from your
local machine into one of the instances in the cluster:

    kubectl port-forward my-release-cockroachdb-0 8080

Then you can access the admin UI at http://localhost:8080/ in your web browser.

For more information on using CockroachDB, please see the project's docs at:
https://www.cockroachlabs.com/docs/

```

<HR>
  
```
k get po -w
NAME                                READY   STATUS    RESTARTS   AGE
my-release-cockroachdb-0            0/1     Running   0          10s
my-release-cockroachdb-1            0/1     Running   0          10s
my-release-cockroachdb-2            0/1     Running   0          10s
my-release-cockroachdb-init-djjkb   1/1     Running   0          10s
raj$ k get pvc
NAME                               STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
datadir-my-release-cockroachdb-0   Bound    pvc-b87ac1e4-a9f2-458b-acd7-d82eea6b34ca   100Gi      RWO            standard       13m
datadir-my-release-cockroachdb-1   Bound    pvc-c860a4d6-c842-45e4-9f42-9a6eae34a7d2   100Gi      RWO            standard       13m
datadir-my-release-cockroachdb-2   Bound    pvc-645ff5d5-52ec-4f9a-aa05-f1a7a1c02347   100Gi      RWO            standard       13m
```

<HR>
  
Installed Loadbalancer on Minikube with minikube tunnel command. You can see the output from the service list below...

```
NAME                                    TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                          AGE
service/kubernetes                      ClusterIP      10.96.0.1        <none>           443/TCP                          29m
service/my-release-cockroachdb          ClusterIP      None             <none>           26257/TCP,8080/TCP               15m
service/my-release-cockroachdb-public   LoadBalancer   10.110.179.141   10.110.179.141   26257:32319/TCP,8080:30180/TCP   15m

```
## Added a 4th Node as shown below with Kubectl scale command

```
raj$ k scale statefulset.apps/my-release-cockroachdb --replicas=4
statefulset.apps/my-release-cockroachdb scaled

raj$ k get all
NAME                                    READY   STATUS      RESTARTS   AGE
pod/my-release-cockroachdb-0            1/1     Running     0          17m
pod/my-release-cockroachdb-1            1/1     Running     0          17m
pod/my-release-cockroachdb-2            1/1     Running     0          17m
pod/my-release-cockroachdb-3            1/1     Running     0          80s
pod/my-release-cockroachdb-init-djjkb   0/1     Completed   0          17m
pod/workload-run                        1/1     Running     0          2m15s

NAME                                    TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                          AGE
service/kubernetes                      ClusterIP      10.96.0.1        <none>           443/TCP                          30m
service/my-release-cockroachdb          ClusterIP      None             <none>           26257/TCP,8080/TCP               17m
service/my-release-cockroachdb-public   LoadBalancer   10.110.179.141   10.110.179.141   26257:32319/TCP,8080:30180/TCP   17m

NAME                                      READY   AGE
statefulset.apps/my-release-cockroachdb   4/4     17m

NAME                                    COMPLETIONS   DURATION   AGE
job.batch/my-release-cockroachdb-init   1/1           37s        17m

```
<HR>
<P>
 <img src="4thnodeadd.png">
<HR>
<P>
     
 
