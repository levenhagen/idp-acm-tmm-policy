# IDP k8s Policy - RHACM + Policy Generator

The main goal of this repository is to create a k8s Policy using RHACM & Policy Generator.

The policy will provide a basic IDP configuration for managed k8s clusters as a day-2 operation and remove the kubeadmin password.

### Pre-requisite:

1. Have your own htpasswd file.  

    - On a bastion machine, your can create your own htpasswd file. Example: 
    - ```$ htpasswd -c -B -b acmusers.htpasswd acmusers letsgoacm$```

2. Create a secret in your OpenShift Hub Cluster by running:

    - ```oc create secret generic htpass-secret --from-file=htpasswd=acmusers.htpasswd -n openshift-config```


### Steps:

1. Using RHACM, deploy this by going to the Applications menu, and providing this repository as the source URL.
