<properties title="Azure SQL数据库 灵活扩展" pageTitle="Azure SQL数据库 灵活扩展" description="使用 Azure SQL数据库 的灵活扩展功能，轻松扩展云中的数据库资源。" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh"></tags>

# Azure SQL数据库 灵活扩展

欢迎使用 Azure SQL数据库 灵活扩展公共预览版！

### 约定和挑战

Azure SQL数据库 灵活扩展将交付云计算约定，并在 Azure SQL DB 平台上同时实现几乎无限制的容量以及灵活性。到目前为止，云服务提供程序已经能够在无限计算容量和 Blob 存储等大多数方面进行交付。但是，当灵活性涉及云中有状态的数据处理（特别是关系数据库处理）时，灵活性仍然是一个挑战。我们发现这些挑战主要出现在以下两种情况中：

-   增加和缩小您的工作负载的关系数据库部分的容量。
-   管理有状态的数据库工作负载及其数据的利用率热点。

通常，通过购买更多可托管应用程序数据层的硬件来处理这些情况。但是，在预定义商品硬件上执行所有处理的云中，此选项受限制。就成本和灵活性而言，出于容量原因而在几个扩展单元上进行分片（或分发数据）和处理，可提供一种出色的替代方法来代替传统的向上扩展方法。在过去的几年中，我们发现客户必须在 Azure SQL DB 顶部实现他们自己的向外扩展方法，才能成功进行分片。对于某些客户，这意味着要管理数百或数千个 Azure SQL数据库。这意味着其数据层中有大量代码，该数据层用于处理分片的复杂性，而不是应用程序的业务逻辑。

经过多年与客户直接合作，我们从这些项目中发现了几种可供分片的模式。Azure SQL数据库 灵活扩展围绕这些模式提供了客户端库和服务产品。通过灵活扩展，您可以更轻松地开发、扩展和管理您的 Azure 应用程序的有状态数据层。

然后，您可以侧重于您的应用程序的业务逻辑，而不是构建分片的基础结构。

## 功能

无论是开发人员还是管理员，使用分片在开发、扩展和管理向外扩展的应用程序时都存在挑战。对于这两种角色，Azure SQL DB 灵活扩展可使生活更加轻松。图形中的数字概述了与此公共预览版一起交付的主要功能。
下半部分显示了应用程序的数据层以及如何在多个数据库上分发其数据（称为分片）。假定使用多个数据库存储多个分片的数据。

有关此处使用的术语的定义，请参阅[灵活扩展词汇表][灵活扩展词汇表]。

### 通过分片灵活扩展

![][0]

该图在左侧和右侧显示开发人员和管理员。当提交局部分片操作，而非具有其自己的语义的跨分片操作时，客户期望获取完全的 T-SQL 功能。
通过以下特定功能，Azure SQL数据库 灵活扩展的公共预览版可更加轻松地开发标准 Azure SQL DB 应用程序：

-   **分片映射管理**：分片映射管理 (1) 是使应用程序能够管理有关其分片的各种元数据的功能。分片映射管理是灵活扩展客户端库的一项功能。开发人员可以使用此功能来注册分片、介绍如何将单独的分片键或键范围映射到分片，以及在数据层中的分片布局发生变化时维护此元数据以反映容量更改。分片映射管理组成大量重复代码，当客户自行实现分片时，他们必须在其应用程序中编写这些代码。有关详细信息，请参阅[分片映射管理][分片映射管理]

-   **数据依赖路由**：假设将一个请求传入应用程序。基于该请求的分片键值，应用程序需要确定为此分片键值保存数据的正确分片，然后打开到此分片 (2) 的连接以处理该请求。借助数据依赖路由，您能够通过对应用程序的分片映射的单个简单调用打开连接。数据依赖路由是基础结构代码的另一个区域，现在它由灵活扩展客户端库中的功能所覆盖。有关详细信息，请参阅[数据依赖路由][数据依赖路由]

