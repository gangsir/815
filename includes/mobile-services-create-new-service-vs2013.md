以下步骤可在 Azure 中创建新的移动服务，并将代码添加到你的项目，以实现对此新服务的访问。在你能够创建移动服务之前，必须将来自 Azure 订阅的 publishsettings 文件导入 Visual Studio。这使 Visual Studio 能够代表你连接到 Azure。创建新的移动服务时，你必须指定该移动服务用于存储应用程序数据的 Azure SQL数据库。

1.  在 Visual Studio 2013 中，打开解决方案资源管理器，右键单击项目，单击“添加”，然后单击“连接的服务...” 。

    ![添加连接的服务][]

2.  在“服务管理器”对话框中，单击“创建服务...” ，然后从“创建移动服务”对话框的“订阅”中选择“\<导入...\>”。 

    ![从 VS 2013 创建新移动服务][]

3.  在“导入 Azure 订阅”中，单击“下载订阅文件” ，登录到你的 Azure 帐户（如果需要），当浏览器请求保存文件时，单击“保存”。 

    ![在 VS 中下载订阅文件][]

	<div class="dev-callout"><b>说明</b>

    <p>登录窗口显示在浏览器中，可能位于 Visual Studio 窗口后面。请记住，应记下你保存下载的 .publishsettings 文件的位置。如果你的项目已连接到 Azure 订阅，可以跳过此步骤。</p>
	</div>

4.  单击“浏览” ，导航到你保存 .publishsettings 文件的位置，选择该文件，然后单击“打开”， 再单击“导入” 。

    ![在 VS 中导入订阅][]

    Visual Studio 导入连接到你的 Azure 订阅所需的数据。当你的订阅已经具有一个或多个现有移动服务时，则会显示服务名称。

	<div class="dev-callout"><b>安全说明</b>

    <p>导入发布设置之后，应考虑删除所下载的 .publishsettings 文件，因为该文件包含可供他人用来访问你的帐户的信息。如果你打算保留该文件，以备在其他连接的应用程序项目中使用，请保护该文件的安全。</p>
	</div>

5.  回到“创建移动服务” 对话框，选择你的“订阅” 以及移动服务所需的“区域” ，然后键入移动服务的“名称”。 

	<div class="dev-callout"><b>说明</b>

    <p>移动服务名称必须是唯一的。当你提供的名称不可用时，“名称”旁边将显示红色的 X。 </p>
	</div>

6.  在“数据库”中 ，选择“\<创建免费 SQL数据库\>” ，提供“服务器用户名”、 “服务器密码”和“服务器密码确认” ，然后单击“创建” 。

    ![“从 VS 2013 创建服务”第 2 部分][]

    > [WACOM.NOTE]
    > 在本教程中，你将创建新的免费 SQL数据库 实例和服务器。你可以重用此新数据库，并对其进行管理，如同任何其他 SQL数据库 实例一样。你只能有一个免费数据库实例。如果你在新移动服务所在的同一地区已经有了数据库，则可选择使用现有数据库。当你选择现有数据库时，请确认你提供了正确的登录凭据。如果你提供了错误的登录凭据，则会在不正常的状态下创建移动服务。

    创建移动服务之后，会将对移动服务客户端库的引用添加到项目，你的项目源代码也会更新。

  [添加连接的服务]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [从 VS 2013 创建新移动服务]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [在 VS 中下载订阅文件]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [在 VS 中导入订阅]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
  [“从 VS 2013 创建服务”第 2 部分]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png
