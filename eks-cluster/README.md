# eks-master.yaml
This CFT automates the steps for creating an EKS cluster. It creates following resources as recommended in best practices from [AWS EKS Documentation (Credit to AWS)](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)
* VPC with 3 public and 3 private subnets
* A single NatGateway to minimize costs
* Required EKS IAM roles
* Required Security groups
* An autoscaling worker node group (1 worker node by default, can be changed at runtime in the CFT)
* An EKS cluster that resides in this VPC
* An EC2 node with all the configuration in place for running kubectl

Once you are done executing this CFT (with a non-root aws account), simply login to KubectlNodePublicIP 
from the output section of this CFT and follow these steps
* `ssh -i "YOUR KEY that you configured while running the CFT" ec2-user@publicIpOfKubectlNode`
* Execute `aws configure` {USE YOUR AWS CREDS THAT YOU USED TO CREATE THIS CFT[SHOULD NOT BE ROOT AWS ACCOUNT]}
* Goto tmp directory on this node `cd /tmp`
* Execute `kubectl apply -f aws-auth.yaml`
* Execute `kubectl get nodes -w` and watch the worker nodes join the cluster and get to READY state (about 30 secs)

# install-helm.sh (optional)
After executing eks-master.yaml and verifying that kubectl is running as expected, copy install-helm to your kubectl node and run `./install-helm.sh`
This will install helm i the cluster. For more help about helm, visit [Helm Documentation](https://docs.helm.sh/).

# kubernetes-dashboard.yaml (optional)
This kubernetes manifest file will setup our kubernetes dashboard in EKS. This manifest has all the steps needed for setting up a LoadBalancer based Kubernetes Dashboard (public access by default).

* Copy kubernetes-dashboard.yaml to your kubectl node
* From your kubectl node, execute `kubectl apply -f kubernetes-dashboard.yaml`

Please note that this only exposes the dashboard on http, thus, the ALB that is created in the output needs to be secured with Security groups that restrict access to the ALB from within a specific CIDR. If it's not secured, and an ALB is created, it will be public facing and can be accessed from anywhere.