-   **多分片查询 (MSQ)**：当一个请求涉及多个（或所有）分片时，多分片查询将生效。多分片查询 (3) 在所有分片或一组分片上执行相同的 T-SQL 代码。使用 UNION ALL 语义，将参与分片中的结果合并到一个总结果集中。通过客户端库处理许多任务来公开此功能，包括：连接管理、线程管理、故障处理和中间结果处理。MSQ 最多可以查询数百个分片。有关详细信息，请参阅[多分片查询][多分片查询]。

-   **分片灵活性**：借助此功能，管理员可以通过 PowerShell 脚本以及通过 Azure 自动化服务，自动垂直（上拨和下拨单个分片版本）和水平（从分片映射添加或删除分片）扩展其分片的环境。有关详细信息，请参阅[分片灵活性][分片灵活性]。

-   **拆分/合并服务**：当容量需求随着业务发展趋势而前后波动时，应用程序也需要在多个数据库 (4) 上灵活地重新分发数据。灵活扩展可为增加和缩小数据层容量，以及在还要移动数据的情况下管理分片应用程序的热点，提供客户托管的服务体验。它构建于在不同分片之间按需移动 shardlet 的基础功能之上，并与分片映射管理集成，以维护一致的映射和准确的数据依赖路由连接。有关详细信息，请参阅[通过灵活扩展拆分和合并][通过灵活扩展拆分和合并]

## 常见分片模式

**分片**是一项可跨许多独立的数据库分发大量相同结构数据的技术。这项技术尤其受到为最终客户或企业创建软件即服务 (SaaS) 产品的云开发人员的欢迎。这些最终客户通常称为“租户”。需要分片的原因有很多：

-   数据总量过大而超出单个数据库的限制范围
-   整个工作负载的事务吞吐量超出单个数据库的容量
-   租户可能需要与其他租户物理隔离，因此每个租户都需要单独的数据库
-   由于合规性、性能或地理政治的原因，不同的数据库部分可能需要驻留在不同的地域中。

在其他方案（例如从分布式设备中引入数据）中，分片可用于填充临时组织的一组数据库。例如，可以在每天或每周专门使用某个单独的数据库。在此情况下，分片键可以是一个表示日期的整数（显示在分片表的所有行中），应用程序必须将用于检索有关日期范围的信息的查询路由到涉及相关范围的数据库子集。

当应用程序中的每个事务均受限于分片键的单个值时，分片效果最佳。这将确保所有事务都将本地放置在特定数据库中。

某些应用程序使用最简单的方法为每个租户创建一个单独的数据库。这就是**单租户分片模式**，它提供了隔离、备份/还原功能以及根据租户粒度进行的资源缩放。借助单租户分片，每个数据库都将与特定租户 ID 值（或客户键值）关联，而该键无需始终出现在数据本身中。应用程序负责将每个请求路由到相应的数据库。

![][1]

其他方案是将多个租户一同打包到数据库中，而非将其隔离到单独的数据库中。这是典型的**多租户分片模式**，该模式的使用可能取决于对成本、效率或应用程序可管理大量极小型租户这一情况的考虑。在多租户分片中，数据库表中的行全都被设计为带有可标识租户 ID 的键或分片键。同样，应用程序层负责将租户的请求路由到相应的数据库。

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors--> 
<!--Image references-->

  [灵活扩展词汇表]: ./sql-database-elastic-scale-glossary.md
  [0]: ./media/sql-database-elastic-scale-intro/overview.png
  [分片映射管理]: ./sql-database-elastic-scale-shard-map-management.md
  [数据依赖路由]: ./sql-database-elastic-scale-data-dependent-routing.md
  [多分片查询]: ./sql-database-elastic-scale-multishard-querying.md
  [分片灵活性]: ./sql-database-elastic-scale-elasticity.md
  [通过灵活扩展拆分和合并]: ./sql-database-elastic-scale-overview-split-and-merge.md
  [1]: ./media/sql-database-elastic-scale-intro/tenancy.png
