<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="服务总线 Queues" pageTitle="How to use 服务总线 queues (Node.js) - Azure" metaKeywords="Azure 服务总线 queues, Azure queues, Azure messaging, Azure queues Node.js" description="Learn how to use 服务总线 queues in Azure. Code samples written in Node.js." metaCanonical="" services="service-bus" documentationCenter="Node.js" title="How to Use 服务总线 Queues" authors="larryfr" solutions="" manager="" editor="" />

# 如何使用 服务总线 队列

本指南演示如何使用 服务总线 队列。示例用 JavaScript 编写并使用 Node.js Azure 模块。所涉及的任务包括**创建队列、发送和接收消息**和**删除队列**。有关队列的详细信息，请参阅[后续步骤][后续步骤]一节。

## 目录

-   [什么是 服务总线 队列？][什么是 服务总线 队列？]
-   [创建服务命名空间][创建服务命名空间]
-   [获得命名空间的默认管理凭据][获得命名空间的默认管理凭据]
-   [创建 Node.js 应用程序][创建 Node.js 应用程序]
-   [配置应用程序以使用 服务总线][配置应用程序以使用 服务总线]
-   [如何：创建队列][如何：创建队列]
-   [如何：向队列发送消息][如何：向队列发送消息]
-   [如何：从队列接收消息][如何：从队列接收消息]
-   [如何：处理应用程序崩溃和不可读消息][如何：处理应用程序崩溃和不可读消息]
-   [后续步骤][后续步骤]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>创建 Node.js 应用程序

创建一个空的 Node.js 应用程序。有关创建 Node.js 应用程序的说明，请参阅[创建 Node.js 应用程序并将其部署到 Azure 网站][创建 Node.js 应用程序并将其部署到 Azure 网站]、[Node.js 云服务][Node.js 云服务]（使用 Windows PowerShell）或[使用 WebMatrix 构建网站][使用 WebMatrix 构建网站]。

## <a name="configure-app"> </a>配置应用程序以使用 服务总线

若要使用 Azure 服务总线，需要下载和使用 Node.js azure 包。其中包括一组便于与 服务总线 REST 服务进行通信的库。

### 使用 Node 包管理器 (NPM) 可获取该程序包

1.  使用 **Windows PowerShell for Node.js** 命令窗口导航到你在其中创建了示例应用程序的 **c:\\node\\sbqueues\\WebRole1** 文件夹。

2.  在命令窗口中键入 **npm install azure**，这应该产生
    类似如下的输出：

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  可以手动运行 **ls** 命令来验证是否创建了**node\_modules** 文件夹。在该文件夹中，找到**azure** 程序包，其中包含访问 服务总线 队列所需的库。

### 导入模块

使用记事本或其他文本编辑器将以下内容添加到应用程序的 **server.js** 文件的顶部：

    var azure = require('azure');

### 设置 Azure 服务总线 连接

azure 模块将读取环境变量 AZURE\_SERVICEBUS\_NAMESPACE 和 AZURE\_SERVICEBUS\_ACCESS\_KEY 以获取连接 Azure 服务总线 所需的信息。如果未设置这些环境变量，则必须在调用 **createServiceBusService** 时指定帐户信息。

有关在 Azure 云服务的配置文件中设置环境变量的示例，请参阅[使用存储构建 Node.js 云服务][使用存储构建 Node.js 云服务]。

有关在管理门户中为 Azure 网站设置环境变量的示例，请参阅[使用存储构建 Node.js Web 应用程序][使用存储构建 Node.js Web 应用程序]。

## <a name="create-queue"> </a>如何创建队列

可以通过 **ServiceBusService** 对象处理队列。以下
代码将创建 **ServiceBusService** 对象。将它添加到
靠近 **server.js** 文件顶部、用于导入 azure 模块的语句
之后的位置：

    var serviceBusService = azure.createServiceBusService();

通过对 **ServiceBusService** 对象调用 **createQueueIfNotExists**，将返回指定的队列（如果存在），否则将使用指定的名称创建一个新队列。以下代码使用**createQueueIfNotExists** 创建或连接到名为“myqueue”的队列：

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** 还支持其他选项，以允许你重写默认队列设置，例如消息生存时间或最大队列大小。下面的示例演示如何将最大队列大小设置为 5 GB，将生存时间设置为 1 分钟：

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

### 筛选器

可选的筛选操作可应用于使用 **ServiceBusService** 执行的操作。筛选操作可以包括日志记录、自动重试等。筛选器是实现了具有签名的方法的对象：

        function handle (requestOptions, next)

在对请求选项执行预处理后，该方法需要调用“next”并且传递具有以下签名的回调：

        function (returnObject, finalCallback, next)

在此回调中并且在处理 returnObject（来自对服务器请求的响应）后，回调需要调用 next（如果它存在以便继续处理其他筛选器）或只调用 finalCallback 以便结束服务调用。

Azure SDK for Node.js 中附带了两个实现了重试逻辑的筛选器，分别是 **ExponentialRetryPolicyFilter** 和**LinearRetryPolicyFilter**。下面的代码创建一个 **ServiceBusService** 对象，该对象使用**ExponentialRetryPolicyFilter**：

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>如何向队列发送消息

