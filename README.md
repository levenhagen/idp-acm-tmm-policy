# IDP k8s Policy - RHACM + Policy Generator

The main goal of this repository is to create a k8s Policy using RHACM & Policy Generator.

This policy will provide a basic IDP configuration for managed k8s clusters as a day-2 operation and introduce the possibility to remove the kubeadmin password.

### Pre-requisite:

1. Have the ```policies``` namespace in the hub cluster.

- ```kubectl create ns policies```

2. Create the htpasswd file. 

    - On a bastion machine, create a htpasswd file. Example: 
    - ```$ htpasswd -c -B -b acmusers.htpasswd acmuser letsgoacm$```

3. Create a secret in your OpenShift Hub Cluster by running:

    - ```oc create secret generic acm-htpass-secret --from-file=htpasswd=acmusers.htpasswd -n policies```
    
    The policy will resolve this secret before applying it to managed clusters.


### Steps:

1. Using RHACM, deploy this to the ACM Hub Cluster (local-cluster) by going to the Applications menu, and providing this repository as the source URL and ```main``` as branch.

### Explanation:
 
This policyGenerator will generate a policySet with 4 policies.

1. Policy #1 (add-idp-secret) will add the acm-htpass-secret created in the hub into the desired managed clusters. In this case, the policy will target every cluster with the label ```vendor: OpenShift```
**Note:** this is a templatized policy. If the secret containing the idp credentials gets changed in the hub, consequently it will also change on the managed cluster.
 
2. When policy #1 is compliant(only when policy #1 is compliant), policy #2 (add-acm-idp-provider) will configure a new Identity Provider in the managed clusters, so users can log in with the recently created user.

3. When policy #2 is compliant(only when policy #2 is compliant), policy #3 (add-acm-idp-admin-cluster-role) will add cluster admin role/rights to the recently added user (acmuser).

4. When policy #3 is compliant(only when policy #2 is compliant), policy #4 (remove-kubeadmin-password) will inform if the managed cluster has the kubeadmin password still enabled. You can enforce this policy if you want the policy to remove the kubeadmin pass.

### Note
You can customize and add users as you want in the acm-htpass-secret, but keep in mind some changes may also be required in the 3rd policy (ClusterRole).
