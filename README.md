# CEPH Filesystem Implementation

<br>

*CEPH is a powerful, open-source, distributed storage system that provides scalable object storage, block storage, and file system storage. It's designed to offer high availability, fault tolerance, and scalability, making it suitable for large-scale deployments like cloud storage, big data, and distributed systems. Let’s dive into the theoretical concepts behind Ceph and its different components.*


## 1. Overview of CEPH

*Ceph is a distributed storage system that can handle petabytes of data across thousands of nodes. It was created to address the limitations of traditional storage systems, such as centralized bottlenecks, limited scalability, and failure-prone architectures.*

  - **CEPH provides three types of storage:**
    
- **Object Storage:** Typically used for cloud storage applications (e.g., Amazon S3, OpenStack Swift).

- **Block Storage:** Similar to virtualized storage devices (e.g., AWS EBS, RBD).

- **File System Storage (CephFS):** A POSIX-compliant file system that provides a shared file system across a distributed cluster.


## Key components of CEPH include:

- **Monitors (MONs):** Maintain the state and health of the cluster, managing cluster membership and mapping.

- **Object Storage Daemons (OSDs):** Handle the storage of data, responsible for storing objects and managing replication, recovery, and rebalancing of data.

- **Managers (MGRs):** Provide additional management features, such as monitoring, balancing, and offering a web-based dashboard.

- **Ceph Clients:** Interface with the Ceph system, such as Rados Block Devices (RBD) or CephFS file system.

## 2. Architecture of CEPH

*Ceph operates in a highly distributed and decentralized manner. Unlike traditional storage systems, Ceph avoids a single point of failure by using replication and the CRUSH (Controlled Replication Under Scalable Hashing) algorithm.*

- **Monitors (MONs)**
  
*Monitors are responsible for the overall health of the Ceph cluster. They maintain the cluster map, which tracks all objects, OSDs, and other components. Monitors also ensure the consistency and quorum of the cluster. Typically, there are an odd number of monitors (3, 5, 7, etc.) to avoid split-brain scenarios.*

- **Object Storage Daemons (OSDs)**

*OSDs are the heart of the Ceph storage system. Each OSD is associated with a physical disk or storage device. These daemons are responsible for storing objects, handling read/write requests, managing replication, and performing recovery operations when failures occur. Data in Ceph is split into objects, each of which is assigned to an OSD.*

*OSDs use replication or erasure coding to ensure fault tolerance. Replication involves maintaining multiple copies of each object, while erasure coding splits data into fragments and adds redundancy for efficient storage.*

- **CEPH Managers (MGRs)**

*Ceph Managers provide cluster-wide management functionality and maintain the cluster's health. They collect and display monitoring metrics, manage resource allocation, and provide a web-based interface (Ceph Dashboard) for administrators.*

*MGRs also perform tasks like balancing data across OSDs, managing placement groups (PGs), and handling client requests.*

- **CRUSH Algorithm**

*The CRUSH algorithm is the core of Ceph’s data placement and distribution mechanism. It maps logical objects to physical locations (OSDs) in the cluster based on rules defined in the CRUSH map. This enables Ceph to distribute data across many nodes in a scalable manner, without requiring a centralized metadata server, which eliminates a single point of failure.*


## 3. CEPH Storage Types
  *Ceph supports three main storage types: Object Storage, Block Storage, and File System Storage.*
  
- **Object Storage (RADOS Gateway - RGW)**

*CEPH’s object storage system is exposed to clients via the Rados Gateway (RGW), which provides an S3 and Swift-compatible interface. RGW is used to store and manage objects like files, videos, and images. It is commonly used in cloud storage solutions.*

   - Data in RGW is stored as objects.
   - S3/Swift Compatibility: Applications and tools that use the Amazon S3 API can directly interact with Ceph’s object storage.


- **Block Storage (Rados Block Device - RBD)**

*CEPH also provides block-level storage known as Rados Block Device (RBD), which can be used as a raw block device or formatted with a file system. RBD is highly scalable and is commonly used for virtual machine storage in cloud environments.*

   - Use Cases: Cloud environments (like OpenStack), databases, and virtual machines.

   - Features: Snapshots, cloning, and thin provisioning.

- **CEPH File System (CephFS)**

*CephFS is a distributed file system that is POSIX-compliant, meaning it supports standard file operations like file locks, directories, and symbolic links. CephFS is mounted on clients, allowing them to access the storage as a shared file system across multiple nodes.*

   - High Availability: Through replication and failover mechanisms, CephFS ensures data is always available.

   - Performance: CephFS scales with the addition of more OSDs and monitors.

## 4. Data Distribution and Replication

*CEPH distributes data across nodes using the CRUSH algorithm, which helps Ceph achieve high scalability and fault tolerance. The data is divided into objects, which are then replicated across different OSDs based on predefined rules.*

- **Replication**

