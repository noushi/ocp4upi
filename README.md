reg=nexus-registry-int.apps.tools-na.prod.nextcle.com
releaseimg=/openshift/ocp4:4.6.4-x86_64
export OPENSHIFT_INSTALL_RELEASE_IMAGE_OVERRIDE=nexus-registry-int.apps.tools-na.prod.nextcle.com/openshift/ocp4:4.6.4-x86_64
openshift-install create manifests --dir=.
openshift-install create ignition-configs --dir=.


sudo cp *.ign /var/www/html/openshift4/4.6.4/ignitions/
sudo chmod +r /var/www/html/openshift4/4.6.4/ignitions/*.ign

openshift-install --dir=. wait-for bootstrap-complete
ssh -i /home/lab/.ssh/ocp4ui core@bootstrap.ocp4.example.com
export KUBECONFIG=~/ocp4upi/auth/kubeconfig 
oc whoami



```
$ oc get nodes
NAME       STATUS   ROLES    AGE   VERSION
master01   Ready    master   16m   v1.19.0+9f84db3
master02   Ready    master   15m   v1.19.0+9f84db3
master03   Ready    master   15m   v1.19.0+9f84db3
```



```
$ oc get clusterversion
NAME      VERSION   AVAILABLE   PROGRESSING   SINCE   STATUS
version             False       True          20m     Unable to apply 4.6.4: some cluster operators have not yet rolled out
```


```
$ oc get clusteroperator
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                                       False       False         True       17m
cloud-credential                           4.6.4     True        False         False      19m
cluster-autoscaler                         4.6.4     True        False         False      16m
config-operator                            4.6.4     True        False         False      17m
console                                    4.6.4     Unknown     True          False      13m
csi-snapshot-controller                    4.6.4     True        False         False      17m
dns                                        4.6.4     True        False         False      16m
etcd                                       4.6.4     True        False         False      15m
image-registry                             4.6.4     True        False         False      12m
ingress                                              False       True          True       16m
insights                                   4.6.4     True        False         False      17m
kube-apiserver                             4.6.4     True        False         False      15m
kube-controller-manager                    4.6.4     True        False         False      12m
kube-scheduler                             4.6.4     True        False         False      12m
kube-storage-version-migrator              4.6.4     False       False         False      17m
machine-api                                4.6.4     True        False         False      16m
machine-approver                           4.6.4     True        False         False      17m
machine-config                             4.6.4     True        False         False      8m49s
marketplace                                4.6.4     True        False         False      15m
monitoring                                           False       True          True       13m
network                                    4.6.4     True        False         False      17m
node-tuning                                4.6.4     True        False         False      17m
openshift-apiserver                        4.6.4     True        False         False      12m
openshift-controller-manager               4.6.4     True        False         False      16m
openshift-samples                          4.6.4     True        False         False      14m
operator-lifecycle-manager                 4.6.4     True        False         False      17m
operator-lifecycle-manager-catalog         4.6.4     True        False         False      16m
operator-lifecycle-manager-packageserver   4.6.4     True        False         False      12m
service-ca                                 4.6.4     True        False         False      17m
storage                                    4.6.4     True        False         False      17m
```



```
watch -n 1 'oc get clusterversion ; oc get clusteroperator ; oc get po -A |grep -v -E "Running|Completed" ; oc get csr ; oc get node'
```

```
openshift-install --dir=. wait-for install-complete  --log-level=debug 
```

in another terminal:
```
$ oc get csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                                                                   CONDITION
csr-4ffvr   30m   kubernetes.io/kube-apiserver-client-kubelet   system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Approved,Issued
csr-dg78m   30m   kubernetes.io/kubelet-serving                 system:node:master03                                                        Approved,Issued
csr-dx8pb   32m   kubernetes.io/kubelet-serving                 system:node:master01                                                        Approved,Issued
csr-g7nxr   32m   kubernetes.io/kube-apiserver-client-kubelet   system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Approved,Issued
csr-pw2jg   30m   kubernetes.io/kubelet-serving                 system:node:master02                                                        Approved,Issued
csr-rqj4w   30m   kubernetes.io/kube-apiserver-client-kubelet   system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Approved,Issued
```

if some csr's were not signed:
```
oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve 
```


