# AWS-EKS
The task of Aws_EKS
Hey all , Thank you for visiting my page. Do you know about EKS ? yes, I think you heard anywhere . If not then don't worry we will first talk about EKS (I mean to say Amazon Elastic Kubernets Service ). you have some questions i know, may be the first question is :

What is Amazon-EKS?

Ans: Amazon Elastic Kubernetes Service (Amazon EKS) is a fully managed Kubernetes service.

Customers such as Intel, Snap, Intuit, GoDaddy, and Autodesk trust EKS to run their most sensitive and mission critical applications because of its security, reliability, and scalability.

EKS is the best place to run Kubernetes for several reasons. First, you can choose to run your EKS clusters using AWS Fargate, which is serverless compute for containers. Fargate removes the need to provision and manage servers, lets you specify and pay for resources per application, and improves security through application isolation by design. Second, EKS is deeply integrated with services such as Amazon CloudWatch, Auto Scaling Groups, AWS Identity and Access Management (IAM), and Amazon Virtual Private Cloud (VPC), providing you a seamless experience to monitor, scale, and load-balance your applications. Third, EKS integrates with AWS App Mesh and provides a Kubernetes native experience to consume service mesh features and bring rich observability, traffic controls and security features to applications. Additionally, EKS provides a scalable and highly-available control plane that runs across multiple availability zones to eliminate a single point of failure.

EKS runs upstream Kubernetes and is certified Kubernetes conformant so you can leverage all benefits of open source tooling from the community. You can also easily migrate any standard Kubernetes application to EKS without needing to refactor your code. ( from internet)

What we gonna do here with Amazon-EKS ?

We are going to launch a open source blogging architecture on the Amazon-EKS.

Why am i doing this ?

This project is a Task given in the Amazon-EKS training by World Record holder MR. VIMAL DAGA. Who is our mentor, and teaches so deep concepts that's the reason not only me but other my co-learners are doing such amazing Task/Project .

So , let's get started to the amin work...

First of all, we need to launch our EKS cluster on AWS Cloud. I have launched a simple cluster with three nodes by aws CLI. First, i have configured a profile.

aws configure --profile ashu
AWS Access Key ID [****************5KMO]:
AWS Secret Access Key [****************VpMS]:
Default region name [us-east-1]:
Default output format [None]:

Then i created the first yml file for launching the nodes.

  apiVersion: eksctl.io/v1alpha5
  kind: ClusterConfig

  metadata:
    name: mycluster
    region: us-east-1

  nodeGroups:
     - name: ng1
       desiredCapacity: 3
       instanceType: t2.micro


And then we will use FARGATE by AWS . Now you have a question , What is FARGATE and why it is used ? so answer is

AWS Fargate is a serverless compute engine for containers that works with both Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS). Fargate makes it easy for you to focus on building your applications. Fargate removes the need to provision and manage servers, lets you specify and pay for resources per application, and improves security through application isolation by design.

Fargate allocates the right amount of compute, eliminating the need to choose instances and scale cluster capacity. You only pay for the resources required to run your containers, so there is no over-provisioning and paying for additional servers. Fargate runs each task or pod in its own kernel providing the tasks and pods their own isolated compute environment. This enables your application to have workload isolation and improved security by design. This is why customers such as Vanguard, Accenture, Foursquare, and Ancestry have chosen to run their mission critical applications on Fargate.(from internet)

And code for that is :

apiVersion: eksctl.io/v1alpha5
  kind: ClusterConfig

  metadata:
    name: myfargatecluster
    region: us-east-1

  fargateProfiles:
    - name: fargatedef
      selectors:
       - namespace: kube-system
       - namespace: default


So , We are done with our first mile stone in our way. i.e. our Cluster is launched succesfully








Add alt text
No alt text provided for this image
After this, we have to run the following command in my command prompt -

aws eks update-kubeconfig --name mycluster


Now what? Now we have to create a EFS storage. wait wait wait , i know you have a question now what is the Amazon-EFS storage? so the answer is :

Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic NFS file system for use with AWS Cloud services and on-premises resources. It is built to scale on demand to petabytes without disrupting applications, growing and shrinking automatically as you add and remove files, eliminating the need to provision and manage capacity to accommodate growth.

Amazon EFS offers two storage classes: the Standard storage class, and the Infrequent Access storage class (EFS IA). EFS IA provides price/performance that's cost-optimized for files not accessed every day. By simply enabling EFS Lifecycle Management on your file system, files not accessed according to the lifecycle policy you choose will be automatically and transparently moved into EFS IA. The EFS IA storage class costs only $0.025/GB-month*.

While workload patterns vary, customers typically find that 80% of files are infrequently accessed (and suitable for EFS IA), and 20% are actively used (suitable for EFS Standard), resulting in an effective storage cost as low as $0.08/GB-month*. Amazon EFS transparently serves files from both storage classes in a common file system namespace.

