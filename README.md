# airflow-kubernetes-gke
Airflow 1.10.3 helm chart for GKE (Google Kubernetes Engine) with KubernetesExecutor, with dags folder synchronized from git repository

Inspired by https://github.com/BrechtDeVlieger/airflow-kube-helm



# Installation:

1. create namespace airflow
    - `kubectl create namespace airflow`
2. install NFS server with shared folders for logs and dags, with git-sync sidecar for periodic syncing from external git repository (public or private) `GIT_SYNC_WAIT` sync interval is set to 15 seconds default. 
    - `kubectl -n airflow -f nfs/nfs_server_github.yaml`
      if you are using a public github repository
      - edit `GIT_SYNC_REPO` first
  or
    - `kubectl -n airflow -f nfs/nfs_server_private_repository.yaml`
      if you are using a private git repository
      - edit `GIT_SYNC_REPO` first
      - edit data.ssh and data.known_hosts, use the SSH private key and at least the git repo lines from your known_hosts file.
        - data.ssh: `cat .ssh/ida_rsa | base64`
        - data.known_hosts: `cat .ssh/known_hosts | base64`         
3. create NFS persistent volume claims
     - `kubectl -n airflow nfs/volumes_nfs.yaml`
4. optional - edit airflow.fernet_key in values.yaml
     - `python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"`
5. edit dags.git.subpath in values.yaml to reflect your dags directory in git    
6. install airflow
     - `helm upgrade --install airflow . --namespace airflow --values values.yaml`
     
     
airflow should be reachable on any of your Kubernetes node IPs, port 32222
     
     
     
  