*In a replication setup, CEPH maintains multiple copies of each object across different OSDs. The default replication factor is 3, meaning that every object is stored in three different locations. Replication ensures high availability and redundancy, even if an OSD or node fails.*

- **Erasure Coding**

*Erasure coding is an alternative to replication and is more efficient in terms of storage. It splits data into fragments, adds redundant parity blocks, and stores these fragments across multiple OSDs. This allows Ceph to achieve fault tolerance while using less storage than replication.*

## 5. Placement Groups (PGs) and Pools

  - *Placement Groups (PGs): PGs are a logical grouping of objects that help in mapping data to physical OSDs. Each object in Ceph is assigned to a PG, which is then mapped to a set of OSDs by the CRUSH algorithm. The use of PGs allows Ceph to manage the distribution of data more efficiently.*

   - Pools: *Pools are logical collections of PGs. A pool defines the replication or erasure coding rules for a group of objects. Different pools can have different replication factors and can be used for different types of data (e.g., one pool for high-priority data with more replicas, another for archival data with erasure coding).*

## 6. Ceph Cluster Scaling and Fault Tolerance

*CEPH is designed to scale horizontally. By adding more nodes (OSDs and MONs), Ceph can grow without disruption. The distributed nature of Ceph ensures that failures of individual nodes do not cause data loss or downtime.*

   - Fault Tolerance: Data is replicated (or encoded) across multiple OSDs to ensure fault tolerance. If an OSD fails, Ceph can automatically restore the lost data using replication or erasure coding.

   - Self-Healing: Ceph continuously monitors the health of its components. When failures occur, Ceph automatically recovers the data and rebalances the system.


## 7. Advanced CEPH Concepts

  *Ceph Rados (Reliable Autonomic Distributed Object Store)*

*The core of Ceph is RADOS, which provides the object store that handles all the underlying data in the system. RADOS manages data replication, recovery, and rebalancing.*

- **CRUSH Map Customization**

*The CRUSH map allows administrators to define custom rules for data placement. For instance, administrators can configure data to be stored in certain racks, failure domains, or regions. This flexibility helps in optimizing the placement of data for both performance and fault tolerance.*

- **Ceph Monitoring and Management**

*Monitoring and managing a Ceph cluster is crucial for maintaining its health and performance. Ceph provides several tools for this purpose:*

   - Ceph Dashboard: A web-based graphical interface for monitoring and managing the cluster.

   - Ceph CLI Tools: Commands like ceph -s (status), ceph health (health check), and ceph osd tree (OSD distribution) are used for real-time monitoring.

- **Ceph in Virtualized and Cloud Environments**
  
*Ceph is widely used in virtualized environments like OpenStack and Kubernetes for storage. It integrates with these platforms to provide scalable and resilient storage for virtual machines, containers, and other workloads.*

### Conclusion

*Ceph provides a highly scalable and fault-tolerant storage system that combines object, block, and file system storage into a single unified platform. Through its distributed architecture, data placement algorithms (CRUSH), and self-healing capabilities, Ceph can handle vast amounts of data with minimal downtime. As organizations scale their infrastructure, understanding the underlying architecture and advanced features of Ceph becomes critical for optimizing performance, ensuring availability, and managing large-scale deployments.*



<br>
<br>


## ********************* Implementation ***********************

<br>
<br>


## ************************ SERVER SETUP ***************************


## Servers:  (Make any Number of server just copy the server commands )

### Step 1: Install Ceph Repository on All Nodes

   - *Adding Ceph repository and installing dependencies:* The centos-release-ceph-squid package is installed to access the Ceph packages. Podman is also required, and CephADM is the tool for managing the Ceph cluster.

```yml

sudo yum install -y centos-release-ceph-squid podman

sudo yum update -y

sudo yum install -y cephadm python3-jinja2

```


**Update** /etc/hosts **on all nodes:** Make sure that all nodes (including the client node) are added to the /etc/hosts file for proper DNS resolution.

```yml

echo "192.168.230.xxx node1"  | sudo tee -a /etc/hosts
echo "192.168.230.xxx node2"  | sudo tee -a /etc/hosts
echo "192.168.230.xxx node3"  | sudo tee -a /etc/hosts
echo "192.168.230.xxx client" | sudo tee -a /etc/hosts

```

### Step 2: SSH Configuration
  **SSH keys:** SSH keys must be generated and distributed across nodes for password-less login.

```yml

ssh-keygen

ssh-copy-id root@node-1
ssh-copy-id root@node-2
ssh-copy-id root@node-3

```

## Step 3: Ceph Cluster Deployment

  *Bootstrap the Ceph cluster on the admin node: The cephadm tool is used to bootstrap the cluster. You need to specify the IP of the monitor.*
  
```yml
cephadm bootstrap --mon-ip 192.168.1.101    # change with your IP      
```

- *Copy the configuration to other nodes: To propagate the configuration and keyring to other nodes, use the cephadm shell and ssh-copy-id to transfer the public key.*