Amazon EFS is designed to provide massively parallel shared access to thousands of Amazon EC2 instances, enabling your applications to achieve high levels of aggregate throughput and IOPS with consistent low latencies.

Amazon EFS is well suited to support a broad spectrum of use cases from home directories to business-critical applications. Customers can use EFS to lift-and-shift existing enterprise applications to the AWS Cloud. Other use cases include: big data analytics, web serving and content management, application development and testing, media and entertainment workflows, database backups, and container storage.

Amazon EFS is a regional service storing data within and across multiple Availability Zones (AZs) for high availability and durability. Amazon EC2 instances can access your file system across AZs, regions, and VPCs, while on-premises servers can access using AWS Direct Connect or AWS VPN.

*pricing in US East (N. Virginia) region, assumes 80% of your storage in EFS IA

So i think you got an idea about EFS and here we created the same.








Add alt text
No alt text provided for this image
It will be used by the PVC later, and if you know About EBS(Elastic Block Storage) then you think why we used here EFS why not EBS . So the main reason is EBS storage can't be mounted to an instance running in any other subnet. and in EFS we are provided this facility.

and for those , are not aware of EBS they may think what am i talking about . so the answer of your question is here:

Amazon Elastic Block Store (EBS) is an easy to use, high performance block storage service designed for use with Amazon Elastic Compute Cloud (EC2) for both throughput and transaction intensive workloads at any scale. A broad range of workloads, such as relational and non-relational databases, enterprise applications, containerized applications, big data analytics engines, file systems, and media workflows are widely deployed on Amazon EBS.

You can choose from four different volume types to balance optimal price and performance. You can achieve single digit-millisecond latency for high performance database workloads such as SAP HANA or gigabyte per second throughput for large, sequential workloads such as Hadoop. You can change volume types, tune performance, or increase volume size without disrupting your critical applications, so you have cost-effective storage when you need it.

Designed for mission-critical systems, EBS volumes are replicated within an Availability Zone (AZ) and can easily scale to petabytes of data. Also, you can use EBS Snapshots with automated lifecycle policies to back up your volumes in Amazon S3, while ensuring geographic protection of your data and business continuity.

The EFS is created now what ? Now we have to create an EFS provisioner. and why this is needed to be done?

Becacause , "The efs-provisioner allows you to mount EFS storage as PersistentVolumes in kubernetes. It consists of a container that has access to an AWS EFS resource. The container reads a configmap which contains the EFS filesystem ID, the AWS region and the name you want to use for your efs-provisioner.

So as we are working through console we will create a yml file for this and the code is

  kind: Deployment
  apiVersion: apps/v1
  metadata:
    name: my-efs-prov
  spec:
    selector:
      matchLabels:
        app: my-efs-prov
    replicas: 1
    strategy:
      type: Recreate
    template:
      metadata:
        labels:
          app: my-efs-prov
      spec:
        containers:
          - name: my-efs-prov
            image: quay.io/external_storage/my-efs-prov:v0.1.0
            env:
              - name: FILE_SYSTEM_ID
                value: fs-d4zy443b
              - name: AWS_REGION
                value: us-east-1
              - name: PROVISIONER_NAME
                value: projecteks/my-efs
            volumeMounts:
              - name: pv-volume
                mountPath: /persistentvolumes
        volumes:
          - name: pv-volume
            nfs:
              server: fs-e8ab886b.efs.us-east-1.amazonaws.com
              path: /


Now we will have to enter inside the nodes and we have to run the command "- yum install amazon-efs-utils" why? So my answer is because , it will install all the software or tools inside the nodes which will be needed for working with the EFS. after this, What will we do?

Ans: we will create a yml file which will modify the permission through Role Based Access Control (RBAC). I know what are you thinking about ? so i am going first to tell you about the RBAC.

Amazon Cognito identity pools assign your authenticated users a set of temporary, limited privilege credentials to access your AWS resources. The permissions for each user are controlled through IAM roles that you create. You can define rules to choose the role for each user based on claims in the user's ID token. You can define a default role for authenticated users. You can also define a separate IAM role with limited permissions for guest users who are not authenticated.

And the Code is

 apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRoleBinding
    metadata:
      name: nfs-prov-role-bind
    subjects:
      - kind: ServiceAccount
        name: default
        namespace: ashu
    roleRef:
      kind: ClusterRole
      name: cluster-admin
      apiGroup: rbac.authorization.k8s.io


It is done now what ? Now we have to create a storage class which will further create a PVC and a dynamic PV also. Yes i know you have two questions so their answers are :

PVC: A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. It is a resource in the cluster just like a node is a cluster resource. PVs are volume plugins like Volumes, but have a lifecycle independent of any individual Pod that uses the PV. This API object captures the details of the implementation of the storage, be that NFS, iSCSI, or a cloud-provider-specific storage system.

A PersistentVolumeClaim (PVC) is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany or ReadWriteMany, see AccessModes).

