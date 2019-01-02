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

