<a name="add-select-images"></a>
## 更新应用程序以调用自定义 API

1.  在 Visual Studio 中，打开你的快速启动项目中的 MainPage.xaml 文件，找到名为 `ButtonRefresh` 的 "Button" 元素，并将其替换为以下 XAML 代码：

        <StackPanel Orientation="Horizontal">
        <Button Margin="72,0,0,0" Name="ButtonRefresh" 
        Click="ButtonRefresh_Click">Refresh</Button>
        <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
        Click="ButtonCompleteAll_Click">Complete All</Button>
        </StackPanel>

    这样可将新按钮添加到页。

2.  打开 MainPage.xaml.cs 代码文件，并添加以下类定义代码：

        public class MarkAllResult
        {
        public int Count { get; set; }
        }

    此类用于保存自定义 API 返回的行计数值。

3.  找到 "MainPage" 类中的 "RefreshTodoItems" 方法，并确认 `query` 是使用以下 "Where" 方法定义的：

        .Where(todoItem => todoItem.Complete == false)

    这样可以筛选项，使得查询不返回已完成的项。

4.  在 "MainPage" 类中，添加以下方法：

        private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
        {
        string message;
        try
            {
        // Asynchronously call the custom API using the POST method. 
        var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll", 
        System.Net.Http.HttpMethod.Post, null);
        message =  result.Count + " item(s) marked as complete.";
        RefreshTodoItems();
            }
        catch (MobileServiceInvalidOperationException ex)
            {
        message = ex.Message;                
            }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
        }

    此方法可处理新按钮的 "Click" 事件。[InvokeApiAsync][] 方法在客户端上调用，该客户端向新的自定义 API 发送 POST 请求。与任何错误相同，自定义 API 返回的结果也显示在消息对话框中。

<a name="test"></a>
## 测试应用程序

1.  在 Visual Studio 中，按 "F5" 键以重新生成项目并启动应用程序。

2.  在应用程序的“插入 TodoItem”中键入一些文本 ，然后单击“保存” 。

3.  重复上述步骤，直至你将多个 Todo 项添加到列表。

4.  单击“Complete All”（完成全部） 按钮。

    ![][0]

    此时会显示一个消息框，指示标记为完成的多个项，并再次执行筛选查询，将所有项从列表中清除。

  [InvokeApiAsync]: http://msdn.microsoft.com/zh-cn/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
  [0]: ./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png