While PersistentVolumeClaims allow a user to consume abstract storage resources, it is common that users need PersistentVolumes with varying properties, such as performance, for different problems. Cluster administrators need to be able to offer a variety of PersistentVolumes that differ in more ways than just size and access modes, without exposing users to the details of how those volumes are implemented. For these needs, there is the StorageClass resource.

Dynamic volume provisioning allows storage volumes to be created on-demand. Without dynamic provisioning, cluster administrators have to manually make calls to their cloud or storage provider to create new storage volumes, and then create PersistentVolume objects to represent them in Kubernetes. The dynamic provisioning feature eliminates the need for cluster administrators to pre-provision storage. Instead, it automatically provisions storage when it is requested by users.

The code is :

    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
      name: my-efs
    provisioner: projecteks/my-efs
    ---
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: ghostefs
      annotations:
        volume.beta.kubernetes.io/storage-class: "my-efs"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 10Gi
    ---
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: mysqlefs
      annotations:
        volume.beta.kubernetes.io/storage-class: "my-efs"
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        requests:
          storage: 10Gi

So it's done . Now what will we gonna do ? now we will run some commands which will run yml file what we have created earlier :

#first command in that folder where we have fargatefile.yml

kubectl create ns ashu



#2nd in project folder where we have efs-prov.yml

kubectl create -f efs-prov.yml -n ashu


#3rd in also in the same folder 

kubectl create -f myrbac.yml -n ashu


#4th in same project folder 

kubectl create -f my-storage.yml -n ashu

Now we create our Secret , Whoaaaa !!!! Secret !!!!! what is it ? why it is needed ?this will be needed for the Ghost & MYSQL launching codes. we can not show our real passwords and secret thing but a demo we can give here :

  apiVersion: v1
  kind: Secret
  metadata:
    name: mysecret
  data:
      dcpassword: oursecret
      dcdatabase: raazkiekbaat==
      sqlrootpassword: secretsecret
      sqluser: yesecrethai
      sqlpassword: secrethaiye
      sqldb: secrethirhnedo==


Our secret is created. now we will launch the MYSQL which will be connected to the Ghost !!! here don't be afraid ghost is blogging site !! ok so we need to create a file :

  apiVersion: v1
  kind: Service
  metadata:
    name: my-ghost
    labels:
      app: ghost
  spec:
    ports:
      - port: 3306
    selector:
      app: ghost
      tier: mysql
    clusterIP: None
  ---
  apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
  kind: Deployment
  metadata:
    name: my-ghost
    labels:
      app: ghost
  spec:
    selector:
      matchLabels:
        app: ghost
        tier: mysql
    strategy:
      type: Recreate
    template:
      metadata:
        labels:
          app: ghost
          tier: mysql
      spec:
        containers:
        - image: mysql:5.7
          name: mysql
          env:
          - name: MYSQL_ROOT_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysecret
                key: sqlrootpassword
          - name: MYSQL_USER
            valueFrom:
              secretKeyRef:
                name: mysecret
                key: sqluser
          - name: MYSQL_PASSWORD
            valueFrom:
              secretKeyRef:
                name: mysecret
                key: sqlpassword
          - name: MYSQL_DATABASE
            valueFrom:
              secretKeyRef:
                name: mysecret
                key: sqldb


          ports:
          - containerPort: 3306
            name: mysql
          volumeMounts:
          - name: mysql-per-storage
            mountPath: /var/lib/mysql
        volumes:
        - name: mysql-per-storage
          persistentVolumeClaim:
            claimName: efs-mysql


Now the journey end after this step , because we finally going to create last yml file which will launch our Ghost !!! connect it to our MYSQL so let's get started.

    apiVersion: v1
    kind: Service
    metadata:
      name: ghost
      labels:
        app: ghost
    spec:
      ports:
        - port: 80
      selector:
        app: ghost
        tier: frontend
      type: LoadBalancer
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: ghost
      labels:
        app: ghost
    spec:
      selector:
        matchLabels:
          app: ghost
          tier: frontend
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: ghost
            tier: frontend
        spec:
          containers:
          - image: ghost:1-alpine
            name: ghost
            env:
            - name: database_client
              value: mysql
            - name: database_connection_host
              value: db_ghost
            - name: database_connection_user
              value: sparsh
            - name: database_connection_password
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: dcpassword
            - name: database_connection_database
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: dcdatabase
            ports:
            - containerPort: 2368
              name: ghost
            volumeMounts:
            - name: ghost-pers-storage
              mountPath: /var/lib/ghost
          volumes:
          - name: ghost-pers-storage
            persistentVolumeClaim:
              claimName: efs-ghost


Now we have to run some commands to run as shown below:

#1st command to run is 

kubectl create -f secret.yml -n ashu


#2nd command to run is 

kubectl create -f mysqldp.yml -n ashu


#3rd command is 

kubectl create -f myghost.yml -n ashu
 Now All works done and its time to show the result:
 