```yml

/usr/sbin/cephadm shell ceph cephadm get-pub-key > ceph.pub

ssh-copy-id -f -i ceph.pub root@node2

ssh-copy-id -f -i ceph.pub root@node3

```

- *Add OSDs (Object Storage Daemons): OSDs are added to each node, specifying the devices for storage (e.g., /dev/nvme1, /dev/nvme2).*

  - *Add Hosts to Ceph Cluster*

```yml

/usr/sbin/cephadm shell ceph orch host add node1
/usr/sbin/cephadm shell ceph orch host add node2
/usr/sbin/cephadm shell ceph orch host add node3

```
  - *Add OSDs to Each Node*
    
      - *Specify the storage devices and run these commands:*

```yml

  /usr/sbin/cephadm shell ceph orch daemon add osd node1:/dev/nvme1
  /usr/sbin/cephadm shell ceph orch daemon add osd node1:/dev/nvme2
  /usr/sbin/cephadm shell ceph orch daemon add osd node2:/dev/nvme1
  /usr/sbin/cephadm shell ceph orch daemon add osd node2:/dev/nvme2
  /usr/sbin/cephadm shell ceph orch daemon add osd node3:/dev/nvme1
  /usr/sbin/cephadm shell ceph orch daemon add osd node3:/dev/nvme2

```

- *Check Ceph cluster status: Check the health and status of the Ceph cluster after adding the OSDs.*

```yml

cephadm shell ceph health
cephadm shell ceph -s

```

- *Create and configure CephFS (Ceph File System): Create pools and a new CephFS filesystem.*
  
```yml

cephadm shell ceph osd pool create cephfs_metadata 8

cephadm shell ceph osd pool create cephfs_data 8

cephadm shell ceph fs new cephfs cephfs_metadata cephfs_data

cephadm shell ceph fs ls

```

# ************************ CLIENT SETUP ***************************

## Client:

### Step 4: Ceph Client Setup

- *Install Ceph common:* On the client node, install Ceph's common packages so the client can interact with the cluster.

```yml

sudo yum install -y centos-release-ceph-squid

dnf install epel-release -y

sudo yum install -y ceph-common

```

- *Copy configuration to client node:* The client must have access to the Ceph configuration file (ceph.conf) and keyring.  (RUN this from  server(any) only)

```yml

scp /etc/ceph/ceph.conf root@client-node:/etc/ceph/

scp /etc/ceph/ceph.client.admin.keyring root@client-node:/etc/ceph/

sudo chmod +r /etc/ceph/ceph.client.admin.keyring

```

- *Check Ceph health on the client:* After setting up, check the health of the Ceph cluster.

```yml

sudo ceph health

sudo ceph health detail

sudo ceph -s

```

- *Create a Ceph storage pool:* This pool can be used for RBD (RADOS Block Device).

```yml
sudo ceph osd pool create rbd 128 3
```

## Step 5: Ceph Client Configuration

- *Create an RBD block device:* Create an RBD image on the client node and disable certain features for kernel compatibility.

```yml

rbd create disk01 --size 4096

rbd ls -l

modprobe rbd
 
rbd feature disable disk01 exclusive-lock object-map fast-diff deep-flatten

rbd map disk01

rbd showmapped

```

- *Format and mount the block device:* Format the device with XFS and mount it.

```yml

mkfs.xfs /dev/rbd0

mkdir -p /mnt/mydisk

mount /dev/rbd0 /mnt/mydisk

df -h

dd if=/dev/zero of=file1.txt bs=1024 count=220040

```


- *Mount CephFS:* Mount the Ceph filesystem on the client node.

```yml

ceph fs ls

ceph orch apply mds cephfs --placement="count:1"

ceph mds stat

ceph auth get-key client.admin

echo -n "AQC7wHBnt+4GFhAAoUkcntGGFVfw2jXdEnWfUQ==" | base64

sudo mkdir /mnt/cephfs

sudo mount -t ceph node1:6789:/ /mnt/cephfs -o name=admin,secret=QVFDTjlIQm4zMmZyREJBQUZwZFVMNHBnUzBrUWlKSWdKTmJ3dFE9PQ==

```


<br>


*This implementation provides a fully functional Ceph storage cluster with CephFS and RBD. The client node can access and use both block devices (RBD) and the file system (CephFS). Ensure that all nodes have proper networking and firewall configurations to allow communication between them.*











<br>
<br>
<br>
<br>



**👨‍💻 𝓒𝓻𝓪𝓯𝓽𝓮𝓭 𝓫𝔂**: [Suraj Kumar Choudhary](https://github.com/Surajkumar4-source) | 📩 **𝓕𝓮𝓮𝓵 𝓯𝓻𝓮𝓮 𝓽𝓸 𝓓𝓜 𝓯𝓸𝓻 𝓪𝓷𝔂 𝓱𝓮𝓵𝓹**: [csuraj982@gmail.com](mailto:csuraj982@gmail.com)





<br>