若要将消息发送到 服务总线 队列，你的应用程序将会对**ServiceBusService** 对象调用 **sendQueueMessage** 方法。发送到 服务总线 队列（以及从中接收的）的消息是**BrokeredMessage** 对象，并且具有一组标准属性（如 **Label** 和 **TimeToLive**）、一个用于保存自定义应用程序特定属性的字典，以及一组任意的应用程序数据。应用程序可以通过将字符串值作为消息传送来设置消息正文，任何必需的标准属性将用默认值填充。

下面的示例演示如何使用 **sendQueueMessage** 向名为“myqueue”的队列发送一条测试消息：

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        };
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

服务总线 队列支持最大为 256 KB 的消息（标头最大为 64 KB，其中包括标准和自定义应用程序属性）。一个队列可包含的消息数不受限制，但消息的总大小受限。此队列大小是在创建时定义的，上限为 5 GB。

## <a name="receive-messages"> </a>如何从队列接收消息

对 **ServiceBusService** 对象使用 **receiveQueueMessage** 方法可从队列接收消息。默认情况下，在读取消息后将从队列中删除它们；不过，通过将可选参数 **isPeekLock** 设置为 **true**，你可以读取（扫视）并锁定消息，以避免将其从队列中删除。

在接收过程中读取并删除消息的默认行为是最简单的模式，并且最适合在发生故障时应用程序可以容忍不处理消息的情况。为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。由于 服务总线 会将消息标记为“将使用”，因此当应用程序重启并重新开始使用消息时，它会丢失在发生崩溃前使用的消息。

如果将 **isPeekLock** 参数设置为 **true**，则接收将变成一个两阶段操作，这样就可以支持无法容忍遗漏消息的应用程序。当 服务总线 接收请求时，它会查找将要被使用的下一条消息，将其锁定以防止其他使用者接收它，然后将它返回到应用程序。当应用程序完成处理该消息（或将其可靠地存储下来以备将来处理）后，它会通过调用 **deleteMessage** 方法并提供要删除的消息作为参数来完成接收过程的第二阶段。**deleteMessage** 方法将此消息标记为“已使用”并将其从队列中删除。

下面的示例演示了如何使用 **receiveQueueMessage** 接收和处理消息。该示例先接收并删除一条消息，然后将 **isPeekLock** 设置
为 true 后再接收一条消息，最后使用 **deleteMessage** 删除该消息：

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## <a name="handle-crashes"> </a>如何处理应用程序崩溃和不可读消息

服务总线 提供了相关功能来帮助你轻松地从应用程序错误或消息处理问题中恢复。如果接收方应用程序因某种原因无法处理消息，
则它可以对**ServiceBusService** 对象调用 **unlockMessage** 方法。这将导致 服务总线 解锁队列中的消息并使其能够
重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

还存在与队列中已锁定消息关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），服务总线 将自动解锁该消息并使它可再次被接收。

如果应用程序在处理消息之后，但在调用 **deleteMessage** 方法之前崩溃，则在应用程序重新启动时会将该消息重新传送给它。此情况通常称作**至少处理一次**，即每条消息将至少被处理一次，但在某些情况下，同一消息可能会被重新传送。如果方案无法容忍重复处理，则应用程序开发人员应向其应用程序添加更多逻辑以处理重复消息传送。这通常可以通过使用消息的**MessageId** 属性来实现，该属性在多次传送尝试中保持不变。

## <a name="next-steps"> </a> 后续步骤

现在，你已了解有关 服务总线 队列的基础知识，单击下面的链接可了解更多信息。

-   查看 MSDN 参考：[队列、主题和订阅。][队列、主题和订阅。]
-   访问 GitHub 上的 [Azure SDK for Node][Azure SDK for Node] 存储库。

  [后续步骤]: #next-steps
  [什么是 服务总线 队列？]: #what-are-service-bus-queues
  [创建服务命名空间]: #create-a-service-namespace
  [获得命名空间的默认管理凭据]: #obtain-default-credentials
  [创建 Node.js 应用程序]: #create-app
  [配置应用程序以使用 服务总线]: #configure-app
  [如何：创建队列]: #create-queue
  [如何：向队列发送消息]: #send-messages
  [如何：从队列接收消息]: #receive-messages
  [如何：处理应用程序崩溃和不可读消息]: #handle-crashes
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [创建 Node.js 应用程序并将其部署到 Azure 网站]: /zh-cn/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js 云服务]: /zh-cn/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [使用 WebMatrix 构建网站]: /zh-cn/develop/nodejs/tutorials/web-site-with-webmatrix/
  [使用存储构建 Node.js 云服务]: /zh-cn/develop/nodejs/tutorials/web-app-with-storage/
  [使用存储构建 Node.js Web 应用程序]: /zh-cn/develop/nodejs/tutorials/web-site-with-storage/
  [队列、主题和订阅。]: http://msdn.microsoft.com/zh-cn/library/windowsazure/hh367516.aspx
  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
