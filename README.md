This is short guide provides steps to get setup with cloud managed Kubernetes (aka k8s) provided by [GKE (Google)](https://cloud.google.com/sdk/gcloud/reference/container/clusters).

The fact that k8s is a Google product, unfortunately does not equate to a more easy or prompt experience (in my opinion) and what's more the labyrinth of documentation (or a lack thereof) also doesn't help.

**Prerequisite**: A Google-Cloud account which you're already logged into (via a browser) as well as a Linux / Unix type terminal shell.

## Setup Steps
 **1.** - Log into: [console.cloud.google.com](https://console.cloud.google.com/) - wherein the Kubernetes Engines section should be visible to you via the top left hand side (bar) menu. You can chose to create your cluster via the graphical interface on this page or alternatively using the terminal instructions detailed below. ![url: console.cloud.google.com](/console-cloud-google2.jpg)

 **2.** - Download & Install terminal Command-Line-Interface (CLI) utilities for your OS:
  - - **[Google-Cloud-SDK](https://cloud.google.com/sdk/docs/downloads-versioned-archives#installation_instructions)** - updating your **PATH** variable with the `bin` directory from the above archive such as: `export PATH=~/Downloads/google-cloud-sdk/bin:$PATH` consider adding this to your `.bash_rc` / `.bash_profile` to make it permanently available in future sessions.

  - - **[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)** :warning: **Ensure** that you're using a **matching version** or one that's near to the Kubernetes version of your cluster or you may otherwise encounter incompatibility issues. :warning:

 **3.** - In terminal shell execute **`gcloud init`** to initialise and link account details:
 ```bash
gcloud init ;
 # Welcome! This command will take you through the configuration of gcloud.
 # // ...
 # You must log in to continue. Would you like to log in (Y/n)?  Y
 #
 # Your browser has been opened to visit:
 #
 #     https://accounts.google.com/o/oauth2/auth?...
 #
 #
 # You are logged in as: [aphorise@tld.com].
 #
 # This account has a lot of projects! Listing them all can take a while.
 #  [1] Enter a project ID
 #  [2] Create a new project
 #  [3] List projects
 # Please enter your numeric choice:  3
 # // ...
 # // ...
 # // ...
 # Do you want to configure a default Compute Region and Zone? (Y/n)?  Y
 # // ...
 # // ENDS WITH:
 # Credentials saved to file: [/home/auser/.config/gcloud/application_default_credentials.json]
 #
 # These credentials will be used by any library that requests Application Default Credentials (ADC).
```

 **4.** - Create GKE cluster via **`gcloud container clusters create`** to initialise and link account details:
 ```bash
gcloud container clusters create aphorise-test --enable-stackdriver-kubernetes --enable-stackdriver-kubernetes --subnetwork default ; # // --num-nodes N --disk-size N ...
 # WARNING: Currently VPC-native is not the default mode during cluster creation. In the future, this will become the default mode and can be disabled using `--no-enable-ip-alias` flag.  Use # `--[no-]enable-ip-alias` flag to suppress this warning.
 # WARNING: Newly created clusters and node-pools will have node auto-upgrade enabled by default. This can be disabled using the `--no-enable-autoupgrade` flag.
 # WARNING: Starting with version 1.18, clusters will have shielded GKE nodes by default.
 # WARNING: Your Pod address range (`--cluster-ipv4-cidr`) can accommodate at most 1008 node(s).
 # This will enable the autorepair feature for nodes. Please see https://cloud.google.com/kubernetes-engine/docs/node-auto-repair for more information on node autorepairs.
 # Creating cluster aphorise-test in europe-west4-a... Cluster is being deployed...⠛
 # // ...
 # Creating cluster aphorise-test in europe-west4-a... Cluster is being health-checked (master is healthy)...done.
```

 **5.** - List GKE clusters using **`gcloud container clusters list`**:
 ```bash
gcloud container clusters list ;
 # NAME        LOCATION        MASTER_VERSION  MASTER_IP      MACHINE_TYPE   NODE_VERSION   NUM_NODES  STATUS
 # aphorise-test  europe-west4-a  1.15.9-gke.22   34.254.254.187  n1-standard-1  1.15.9-gke.22  3          RUNNING
```
 
 **6.** - Get Credentials (`~/.kube/config`) for the created clusters with **`gcloud container clusters get-credentials ...NAME...`** eg:
 ```bash
gcloud container clusters get-credentials aphorise-test ;
 # Fetching cluster endpoint and auth data.
 # kubeconfig entry generated for aphorise-test.

# // ^^^ repeat for all other cluster names
```

 **7.** - Check that invoking **`kubectl ...`** works as expected - eg:
 ```bash
kubectl cluster-info ;
 # Kubernetes master is running at https://34.254.254.187
 # GLBCDefaultBackend is running at https://34.254.254.187/api/v1/namespaces/kube-system/services/default-http-backend:http/proxy
 # Heapster is running at https://34.254.254.187/api/v1/namespaces/kube-system/services/heapster/proxy
 # KubeDNS is running at https://34.254.254.187/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
 # Metrics-server is running at https://34.254.254.187/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
 # 
 # To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

# // can also do:
kubectl get nodes -o wide ;
kubectl get pods --all-namespaces -o wide ; # // etc ...
```

 **8.** - Cluster deletion using **`gcloud container clusters delete ...NAME...`** - eg:
 ```bash
gcloud container clusters delete --quiet aphorise-test ; # // similar to force delete will not prompt for yes/no
 # Deleting cluster aphorise-test...⠹
 # Deleted [https://container.googleapis.com/v1/projects/...].
```


## Continuation & other recommended tools
Having successfully completed the above steps to create your cluster - you should now be able to perform all other common operations such as `kubectl apply -f FILE.yaml`.

The tool **[yamllint](https://yamllint.readthedocs.io/en/stable/)** is very useful and should be considered as part of your toolset and procedures where authoring or editing `.yaml` file manifests for usage with k8s. It's often easier to understand error message from yamllint before attempting to apply them to k8s API rather than applying them to k8s and receiving a different or less clear error message.  

If you're interacting with multiple Kubernetes clusters then you may switch between with using:
```
gcloud container clusters get-credentials ...CLUSTER_NAME... ;
# // sets: current-context: ... within your ~/.kube/config
```
A better alternative may be to consider **[kubectx](https://github.com/ahmetb/kubectx)** with which you can leverage commands **`kubectx`** to list or switch between clusters and **`kubens`** similar for namespaces with each cluster.


## Reference
 * [Get Started with the Google Kubernetes Engine (GKE)](https://docs.bitnami.com/kubernetes/get-started-gke/)

------