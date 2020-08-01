---
title: "📔 Helpful gists"
date: "2019-09-12"
author: grahamplata
excert: "Helpful gists"
tags: ["gists"]
---

## Proxy React App

Quickly Proxy a React App to a local api

Add the "proxy" property to package.json:

```json
{
  "name": "app",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^16.9.0",
    "react-dom": "^16.9.0",
    "react-scripts": "3.1.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  },
  "proxy": "http://localhost:<PORT-OF-API-HERE>"
}
```

Edit http requests to follow

```javascript
axios.get("http://localhost:<PORT-OF-API-HERE>/example");
// or
fetch("http://localhost:<PORT-OF-API-HERE>/example");
```

To

```javascript
axios.get("/example");
// or
fetch("/example");
```

## Resize EC2 Root Volume

- Power down services
  - Disable anything that has a + or is listed as start/running with the appropriate commands.
  - Obviously environements will differ
  - ```bash
    #Check services and shutdown any needed
    sudo service --status-all
    ```
- Create a snapshot of the EBS volume you need to resize.
- Create the new volume with the id of the snapshot you created in the previous step.
  - Make sure to create the new volume in the same datacenter.
- Attach new EBS Volume
  - Detach the old volume and attach the new volume to the root filesystem `/dev/sda1`
  - After you do this you will need to go to the Elastic IP tab and reattach the EIP to the EC2 instance.
  - Once everything is reattached you are good to restart the EC2 instance.
- Resizing

  - Restart and reconnect to the EC2 instance
  - Run `sudo df -h`

  - ```bash
    graham@node:~$ sudo df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/sda1        15G  9.5G  4.0G  71% /
    ```

  - Run `resize2fs /dev/sda1`
  - Re-Run `sudo df -h`

  - ```bash
    graham@node:~$ sudo df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/sda1        30G  9.5G  20.5G  31% /
    ```

    - After this you’re good to restart services and start your application.

---

## Install R Packages

- R Studio
- GitHub and a Personal Access Token

### Private Repository prerequisites

Note: If you wish to install from a private repo you need a token from your [GitHub Settings](https://github.com/settings/tokens/). Otherwise feel free to skip this section.

Set a `GITHUB_PAT` environement variable

Note: Don't commit your tokens!

```R
Sys.setenv(GITHUB_PAT="PUT_YOUR_TOKEN_HERE")
```

or in your `.Renviron`

```R
GITHUB_PAT=PUT_YOUR_TOKEN_HERE
```

Note: You will need to sign-in and start a new R session for the environment variable (accessed by Sys.getenv) to be visible.

### Required libraries

Baked into the R devtools package there lives a function to install R packages hosted on GitHub.

```R
install.packages("devtools") # Install latest devtools from CRAN
library(devtools)            # Load/Import devtools into working session
```

### Install a package

This exposes the `install_github()` function for use. Listed below are the arguments we are interested in populating.

```R
repo        # Repository address format username/package
ref         # Commit, tag, or branch name. Default value is Master
auth_token  # User personal access token
```

Our Final Command should look like this

```R
install_github(repo="username/package", auth_token=Sys.getenv("GITHUB_PAT"))
```

Note: There are handlers for all flavors of version control.

```R
install_github() from GitHub
install_gitlab() from GitLab
install_bitbucket() from Bitbucket
install_url() from an arbitrary url
install_git() and install_svn() from an arbitrary git or SVN repository
install_local() from a local file on disk
install_version() from a specific version on CRAN
```

### Update a package

`update_packages()` updates a package to the latest version.
This works both on packages installed from CRAN as well as those installed from any of the functions listed above.

### Development

Add `ref` parameter to the function with the specific branch name in which you want to develop.
This can be a branch, tag, or commit.

```R
install_github(repo="username/package", ref="develop", auth_token=Sys.getenv("GITHUB_PAT"))
```

### Distributing a package build privately

- RStudio Shared Directory: Save the R package source code in a `/shared-folder/`.
- Users can install these packages by navigation to the toolbar menu and executing.

  - Option 1: `Tools > Install Packages > Select Build from picklist`
  - Option 2: `use the install.packages('<path>') function from the command line.`

### Resources

- [devtools](https://github.com/r-lib/devtools/) - Tools to make an R developer's life easier
- [github docs](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line) - Creating a personal access token for the command line

## RabbitMQ Recovery from Mnesia Tables

### Cause

An unexpected shutdown caused RabbitMQ node(s) to get stuck in a "Waiting for Mnesia tables" state.

### Fixes

#### Node

Stop rabbit and force boot

```shell
rabbitmqctl stop_app && rabbitmqctl force_boot
```

#### K8s

Stop rabbit and force boot

```shell
kubectl exec -it -n <NAMESPACE> <POD> -c -c rabbitmq-ha -- bash -c "rabbitmqctl stop_app && rabbitmqctl force_boot"
```

Force a reload

```shell
kubectl exec -it -n <NAMESPACE> <POD> -c -c rabbitmq-ha -- bash -c "cd /var/lib/rabbitmq/mnesia/rabbit@rabbitmq-ha.rabbitmq-ha-discovery.rabbit.svc.cluster.local && touch force_load && chown rabbitmq: force_load"
```

## ETCD Cluster back and restore

[etcd](https://github.com/etcd-io/etcd) is a consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.

Check the pod configuration with kubectl

- `kubectl get pods` locate the etcd pod
- `kubectl describe pod/etcd-pod` describe the pod
- Look for the command field. This will contain the cluster configuration.

```shell
command:
- |
  exec etcd --name ${HOSTNAME} \
    --listen-peer-urls http://127.0.0.1:2380 \
    --listen-client-urls http://127.0.0.1:2379 \
    --advertise-client-urls http://127.0.0.1.etcd:2379 \
    --initial-advertise-peer-urls http://127.0.0.1:2380 \
    --initial-cluster-token etcd-cluster-1 \
    --initial-cluster-state new \
    --data-dir /var/run/etcd/default.etcd
```

### Locating etcd configuration

```shell
# declare an api version as env var
export ETCDCTL_API=3
```

### Backup

```shell
# backup etcd with the etcdctl tool
etcdctl --endpoints=https://ip-of-master:2379 \
  --key=/path/to/server.key \
  --cacert=/path/to/ca.crt \
  --cert=/path/to/server.crt \
  snapshot save /path/to/snapshot.db
```

### Restore

```shell
# restore etcd with the etcdctl tool
ETCDCTL_API=3 etcdctl --endpoints=https://ip-of-master:2379
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --name=master \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --data-dir /var/lib/etcd-from-backup \
  --initial-cluster=master=https://127.0.0.1:2380 \
  --initial-cluster-token=etcd-cluster-1 \
  --initial-advertise-peer-urls=https://127.0.0.1:2380 \
  snapshot restore /tmp/snapshot-pre-boot.db
```
