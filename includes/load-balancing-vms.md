<properties writer="josephd" editor="tysonn" manager="dongill" />

# 对虚拟机进行负载平衡

在 Azure 中创建的所有虚拟机都可以通过专用网络通道与同一云服务或虚拟网络中的其他虚拟机自动连接。所有其他入站通信，例如从 Internet 主机或其他云服务或虚拟网络中的虚拟机发起的流量，都需要终结点。

终结点可用于不同目的。你使用 Azure 管理门户创建的虚拟机上的终结点的默认用途和配置是针对远程桌面协议 (RDP) 和远程 Windows PowerShell 会话流量的。利用这些终结点，你可以通过 Internet 远程管理虚拟机。

终结点的另外一种用途是 Azure 负载平衡器配置，在多个虚拟机或服务之间分配特定类型流量。例如，你可将 Web 请求流量负载分配到多个 Web 服务器或 Web 角色。

为虚拟机定义的每个终结点将获得公用和专用端口，包括 TCP 或 UDP。Internet 主机将它们的传入流量发送到云服务的公用 IP 地址以及公用端口。云服务中的虚拟机和服务在它们的专用 IP 地址和专用端口上进行侦听。负载平衡器将传入流量的公用 IP 地址和端口号映射到虚拟机的专用 IP 地址和端口号，对于来自虚拟机的响应流量，则进行反向的映射。

当你配置了多个虚拟机或服务之间的流量负载平衡时，Azure 提供传入流量的随机分配。

对于包含 Web 角色或辅助角色实例的云服务，你可以在服务定义中定义一个公共终结点。对于包含虚拟机的云服务，你可在创建虚拟机时为虚拟机添加终结点，也可随后再添加终结点。

下图显示了公用和专用 TCP 端口 80 的标准（未加密）Web 流量的负载平衡终结点，由三个虚拟机共享。这三个虚拟机位于一个负载平衡集中。

![负载平衡][负载平衡]

当 Internet 客户端将网页请求发送到云服务的公用 IP 地址和 TCP 端口 80 时，负载平衡器会在负载平衡集中的三个虚拟机之间对这些请求进行随机平衡。

若要创建 Azure 虚拟机的负载平衡集，请使用以下步骤：

-   [步骤 1：创建第一台虚拟机][步骤 1：创建第一台虚拟机]
-   [步骤 2：在同一云服务中创建其他虚拟机][步骤 2：在同一云服务中创建其他虚拟机]
-   [步骤 3：使用第一台虚拟机创建负载平衡集][步骤 3：使用第一台虚拟机创建负载平衡集]
-   [步骤 4：将虚拟机添加到负载平衡集][步骤 4：将虚拟机添加到负载平衡集]

## <span id="firstmachine"></span> </a>步骤 1：创建第一台虚拟机

登录到 [Azure 管理门户][Azure 管理门户]（如果你尚未这么做）。你可以使用“从库中”或“快速创建”方法创建第一台虚拟机。

-   **从库中** - 使用“从库中”方法，你能够在创建虚拟机时创建终结点，这样就能够为你创建虚拟机时创建的云服务指定名称。有关说明，请参阅[创建运行 Linux 的虚拟机][创建运行 Linux 的虚拟机]或[创建运行 Windows Server 的虚拟机][创建运行 Windows Server 的虚拟机]。

-   **快速创建** - 通过从映像库中选择映像并提供基本信息来创建虚拟机。当你使用此方法时，需要在创建虚拟机之后添加终结点。此方法还使用默认名称来创建云服务。有关更多信息，请参阅[如何快速创建虚拟机][如何快速创建虚拟机]。

**注意**：使用“快速创建”创建虚拟机之后，管理门户的“云服务”页将列出新云服务的名称，以及有关该服务的其他信息。

## <span id="addmachines"></span> </a>步骤 2：在同一云服务中创建其他虚拟机

使用“从库中”方法，在第一台虚拟机的同一云服务中创建其他虚拟机。

## <span id="loadbalance"></span> </a>步骤 3：使用第一台虚拟机创建负载平衡集

1.  在 Azure 管理门户中，单击“虚拟机”，然后单击第一台虚拟机的名称。

2.  单击“终结点”，然后单击“添加”。

3.  在“将终结点添加到虚拟机”页上，单击右箭头。

4.  在“指定终结点的详细信息”页上执行以下操作：

    -   在“名称”中，键入终结点的名称，并从通用协议的预定义终结点列表中进行选择。
    -   在“协议”中，根据需要选择终结点类型需要的协议，例如 TCP 或 UDP。
    -   在“公用端口”和“专用端口”中，根据需要键入你希望虚拟机使用的端口号。你可以使用虚拟机上的专用端口和防火墙规则，从而以适合你的应用程序的方式重定向流量。专用端口可以与公用端口一样。例如，对于 Web (HTTP) 流量的终结点，你可将端口 80 指定为公用端口和专用端口。

5.  选择“创建负载平衡集”，然后单击右箭头。

6.  在“配置负载平衡集”页上，键入负载平衡集的名称，然后分配用于 Azure 负载平衡器的探测行为的值。负载平衡器使用探测来确定负载平衡集中的虚拟机是否可用于接收传入流量。

7.  单击复选标记以创建负载平衡的终结点。你将在虚拟机的“终结点”页的“负载平衡集名称”列中看到“是”。

## <span id="addtoset"></span> </a>步骤 4：将虚拟机添加到负载平衡集

创建负载平衡集之后，将其他虚拟机添加到其中。对同一云服务中的每个虚拟机执行以下操作：

1.  在管理门户中，依次单击“虚拟机”、虚拟机的名称、“终结点”、“添加”。

2.  在“将终结点添加到虚拟机”页上，单击“将终结点添加到现有负载平衡集”，选择负载平衡集的名称，然后单击右箭头。

3.  在“指定终结点详细信息”页上，键入终结点的名称，然后单击复选标记。

<!-- LINKS -->

  [负载平衡]: ./media/load-balancing-vms/LoadBalancing.png
  [步骤 1：创建第一台虚拟机]: #firstmachine
  [步骤 2：在同一云服务中创建其他虚拟机]: #addmachines
  [步骤 3：使用第一台虚拟机创建负载平衡集]: #loadbalance
  [步骤 4：将虚拟机添加到负载平衡集]: #addtoset
  [Azure 管理门户]: http://manage.windowsazure.cn
  [创建运行 Linux 的虚拟机]: ../virtual-machines-linux-tutorial
  [创建运行 Windows Server 的虚拟机]: ../virtual-machines-windows-tutorial
  [如何快速创建虚拟机]: ../virtual-machines-quick-create
