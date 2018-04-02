## 初始计划 {#inital-planning}

对于生产环境，有几个因素会影响安装。阅读文档时请考虑以下问题：

* _你想使用哪种安装方法？_该[安装方法](https://docs.openshift.org/3.6/install_config/install/planning.html#installation-methods)部分提供了快速和高级安装方法的一些信息。

* _集群中需要多少个主机？_“[环境场景”](https://docs.openshift.org/3.6/install_config/install/planning.html#environment-scenarios)部分提供了多个单主控和多主控配置的示例。

* _群集中需要多少个群集？_“[大小调整注意事项”](https://docs.openshift.org/3.6/install_config/install/planning.html#sizing)部分为节点和窗格提供了限制，以便您可以计算您的环境需要多大。

* _是否需要_[_高可用性_](https://docs.openshift.org/3.6/admin_guide/high_availability.html#admin-guide-high-availability)_？_建议高可用性用于容错。在这种情况下，您可能会尝试使用[多个母版使用本地HA](https://docs.openshift.org/3.6/install_config/install/planning.html#multi-masters-using-native-ha)示例作为您环境的基础。

* _你想使用哪种安装类型：_[_RPM还是集装箱式的_](https://docs.openshift.org/3.6/install_config/install/planning.html#rpm-vs-containerized)_？_这两种安装都提供了一个可用的OpenShift Origin环境，但您可能会偏好某种特定的安装，管理和更新服务的方法。

## 安装方法 {#installation-methods}

开发和生产环境都支持快速和高级安装方法。如果您想快速使OpenShift Origin运行并首次尝试，请使用快速安装程序，并让交互式CLI指导您完成与环境相关的配置选项。

为了最大程度地控制群集的配置，可以使用高级安装方法。如果您已经熟悉Ansible，此方法特别适用。但是，遵循OpenShift Origin文档应该为您提供足够的信息，以便可靠地部署集群并直接使用提供的Ansible操作手册继续管理其配置后的配置。

如果最初使用快速安装程序进行安装，则始终可以进一步调整群集的配置，并使用相同的安装程序工具调整群集中的主机数量。如果您想稍后切换到使用高级方法，则可以为您的配置创建库存文件并继续进行。

## 调整大小的注意事项 {#sizing}

确定您的OpenShift Origin群集需要多少个节点和pod。群集可伸缩性与群集环境中的群集数量相关。该数字会影响您设置中的其他数字。

下表提供了节点和窗格的最大尺寸限制：

| 类型 | 最大 |
| :--- | :--- |
| 每个群集的最大节点 | 2000 |
| 每个群集的最大群集数 | 120000 |
| 每个节点的最大数量 | 250 |
| 每个核心的最大豆荚数 | 10 |

|  | 过度订阅节点上的物理资源会影响资源，以保证Kubernetes调度程序在放置过程中进行。了解您可以采取哪些措施来[避免内存交换](https://docs.openshift.org/3.6/admin_guide/overcommit.html#disabling-swap-memory)。 |
| :--- | :--- |


确定每个节点预计有多少个豆荚适合：

```
每个群集的最大机架数/每个节点的预期机架数=总节点数
```

示例场景

如果要为每个群集的2200个群集确定群集的范围，则假定每个节点有250个最大群集，则至少需要9个节点：

```
2200/250 = 8.8
```

如果将节点数量增加到20个，则pod分配将更改为每个节点110个pod：

```
2200/20 = 110
```

## 环境方案 {#environment-scenarios}

本节概述了OpenShift Origin环境的各种场景示例。根据您的[尺寸](https://docs.openshift.org/3.6/install_config/install/planning.html#sizing)需求，将这些场景用作规划您自己的OpenShift Origin群集的基础。

|  | 不支持在安装之后从单个主集群迁移到多个主集群。 |
| :--- | :--- |


### 单一主站和一个系统上的节点 {#single-master-single-box}

OpenShift Origin只能安装在一个[开发](https://docs.openshift.org/3.6/dev_guide/application_lifecycle/promoting_applications.html#dev-guide-promoting-application-de)环境的单个系统上。的_所有功能于一身的环境_不被认为是生产环境。

### 单主控和多个节点 {#single-master-multi-node}

下表描述了单个[主](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#master)[节点](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#node)（带有嵌入式**etcd**）和两个[节点](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#node)的示例环境：

| 主机名 | 要安装的基础组件 |
| :--- | :--- |
| **master.example.com** | 主和节点 |
| **node1.example.com** | 节点 |
|  | **node2.example.com** |

### 单主，多重等等，和多个节点 {#single-master-multi-etcd-multi-node}

下表介绍了单个[主](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#master)[节点](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#node)，三个[**etcd**](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#master)主机和两个[节点](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#node)的示例环境：

| 主机名 | 要安装的基础组件 |
| :--- | :--- |
| **master.example.com** | 主和节点 |
| **etcd1.example.com** | **ETCD** |
|  | **etcd2.example.com** |
|  | **etcd3.example.com** |
| **node1.example.com** | 节点 |
|  | **node2.example.com** |

|  | 指定多个**etcd**主机时，会安装并配置外部**etcd**。不支持OpenShift Origin的嵌入式**etcd的**群集。 |
| :--- | :--- |


### 多个大师使用本机HA {#multi-masters-using-native-ha}

以下描述了三个[主服务器](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#master)，一个HAProxy负载平衡器，三个[**etcd**](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#master)主机和两个使用HA方法的[节点](https://docs.openshift.org/3.6/architecture/infrastructure_components/kubernetes_infrastructure.html#node)的示例环境`native`：

| 主机名 | 要安装的基础组件 |
| :--- | :--- |
| **master1.example.com** | Master（使用本地HA群集）和节点 |
|  | **master2.example.com** |
|  | **master3.example.com** |
| **lb.example.com** | HAProxy负载平衡API主终端 |
| **etcd1.example.com** | **ETCD** |
|  | **etcd2.example.com** |
|  | **etcd3.example.com** |
| **node1.example.com** | 节点 |
|  | **node2.example.com** |

|  | 指定多个**etcd**主机时，会安装并配置外部**etcd**。不支持OpenShift Origin的嵌入式**etcd的**群集。 |
| :--- | :--- |


### 独立注册表 {#planning-stand-alone-registry}

您还可以使用OpenShift Origin的集成注册表安装OpenShift Origin以充当独立注册表。有关此方案的详细信息，请参阅[安装独立注册表](https://docs.openshift.org/3.6/install_config/install/stand_alone_registry.html#install-config-installing-stand-alone-registry)。

## RPM vs Containerized {#rpm-vs-containerized}

RPM安装通过软件包管理安装所有服务，并将服务配置为在同一用户空间内运行，而集装箱式安装使用容器映像安装服务并在单个容器中运行单独的服务。

有关配置安装以使用集装箱化服务的更多详细信息，请参阅“[在Containerized主机上安装”](https://docs.openshift.org/3.6/install_config/install/rpm_vs_containerized.html#install-config-install-rpm-vs-containerized)主题。

