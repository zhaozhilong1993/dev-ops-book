## 概观 {#overview}

OpenShift Origin提供了多种安装方法，每种安装方法都可让您快速启动并运行自己的OpenShift Origin实例。根据您的环境，您可以选择最适合您的安装方法。

有关部署完整OpenShift Origin群集的信息，[请参阅高级安装指南](https://docs.openshift.org/3.6/install_config/install/advanced_install.html#install-config-install-advanced-install)。

## 先决条件 {#prerequisites}

在选择安装方法之前，您必须先[满足](https://docs.openshift.org/3.6/install_config/install/prerequisites.html#install-config-install-prerequisites)主机上[的先决条件](https://docs.openshift.org/3.6/install_config/install/prerequisites.html#install-config-install-prerequisites)，其中包括验证系统和环境要求以及安装和配置Docker。确保您的主机设置正确后，您可以继续选择以下安装方法之一。

Docker和OpenShift Origin必须在Linux操作系统上运行。如果您希望从Windows或Mac OS X主机运行服务器，则应首先启动Linux VM。

|  | OpenShift Origin和Docker使用[iptables](https://docs.openshift.org/3.6/admin_guide/iptables.html#admin-guide-iptables)来管理网络。确保本地防火墙规则和其他生成iptable更改的软件不会改变OpenShift Origin和Docker服务设置。 |
| :--- | :--- |


## 安装方法 {#installation-methods}

选择最适合您的以下安装方法之一。

### 方法1：在容器中运行 {#running-in-a-docker-container}

您可以使用Linux系统上[Docker Hub的](https://hub.docker.com/)图像快速获取在容器中运行的OpenShift Origin。仅在Fedora，CentOS和Red Hat Enterprise Linux（RHEL）主机上支持此方法。

|  | OpenShift Origin在端口53,7001和8443上侦听。如果另一个服务正在侦听这些端口，则必须在启动OpenShift Origin容器之前停止该服务。 |
| :--- | :--- |


**安装和启动一体化服务器**

1. 在容器中启动服务器：

   ```
   $ sudo docker run -d --name“origin”\

           --privileged --pid = host --net = host \

           -v /：/ rootfs：ro -v / var / run：/ var / run：rw -v / sys：/ sys -v / sys / fs / cgroup：/ sys / fs / cgroup：rw \

           -v / var / lib / docker：/ var / lib / docker：rw \

           -v /var/lib/origin/openshift.local.volumes:/var/lib/origin/openshift.local.volumes:rslave \ 

           openshift / origin开始
   ```

   |  | `rslave`仅在Docker版本是1.10或更高版本以及Red Hat发行版时才有效。 |
   | :--- | :--- |


   |  | 如果你想使用[OpenShift Origin的聚合记录](https://docs.openshift.org/3.6/install_config/aggregate_logging.html#install-config-aggregate-logging)，你必须添加`-v /var/log:/var/log`到`docker`命令行。该**产地**的容器必须能够访问主机的_**无功/日志/集装箱/**_和_**在/ var / log / messages中**_。 |
   | :--- | :--- |


   这个命令：

   * 在主机上的所有接口（**0.0.0.0:8443**）上启动OpenShift Origin，

   * 启动在`/console`（**0.0.0.0:8443**）处监听所有接口的Web控制台，

   * 启动一个etcd服务器来存储持久数据，并且

   * 启动Kubernetes系统组件。

2. 容器启动后，您可以在容器内打开一个控制台：

   ```
   $ sudo docker exec -it来源bash
   ```

如果删除容器，则任何配置或存储的应用程序定义也将被删除。

**下一步是什么？**

现在您已经在您的环境中成功运行了OpenShift Origin，请通过演示示例应用程序生命周期来[尝试一下](https://docs.openshift.org/3.6/getting_started/administrators.html#try-it-out)。

### 方法2：下载二进制文件 {#downloading-the-binary}

红帽定期将二进制文件发布到GitHub上，您可以在OpenShift Origin存储库的[发布](https://github.com/openshift/origin/releases)页面上下载这些二进制文件。这些是Linux，Windows或Mac OS X 64位二进制文​​件;请注意，Mac和Windows版本仅适用于CLI。

Linux和Mac OS X的发布档案包含服务器二进制文件`openshift`，它是一个全合一的{prodcut-title}安装。所有平台的档案包括[CLI](https://docs.openshift.org/3.6/cli_reference/get_started_cli.html#cli-reference-get-started-cli)（`oc`命令）和Kubernetes客户端（`kubectl`命令）。

**安装和运行一体机服务器**

1. 从[Releases](https://github.com/openshift/origin/releases)页面下载二进制文件并在本地系统上解压缩它。

2. 将解开版本的目录添加到路径中：

   ```
   $ export PATH = $（pwd）：$ PATH
   ```

3. 启动服务器：

   ```
   $ sudo ./openshift开始
   ```

   这个命令：

   * 启动在所有接口（**0.0.0.0:8443**）上侦听的OpenShift Origin，

   * 启动在`/console`（**0.0.0.0:8443**）处监听所有接口的Web控制台，

   * 启动一个etcd服务器来存储持久数据，并且

   * 启动Kubernetes系统组件。

   服务器在前台运行，直到终止进程。

   |  | `root`由于需要修改`iptables`和装入卷，此命令需要访问才能创建服务。 |
   | :--- | :--- |

4. OpenShift Origin服务由TLS保护。在此路径中，我们在启动时生成一个自签名证书，必须由您的Web浏览器或客户端接受。您必须指向`oc`，并`curl`在适当的CA包和客户端密钥和证书连接到OpenShift起源。设置以下环境变量：

       $ export KUBECONFIG =`pwd` / openshift.local.config / master / admin.kubeconfig

       $ export CURL_CA_BUNDLE =`pwd` / openshift.local.config / master / ca.crt

       $ sudo chmod + r`pwd` / openshift.local.config / master / admin.kubeconfig

   |  | 这仅仅是为了举例的目的;在生产环境中，开发人员将生成自己的密钥并且无法访问系统密钥。 |
   | :--- | :--- |

**下一步是什么？**

现在您已经在您的环境中成功运行了OpenShift Origin，请通过演示示例应用程序生命周期来[尝试一下](https://docs.openshift.org/3.6/getting_started/administrators.html#try-it-out)。

## 试试看 {#try-it-out}

启动OpenShift Origin实例后，您可以尝试创建一个展示完整OpenShift Origin概念链的端到端应用程序。

|  | 在虚拟机中运行OpenShift Origin时，您需要确保您的主机系统可以访问容器内的端口8080和8443，以供下面的示例使用。 |
| :--- | :--- |


1. 以常规用户身份登录到服务器：

   ```
   $ oc登录

   用户名：test

   密码：测试
   ```

2. 创建一个新项目来保存您的应用程序：

   ```
   $ oc新项目测试
   ```

3. 根据Docker Hub上的Node.js镜像创建一个新的应用程序：

   ```
   $ oc new-app openshift / deployment-example
   ```

   请注意，服务已创建并提供了一个IP--这是一个可在群集内用于访问应用程序的地址。

4. 显示您创建的资源摘要：

   ```
   $ oc状态
   ```

5. 您的应用程序的容器图像将被拉到本地系统并启动。一旦它开始，它可以在主机上访问。如果这是您的笔记本电脑或台式机，请打开Web浏览器以查看为应用程序显示的服务IP和端口：

   ```
   http://172.30.192.169:8080（举例）
   ```

   如果您位于单独的系统上并且没有对主机的直接网络访问权限，请通过SSH连接到系统并执行`curl`命令：

   ```
   $ curl http://172.30.192.169:8080＃（示例）
   ```

   您应该看到`v1`页面上显示的文字。

现在您的应用程序已部署完毕，您可以通过标记图像来触发该图像的新版本，以便将其展示给主机`v2`。该`new-app`命令创建了一个图像流，用于跟踪您希望使用哪些图像。使用该`tag`命令将新图像标记为需要进行部署：

```
$ oc tag --source = docker openshift / deployment-example：v2 deployment-example：latest
```

您的应用程序的部署配置正在监视，`deployment-example:latest`并且将`latest`标记更新为值时将触发新的滚动部署`v2`。

|  | 您也可以使用该命令的备用版本：$ oc标签docker.io/openshift/deployment-example:v2 deployment-example：latest |
| :--- | :--- |


返回到浏览器或`curl`再次使用，您应该看到`v2`页面上显示的文字。

|  | 对于下一步，我们需要确保Docker能够从主机系统中提取图像。确保您已完成关于`--insecure-registry`从[主机准备中](https://docs.openshift.org/3.6/install_config/install/host_preparation.html#install-config-install-host-preparation)设置标志的说明。 |
| :--- | :--- |


作为开发人员，构建新的容器映像与部署它们同样重要。OpenShift Origin提供了用于运行构建的工具，以及通过Source-to-Image工具链从预定义的构建器映像中构建源代码的工具。

对于此过程，请确保Docker能够从主机系统中提取图像。另外，请确保您已完成有关`--insecure-registry`从[主机准备中](https://docs.openshift.org/3.6/install_config/install/host_preparation.html#install-config-install-host-preparation)设置标志的说明。

1. 切换到管理用户并切换到`default`项目：

   ```
   $ oc登录-u系统：admin

   $ oc项目默认值
   ```

2. 为OpenShift Origin集群设置一个集成的Docker注册表：

   ```
   $ oc adm注册表
   ```

   注册表映像下载并开始使用需要几分钟的时间`oc status`才能知道注册表何时启动。

3. 切换回`test`用户和`test`项目：

   ```
   $ oc登录-u测试

   $ oc项目测试
   ```

4. 创建一个新的应用程序，它将Node.js的构建器图像与示例源代码结合起来，以创建一个新的可部署Node.js图像：

   ```
   $ oc new-app openshift / nodejs-010-centos7〜https：//github.com/openshift
   odejs-ex.git
   ```

   构建将使用提供的镜像和`master`提供的Git存储库分支的最新提交自动触发。要获得构建的状态，请运行：

   ```
   $ oc状态
   ```

   这将总结构建。构建完成后，所得容器映像将被推送到Docker注册表。

5. 等待部署的映像启动，然后使用浏览器查看服务IP`curl`。

您可以通过以下方式了解有关[CLI中](https://docs.openshift.org/3.6/cli_reference/basic_cli_operations.html#cli-reference-basic-cli-operations)可用命令的更多信息（`oc`命令）：

```
$ oc帮助
```

或通过以下方式连接到另一个系

```
$ oc -h 
<
server_hostname_or_IP
>
 [...]
```

OpenShift Origin包含一个Web控制台，可帮助您可视化应用程序并执行常见的创建和管理操作。您可以使用`test`我们上面创建的用户通过登录到控制台[`https://<server>:8443/console`](https://%3Cserver%3E:8443/console)。有关更多信息，请参阅[开发人员入门：Web控制台](https://docs.openshift.org/3.6/getting_started/developers_console.html#getting-started-developers-console)。

您还可以参阅[OpenShift Origin 3应用程序生命周期示例](https://github.com/openshift/origin/blob/master/examples/sample-app)进行更深入的演练。

