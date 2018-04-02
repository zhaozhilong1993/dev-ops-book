## 概观 {#overview}

这种入门体验将引导您通过最简单的方式在OpenShift Origin上启动并运行示例项目。在项目中启动图像有几种不同的方式，但是这个主题关注的是最快和最简单的方法。

如果这是您已阅读的文档的第一部分，并且您不熟悉OpenShift Origin版本3（v3）的核心概念，那么您可能需要先阅读[新增内容](https://docs.openshift.org/3.6/whats_new/index.html#whats-new-index)。此版本的OpenShift Origin与版本2（v2）有很大不同。

OpenShift Origin 3为开发人员提供了开箱即用的[语言](https://docs.openshift.org/3.6/using_images/s2i_images/index.html#using-images-s2i-images-index)和[数据库](https://docs.openshift.org/3.6/using_images/db_images/index.html#using-images-db-images-index)集合，并提供相应的实现和教程，使您能够启动应用程序开发。[快速启动模板的](https://docs.openshift.org/3.6/dev_guide/dev_tutorials/quickstarts.html#dev-guide-app-tutorials-quickstarts)语言支持中心，这反过来又利用了[构建器映像](https://docs.openshift.org/3.6/using_images/s2i_images/index.html#using-images-s2i-images-index)。

| 语言 | 实现和教程 |
| :--- | :--- |
| 红宝石 | [轨道](https://github.com/openshift/rails-ex) |
| 蟒蛇 | [Django的](https://github.com/openshift/django-ex) |
| Node.js的 | [Node.js的](https://github.com/openshift/nodejs-ex) |
| PHP | [CakePHP的](https://github.com/openshift/cakephp-ex) |
| Perl的 | [舞蹈家](https://github.com/openshift/dancer-ex) |
| Java的 | [Maven的](https://docs.openshift.org/3.6/using_images/s2i_images/java.html#using-images-s2i-images-java) |

OpenShift Origin提供的其他图像包括：

* [MySQL的](https://github.com/openshift/mysql)

* [MongoDB的](https://github.com/openshift/mongodb)

* [PostgreSQL的](https://github.com/openshift/postgresql)

* [詹金斯](https://github.com/openshift/jenkins)

为了帮助说明如何构建这样的应用程序，以下各节将指导您创建一个包含将提供欢迎页面和当前命中计数（存储在数据库中）的示例Node.js应用程序的项目。

|  | 本主题讨论[快速入门](https://docs.openshift.org/3.6/dev_guide/dev_tutorials/quickstarts.html#dev-guide-app-tutorials-quickstarts)和[即时应用程序](https://docs.openshift.org/3.6/dev_guide/templates.html#using-the-instantapp-templates)模板和应用程序。快速入门为应用程序开发提供了一个起点，但它们依赖于您的开发来创建一个有用的应用程序。相比之下，詹金斯等即时应用程序即时可用。 |
| :--- | :--- |


## 在你开始之前 {#developers-cli-before-you-begin}

在你开始之前：

* 您必须能够访问正在运行的OpenShift Origin实例。如果您无权访问，请联系您的群集管理员。

* 您的实例必须由集群管理员使用[Instant App模板](https://docs.openshift.org/3.6/dev_guide/templates.html#using-the-instantapp-templates)和生成[器映像](https://docs.openshift.org/3.6/using_images/s2i_images/index.html#using-images-s2i-images-index)进行预配置。如果它们不可用，请指示您的群集管理员[加载默认图像流和模板](https://docs.openshift.org/3.6/install_config/imagestreams_templates.html#install-config-imagestreams-templates)主题。

* 您必须[下载并安装](https://docs.openshift.org/3.6/cli_reference/get_started_cli.html#cli-reference-get-started-cli)OpenShift Origin CLI。

## 分叉样本库 {#developers-cli-forking-the-sample-repository}

1. 在您登录到GitHub时访问[Ruby示例](https://github.com/openshift/ruby-ex)页面。

   |  | 本主题遵循Ruby示例，但您可以继续使用[OpenShift Origin GitHub项目中提供的](https://docs.openshift.org/3.6/getting_started/developers_cli.html#getting-started-developers-cli-languages)任何语言示例。 |
   | :--- | :--- |

2. [分叉存储库](https://help.github.com/articles/fork-a-repo/)。

   你被重定向到你的新叉子。

3. 复制叉的克隆URL。

4. 将存储库克隆到本地计算机。

## 创建一个项目 {#developers-cli-creating-a-project}

要创建应用程序，您必须创建一个新项目并指定源的位置。从那里开始，OpenShift Origin开始构建过程并创建一个新的部署。

1. 从CLI登录到OpenShift Origin：

   * 使用用户名和密码：

     ```
     $ oc login -u = 
     <
     username
     >
      -p = 
     <
     password
     >
      --server = 
     <
     your-openshift-server
     >
      --insecure-skip-tls-verify
     ```

   * 使用oauth标记：

     ```
     $ oc登录
     <
     https://api.your-openshift-server.com
     >
      --token = 
     <
     tokenID
     >
     ```

2. 要创建一个新项目：

   ```
   $ oc new-project 
   <
   projectname
   >
    --description =“
   <
   description
   >
   ”--display-name =“
   <
   display_name
   >
   ”
   ```

创建新项目后，您将自动切换到新的项目命名空间。

## 创建应用程序 {#developers-cli-creating-an-application}

要从分叉存储库中的代码创建新应用程序：

1. 通过指定代码的来源来创建应用程序：

   ```
   $ oc new-app openshift / ruby​​-20-centos7〜https：//github.com/ 
   <
   your_github_username
   >
    / ruby​​-ex
   ```

   OpenShift Origin找到匹配的构建器映像（在本例中为**ruby-20-centos7**），然后为应用程序（映像流，构建配置，部署配置和服务）创建资源。它还安排构建。

2. 跟踪构建的进度：

   ```
   $ oc logs -f bc / ruby​​-ex
   ```

3. 构建完成并将生成的图像成功推送到注册表后，请检查应用程序的状态：

   ```
   $ oc状态
   ```

   或者您可以从Web控制台查看构建。

创建应用程序可能需要一些时间。您可以在Web控制台的Overview页面上查看正在创建的新资源，并观察构建和部署的进度。您还可以使用该`oc get pods`命令来检查Pod何时启动并运行，或者`oc get builds`查看构建统计信息的命令。

在创建Ruby pod时，其状态显示为挂起。Ruby pod然后启动并显示其新分配的IP地址。当Ruby pod运行时，构建完成。

该`oc status`命令会告诉您该服务正在运行的IP地址;它部署的默认端口是8080。

## 创建一个路线 {#developers-cli-create-route}

OpenShift Origin路由以主机名称公开服务，以便外部客户端可以通过名称访问它。要创建到新应用程序的路线：

1. 公开以下服务`ruby-ex`：

   ```
   $ oc公开服务ruby-ex
   ```

2. 查看你的新路线：

   ```
   $ oc获得路线
   ```

3. 复制路线位置，这将是类似的`ruby-ex-my-test.example.openshiftapps.com`。

## 验证应用程序正在运行 {#developers-cli-view-running-app}

要查看您的新应用程序，请将您复制的路线位置（在前一节中）粘贴到Web浏览器的地址栏中，然后按回车。

示例`ruby-ex`应用程序是一个简单的欢迎屏幕，其中包含有关如何部署代码更改，管理应用程序和其他开发资源的详细信息。

接下来，通过GitHub webhook触发器配置自动构建，以便更改分叉的存储库中的代码会导致应用程序重建。

## 配置自动构建 {#configuring-automated-builds}

您从[OpenShift Origin GitHub存储库中](https://github.com/openshift/ruby-ex)分离了此应用程序的源代码。因此，只要将代码更改推送到分叉存储库，就可以使用webhook自动触发重新构建应用程序。

为您的应用程序设置webhook：

1. 查看触发器部分**`BuildConfig`**以验证是否存在GitHub webhook触发器：

   ```
   $ oc编辑bc / ruby​​-ex
   ```

   你应该看到类似这样的东西：

   ```
   触发器

   -  github：

       秘密：Q1tGY0i9f1ZFihQbX07S

       键入：GitHub
   ```

   秘密确保只有您和您的存储库可以触发构建。

2. 运行以下命令以显示与以下内容相关联的webhook URL**`BuildConfig`**：

   ```
   $ oc描述bc ruby​​-ex
   ```

3. 复制上述命令输出的GitHub webhook负载URL。

4. 在GitHub上导航到分叉的存储库，然后单击**设置**。

5. 点击**Webhooks＆Services**。

6. 点击**添加webhook**。

7. 将您的webhook网址粘贴到**Payload URL**字段中。

8. 点击**添加webhook**进行保存。

GitHub现在尝试向您的OpenShift Origin服务器发送ping有效负载，以确保通信成功。如果您看到您的webhook网址旁边显示绿色复选标记，则表明它已正确配置。将鼠标悬停在复选标记上可查看最后一次投递的状态。

当您下一次将代码更改推送到分叉存储库时，应用程序将自动重建。

## 编写代码更改 {#developers-cli-writing-a-code-change}

要在本地工作，然后将更改推送到您的应用程序：

1. 在本地机器上，使用文本编辑器来更改文件_**ruby-ex / config.ru**_的示例应用程序的源代码

2. 进行代码更改，该代码将在您的应用程序中可见。例如：在第229行上，将标题从更改`Welcome to your Ruby application on OpenShift`为`This is my Awesome OpenShift Application`，然后保存更改。

3. 在git中提交更改，并将更改推送到您的分支。

   如果您的webhook配置正确，您的应用程序将立即根据您的更改重建自己。重建成功后，使用之前创建的路径查看更新的应用程序。

现在，您需要做的只是推送代码更新，其余部分则由OpenShift Origin处理。

### 手动重建图像 {#developers-cli-manually-rebuilding-images}

如果您的webhook无法正常工作，或者如果构建失败，并且您不想在重新构建构建之前更改代码，则可能会发现手动重新构建映像非常有用。根据您最近对分叉存储库所做的更改手动重建映像：

```
$ oc start-build ruby​​-ex
```

## 故障排除 {#developers-cli-troubleshooting}

**更改项目**

虽然该`oc new-project`命令会自动将当前项目设置为刚刚创建的项目，但您始终可以通过运行以更改项目：

```
$ oc project 
<
project-name
>
```

要查看项目列表：

```
$ oc获取项目
```

**手动触发构建**

如果构建没有自动启动，请开始构建并传输日志：

```
$ oc start-build ruby​​-ex --follow
```

或者，不要`--follow`在上面的命令中包含，而是在触发构建后发出以下命令，其中`n`是要跟踪的构建的编号：

```
$ oc logs -f build / ruby​​-ex-n
```



