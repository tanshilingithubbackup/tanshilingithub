## Edge computing scenario classification and challenges

The "edge" to be discussed here refers specifically to computing resources that are geographically distributed closer to the equipment and far away from the resource nodes of the cloud data center. Typical edge computing is divided into Internet of Things (for example: next-generation industrial automation, smart cities, smart homes, large supermarkets, etc.) and non-Internet of things (for example: games, CDN, etc.) scenarios.

In the real world, edge computing cannot exist alone. It must be connected to remote data centers/clouds (see below for specific reasons). Take IoT (Internet of Things) as an example. In addition to having sensors to collect data from the surrounding environment, edge devices also receive control commands from the cloud. There are generally two modes of connection between edge devices and cloud: direct connection and connection through relay edge nodes, as shown in the following figure: [VIEW IMAGE](https://static001.geekbang.org/resource/image/80/3c/804f44ee5a6745909adc6d679026833c.png)

Whether edge devices can directly connect to the cloud/data center depends on whether there is the only routable IP in the world, such as mobile phones, tablets, etc. However, most devices communicate with edge nodes through near-field communication protocols, and then connect to the cloud/data center. The edge node is the convergence point of the equipment, has an IP address assigned, and plays the role of an edge gateway. Edge nodes provide IP addresses for ordinary devices and can also run containers.

We noticed that there are also different levels of edge nodes, and one of the criteria for dividing is the size of resources (computing, storage, network bandwidth, etc.). [VIEW IMAGE](https://static001.geekbang.org/resource/image/7b/04/7b9fe188ee8db80614d36c79ddcb9304.png)
shown above, we divide the edge into "Device Edge" and "Infrastructure Edge". Device Edge refers to those edge nodes that are not considered part of the IT infrastructure, such as trains, oil fields, factories, and homes. Usually they don't have much computing power and are used to solve the problem of "one mile" access. A typical example is a smart router. One end uses infrared signals to control home appliances, and the other end is connected to a cloud data center through a network. Due to the poor stability of the device edge network, it is necessary to consider the situation where they will be offline for a long time (such as a train passing a tunnel).

> Note: Many devices in daily life can be regarded as a combination of device+device edge, such as smart phones.

Infrastructure Edge (infrastructure edge) has stronger computing and storage capabilities than Device Edge, and is usually connected to the data center through a backbone network, so it has a larger network bandwidth and a more reliable network connection, such as: CDN, game server, etc. . In addition to running containers at the edge of the infrastructure, some even have enough resources to run full Kubernetes.

The significance of classifying edge nodes is that targeted deployment models are required for edges at different levels, and the platform must provide communication capabilities for edge nodes.

Finally, we summarize several challenges currently facing the field of edge computing:

- Cloud-side collaboration: Intelligent collaboration and flexible migration of AI/security and other services between the cloud and the edge;
- Network: Reliability and bandwidth limitation of edge network;
- Management: Edge node resource management and edge application life cycle management;
- Expansion: highly distributed and large-scale scalability;
- Heterogeneous: Heterogeneous hardware and communication protocols at the edge.

Among them, the Cloud Edge Association is the biggest technical challenge currently facing!

## Why do we need cloud-side collaboration

Edge computing and cloud computing are not two mutually exclusive technologies, they are in a complementary relationship. And from the perspective of scenario requirements, IoT/Edge has some similarities with cloud data centers, such as:

- The edge also has requirements for the computing, storage, and network resources of the management node;
- Edge applications also want containerization and microservices;
- Edge computing hopes to have standard APIs and tool chains;
- Security, data/channel encryption and authentication and authorization.

Therefore, extending the existing architecture and capabilities of the cloud data center to the edge becomes a matter of course. Here are some scenarios that require cloud-side collaboration:

- AI cloud training, edge execution. That is to give full play to the advantages of cloud computing's massive resources, put the training of AI models in the cloud, and the execution of AI is close to the device side;
- Microservices, DevOps. Microservices and DevOps are not just patents developed on the cloud. Bringing them to the edge will significantly accelerate the iterative cycle of IoT software such as embedded devices and robots, and improve the efficiency of deployment and operation and maintenance;
- Data backup dump. For example, massive amounts of industrial data (encrypted) are stored in the cloud;
- Remote control. The cloud remotely issues control signals to edge devices;
- Automatic expansion. Because edge nodes are not as good as the cloud with good automatic expansion capabilities, you can choose to elastically expand the edge load to the cloud at peak times.

We have accumulated enough experience in managing resources on the cloud. Now the next challenge is how to build an edge cloud platform and extend the management method of resources on the cloud to the edge, so that we can seamlessly manage resources and equipment on the edge. . The edge cloud platform will focus on solving the following problems:

- Access to large-scale/heterogeneous equipment, gateways and edge nodes;
- A large amount of telemetry data is aggregated and processed and provided to cloud applications for use;
- Equipment security and identification services;
- Support remotely issuing instructions to equipment;
- Automatically create and manage edge nodes and devices;
- Realize the orchestration, deployment and configuration of edge applications in the cloud;
- Provide data storage, event management, API management and data analysis capabilities for edge application development.

## The advantages and challenges of Kubernetes

Kubernetes has become a cloud-native standard and can provide a consistent cloud experience on any infrastructure. We can often see the combination of "container + Kubernetes" exerting 10X efficiency in DevOps. Recently, there has been an increasing demand for Kubernetes to run outside the data center (edge).

If you want to deploy more complex applications at the edge, then Kubernetes is an ideal choice:

- The lightweight and portability of containers are very suitable for edge computing scenarios;
- Kubernetes has been proven to have good scalability;
- Able to provide a consistent experience across the underlying infrastructure;
- Supports both cluster and stand-alone operation and maintenance modes;
- Workload abstraction, such as: Deployment and Job, etc.;
- Rolling upgrade and rollback of applications;
- A strong cloud-native technology ecosystem has been formed around Kubernetes, such as: monitoring, logging, CI, storage, and networking can find ready-made tool chains;
- Support heterogeneous hardware configuration (storage, CPU, GPU, etc.);
- Users can use the familiar kubectl or helm chart to push IoT applications from the cloud to the edge;
- Edge nodes can be directly mapped to Kubernetes Node resources, and Kubernetes' extended API (CRD) can realize the abstraction of edge devices.

However, Kubernetes is designed for cloud data centers after all. To use Kubernetes at the edge, Kubernetes or its extensions need to solve the following problems:

- ARM's low power consumption and multi-core characteristics make it widely used in the IoT/Edge field. However, most Kubernetes distributions do not support the ARM architecture;
- Many devices have limited resource specifications at the edge, especially CPU processing power is weak, so it is impossible to deploy a complete Kubernetes;
- Kubernetes relies heavily on the list/watch mechanism and does not support offline operation, and the offline of edge nodes is normal, for example: device sleep restart;
- The operation and maintenance of Kubernetes is still too complicated compared to the subset of functions used in edge scenarios;
- Special network protocol and topology requirements. Device access protocols are often non-TCP/IP protocols, such as Modbus and OPC UA for industrial Internet of Things, Bluetooth and ZigBee for consumer Internet of Things, etc.;
- Multi-rental equipment.

Regarding how to use Kubernetes at the edge, a survey conducted by the Kubernetes IoT/Edge WG organization shows that 30% of users want to deploy a complete Kubernetes cluster at the edge, while 70% of users want to deploy the management plane of Kubernetes in the cloud and only on the edge nodes. Deploy the Kubernetes agent.

Extending Kubernetes from the cloud to the edge, there are two open source projects that have done a good job, namely KubeEdge and K3S. Below we will explain these two projects in detail and make a comprehensive comparison.

## KubeEdge architecture analysis

KubeEdge is the first open intelligent edge platform based on Kubernetes that provides cloud-side collaboration capabilities. It is also CNCF's first official project in the field of intelligent edge. The key problems KubeEdge should solve are:

- Cloud Edge Collaboration
- Resource heterogeneity
- Large scale
- Lightweight
- Consistent device management and access experience

The architecture of KubeEdge is as follows: [VIEW IMAGE](https://static001.geekbang.org/resource/image/4f/98/4f53872e5269818ab582bd496d652b98.png)


KubeEdge architecture is clearly divided into three layers, namely: cloud, edge and device layer. This is a complete open source edge cloud platform from cloud to edge to device, eliminating user vendors Binding concerns.



The edge process of KubeEdge includes the following 5 components:

- edged is a re-developed lightweight Kubelet, which realizes the life cycle management of Kubernetes resource objects such as Pod, Volume, Node, etc.;
- Metamanager is responsible for the persistence of local metadata and is the key to the autonomy of edge nodes;
- edgehub is a multiplexed message channel, providing reliable and efficient cloud edge information synchronization;
- devicetwin is used to abstract physical devices and generate a device state mapping in the cloud;
- Eventbus subscribes to device data from MQTT Broker.
- The cloud process of KubeEdge includes the following 2 components:
- cloudhub is deployed in the cloud and receives the information synchronized by edgehub to the cloud;
- The edgecontroller is deployed in the cloud and is used to control the synchronization of the Kubernetes API Server with the edge nodes, applications, and configurations.

Kubernetes maser runs in the cloud, and users can directly manage edge nodes, devices and applications in the cloud through the kubectl command line. The usage habits are exactly the same as those native to Kubernetes, and there is no need to re-adapt.

## K3S architecture analysis

K3S is a Kubernetes release officially certified by CNCF, and its open source time is a little later than KubeEdge. K3S is designed for R&D and operation and maintenance personnel who run Kubernetes in a resource-limited environment. The purpose is to run small Kubernetes clusters on edge nodes of x86, ARM64 and ARMv7D architecture. The overall architecture of K3S is as follows: [VIEW IMAGE](https://static001.geekbang.org/resource/image/8b/8a/8bed2a683a91a67ec3040df149d6968a.png)[VIEW IMAGE](https://static001.geekbang.org/resource/image/22/10/2207fabad8c2687077c507160e989310.png)
In fact, K3S is based on a specific version of Kubernetes (for example: 1.13) directly made code changes. K3S is divided into Server and Agent. Server is Kubernetes management plane component + SQLite and Tunnel Proxy, and Agent is Kubernetes data plane + Tunnel Proxy.

In order to reduce the resources required to run Kubernetes, K3S made the following modifications to the native Kubernetes code:

- Delete old, non-essential code. K3S does not include any non-default, Alpha or outdated Kubernetes features. In addition, K3S also deleted all non-default admission controllers, in-tree cloud providers and storage plug-ins;
- Integrate the packaging process. In order to save memory, K3S merges multiple processes of the Kubernetes management plane and data plane that originally run in a multi-process mode into one to run;
- Use containderd to replace Docker, which significantly reduces the space occupied during runtime;
- Introduce SQLite instead of etcd as the management plane data storage, and use SQLite to implement the list/watch interface, namely Tunnel Proxy;
- Added a simple installation procedure.

All components of K3S (including Server and Agent) run on the edge, so no cloud-side collaboration is involved. If K3S is to fall into production, there should be a cluster management solution on top of K3S that is responsible for cross-cluster application management, monitoring, alarms, logs, security, and policies. Unfortunately, Rancher has not yet open sourced this part of its capabilities.

## KubeEdge and K3S all-round comparison

### Deployment model

KubeEdge follows the following deployment model: [VIEW IMAGE](https://static001.geekbang.org/resource/image/8b/8a/8bed2a683a91a67ec3040df149d6968a.png)

KubeEdge is a completely decentralized deployment model. The management plane is deployed in the cloud. Edge nodes can run Kubernetes agents without too many resources. The cloud edge collaborates through messages. From the perspective of Kubernetes, the edge node + cloud is a complete Kubernetes cluster. This deployment model can meet the deployment requirements of both "device edge" and "infrastructure edge" scenarios.

The deployment model of K3S is as follows: [VIEW IMAGE](https://static001.geekbang.org/resource/image/22/86/22f9c3750a065d7bb2ac4f8f637a1186.png)[VIEW IMAGE](https://static001.geekbang.org/resource/image/8b/8a/8bed2a683a91a67ec3040df149d6968a.png)
K3S will run a complete Kubernetes cluster at the edge, which means that K3S is not a decentralized deployment model. Each edge requires additional deployment of Kubernetes management planes. The problems with this deployment model are:

- Installing the Kubernetes management plane at the edge will consume more resources, so this deployment model is only suitable for the "infrastructure edge" scenario with sufficient resources, and is not suitable for the "device edge" scenario with less resources;
- The network between clusters needs to be opened up;
- In order to manage the edge Kubernetes cluster, a layer of multi-cluster management components must be superimposed on it (unfortunately, this component is not open source).

### Cloud Edge Collaboration

Cloud edge collaboration is a highlight of KubeEdge. KubeEdge manages the addition, deletion, modification, and inspection of edge nodes, devices, and workloads in the cloud through the Kubernetes standard API. System upgrades and application updates of edge nodes can be directly issued from the cloud to improve the efficiency of edge operation and maintenance. In addition, the multiplex message channel optimized at the bottom of KubeEdge has better scalability than Kubernetes' list/watch mechanism based on HTTP long connections, allowing the access of a large number of edge nodes and devices. KubeEdge cloud components are completely open source. Users can deploy KubeEdge on any public cloud/private cloud without worrying about vendor lock-in, and freely integrate other services of the public cloud. K3S does not provide cloud-side collaboration capabilities.

### Edge node offline autonomy

Different from the nodes of a Kubernetes cluster, edge nodes need to work autonomously in a completely disconnected mode, and do not periodically synchronize their states, and only communicate with the control plane when they are reconnected. This mode is very different from the original design of Kubernetes management plane and worker nodes to keep state updated through heartbeat and list/watch.

KubeEdge realizes the offline autonomy of nodes through the message bus and local metadata storage. The user's desired control plane configuration and real-time device status updates are synchronized to local storage through messages, so that the node will not lose management metadata even if it is restarted when offline, and maintain the ability to manage the device and application of the node.

K3S also does not involve this ability.

### Equipment management

KubeEdge provides a pluggable unified management framework for devices, allowing users to develop device access drivers based on different protocols or actual needs on this framework. Currently supported and planned to support protocols are: MQTT, BlueTooth, OPC UA, Modbus, etc. As more and more community partners join, KubeEdge will support more device communication protocols in the future. KubeEdge implements device status update and synchronization through device twins/digital twins, and provides Kubernetes extended API abstract device objects in the cloud. Users can use kubectl to manage edge devices in the cloud by operating Kubernetes resource objects.

K3S does not involve this ability.

### Lightweight

In order to deploy Kubernetes at the edge, both KubeEdge and K3S have undergone lightweight transformations. The difference is that the direction of K3S is based on the continuous subtraction of the community version of Kubernetes (including the management plane and the control plane), while KubeEdge retains the Kubernetes management plane and redevelops the node agent.

It should be noted that in the process of tailoring Kubernetes, K3S led to the lack of some management capabilities, such as some admission controllers. On the other hand, KubeEdge retains the Kubernetes management surface intact, without modifying a single line of code.

Below we will compare the resource consumption of KubeEdge and K3S from the three dimensions of binary size, memory and CPU. Since the management plane of KubeEdge is deployed in the cloud, users do not care much about the consumption of cloud resources, and K3S server and agent run on the edge, so the following will compare the CPU and CPU and the three different processes of KubeEdge agent, K3S agent and K3S server. Resource consumption of memory.

The specifications of the test machine are 4 vCPU and 8GB RAM.

#### Memory consumption comparison

Deploy 0~100 applications with KubeEdge and K3S respectively, and observe the memory consumption of the two respectively. The comparison is as follows: [VIEW IMAGE](https://static001.geekbang.org/resource/image/ce/1e/ce9c5a8e5da7e1236ad6dc3e2598bf1e.jpg)
As can be seen from the above figure, the memory consumption: KubeEdge agent/u0026lt; K3S agent/u0026lt; K3S Server. What's interesting is that K3S agent consumes 100+MB of memory even if the application is not running, while the memory consumption of K3S server is about 300MB in the case of empty running.

#### CPU usage comparison

Deploy 0-100 applications with KubeEdge and K3S respectively, observe the CPU usage of the two, and compare them as follows:

[VIEW IMAGE](https://static001.geekbang.org/resource/image/a7/57/a72b2f21eb33b041f4d8b983c6dda257.jpg)As can be seen from the above figure, KubeEdge agent CPU consumes less than K3S agent and server.

#### Binary size

[VIEW IMAGE](https://static001.geekbang.org/resource/image/f6/f5/f6c2bb7fd2e20da1c46714e3d6dae4f5.jpg)KubeEdge agent binary size is 62MB, K3S binary size is 36MB.

### Large scale

The native scalability of Kubernetes is limited by the long connection consumption of list/watch, and the scale of nodes that can be stably supported in the production environment is about 1,000. As the core of Huawei Cloud Intelligent Edge Service IEF, KubeEdge optimizes the performance of cloud-side communication through multiplexed message channels. Stress testing found that it can easily support 5000+ nodes. The cluster management technology of K3S has not been open source, because it is impossible to know the ability of K3S to manage large-scale clusters.

## Summary

The most impressive innovation of K3S lies in its attempts to miniaturize Kubernetes. By tailoring some of the infrequent functions of Kubernetes and merging multiple components into one process, it enables Kubernetes to run on edge nodes with sufficient resources. , So that users in the edge scenario can also get a consistent Kubernetes experience. However, through the performance comparison data above, it is found that the resource consumption of K3S is still several times higher than that of KubeEdge, and the memory of several hundred MB is not what most device edge nodes can provide. The most important thing is that Kubernetes was originally designed for cloud data centers. Many special problems in edge computing scenarios cannot be solved by native Kubernetes. K3S directly modifies the Kubernetes code or even the basic implementation mechanism (for example, replacing etcd with SQLite). The approach is still debatable. About what can be changed, what can't be changed and how? Each user has his own point of view according to his actual needs, and it is difficult to reach agreement. In addition, whether K3S's intrusive modification can continue to follow the development of Kubernetes upstream is still unknown.

KubeEdge and K3S are taking another path. KubeEdge is a complete edge cloud platform from cloud to edge to device. Its coupling with Kubernetes is only 100% compatible with Kubernetes native API. KubeEdge provides cloud-side collaboration capabilities that K3S does not have, develops a lighter edge container management agent, solves the offline autonomy of native Kubernetes in edge scenarios, and supports the access of massive heterogeneous edge devices. KubeEdge recently donated to CNCF and became the first official project in the field of CNCF edge computing. It is to work with community partners to formulate cloud and edge computing collaboration standards, end the chaotic state of edge computing without unified standards and reference architectures, and jointly promote the edge Computing industry development.

Finally, there are broad development prospects for edge computing. KubeEdge and K3S are both very young and excellent open source projects. I believe that in the future, they will make progress together in the process of learning from each other to better address the needs of edge computing users.

[VIEW IMAGE](https://profile.csdnimg.cn/2/F/1/3_cpongo3)