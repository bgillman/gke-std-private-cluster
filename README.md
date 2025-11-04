# GKE Standard Private Cluster Deployment Script

This script automates the creation of a Google Kubernetes Engine (GKE) Standard private cluster. It is designed to provision a secure and production-ready cluster with a variety of features enabled by default.

## Features

The script provisions a GKE cluster with the following features:

*   **Private Cluster:** Worker nodes have no external IP addresses.
*   **Private Control Plane:** The Kubernetes API server is only accessible from authorized networks.
*   **Workload Identity:** For secure authentication of workloads to Google Cloud services.
*   **Shielded Nodes:** Provides verifiable integrity of your nodes.
*   **Custom VPC Networking:** Deploys the cluster into a pre-existing VPC and subnet.
*   **Dataplane V2:** For improved networking performance and security, with metrics and flow observability.
*   **Enhanced Monitoring:** In-depth monitoring for various Kubernetes resources.
*   **Managed Prometheus:** For scraping Prometheus-style metrics.
*   **Gateway API**: The script enables the `standard` channel of the Gateway API.

## Prerequisites

Before running this script, ensure you have the following:

*   `gcloud` CLI installed and authenticated (`gcloud auth login`).
*   A Google Cloud project with the required APIs enabled (the script will attempt to enable them).
*   A pre-existing VPC and subnet.

## Configuration

The script can be configured by setting environment variables. You can copy the commands below and modify the values to suit your needs.

| Variable | Description |
|---|---|
| `export PROJECT_ID="YOUR_PROJECT"` | The Google Cloud project ID. |
| `export REGION="YOUR_REGION"` | The region to create the cluster in. |
| `export ZONE="YOUR_ZONE"` | The zone to create the cluster in. |
| `export CLUSTER_NAME="CLUSTER_NAME` | The name of the GKE cluster. |
| `export CLUSTER_VERSION="1.34.0-gke.2201000"` | The GKE version to use. |
| `export RELEASE_CHANNEL="rapid"` | The GKE release channel. |
| `export VPC_NETWORK="YOUR_VPC_NETWORK` | The VPC network for the cluster. |
| `export VPC_SUBNET="YOUR_VPC_SUBNETWORK"` | The VPC subnetwork for the cluster. |
| `export POD_CIDR="POD_CIDR"` | The IP range for pods. |
| `export SERVICE_CIDR="SERVICES_CIDR"` | The IP range for services. |
| `export MASTER_AUTHORIZED_NETWORKS="YOUR_AUTHORIZED_NETWORK"` | Comma-separated CIDR blocks for master access. |
| `export MACHINE_TYPE="e2-medium"` | The machine type for the nodes. |
| `export NUM_NODES="3"` | The number of nodes in the default node pool. |
| `export DISK_SIZE="100"` | The disk size for the nodes in GB. |
| `export DISK_TYPE="pd-balanced"` | The disk type for the nodes. |
| `export IMAGE_TYPE="COS_CONTAINERD"` | The node image type. |
| `export MAX_PODS_PER_NODE="110"` | The maximum number of pods per node. |
| `export MAX_SURGE_UPGRADE="1"` | The maximum number of nodes that can be created during an upgrade. |
| `export MAX_UNAVAILABLE_UPGRADE="0"` | The maximum number of nodes that can be unavailable during an upgrade. |

## Usage

1.  **Make the script executable:**
    ```bash
    chmod +x create-gke-cluster.sh
    ```

2.  **Run the script:**

    *   **Basic usage (with defaults from the script):**
        ```bash
        ./create-gke-cluster.sh
        ```

    *   **Override cluster name:**
        ```bash
        CLUSTER_NAME="my-production-cluster" ./create-gke-cluster.sh
        ```

    *   **Override authorized networks (add your IP):**
        ```bash
        # Get your current IP
        MY_IP=$(curl -s ifconfig.me)
        MASTER_AUTHORIZED_NETWORKS="<YOUR_EXISTING_CIDR>,${MY_IP}/32" ./create-gke-cluster.sh
        ```

    *   **Override multiple settings:**
        ```bash
        PROJECT_ID="my-gcp-project" \
        CLUSTER_NAME="prod-cluster-01" \
        NUM_NODES="5" \
        MACHINE_TYPE="n2-standard-4" \
        ./create-gke-cluster.sh
        ```

## Post-Creation

After the cluster is created, the script will:

1.  **Configure `kubectl`:** It will fetch the credentials for the new cluster so you can use `kubectl` to interact with it.
2.  **Verify the cluster:** It will run a series of checks to verify that the cluster is configured correctly, including checking node status, Dataplane V2, Workload Identity, and more.
3.  **Display access instructions:** It will print instructions on how to access the cluster's private control plane.
4.  **Display monitoring instructions:** It will provide information on the monitoring features and how to use them.

## Accessing the Cluster

Since this is a private cluster with an authorized network, you can access the Kubernetes API server in the following ways:

*   **From an authorized IP address:** If your current IP is in the `MASTER_AUTHORIZED_NETWORKS` list, you can use `kubectl` directly.
*   **Add your IP to the authorized network:** You can run a `gcloud` command to add your IP to the list.
*   **From Google Cloud Shell:** Cloud Shell is automatically authorized for clusters in the same project.
*   **From a bastion host:** You can set up a bastion host in the same VPC as the cluster.

The script will output detailed instructions on how to do this after the cluster is created.