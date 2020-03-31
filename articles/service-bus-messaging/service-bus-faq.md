---
title: Azure 服務匯流排常見問題集 (FAQ) | Microsoft Docs
description: 本文提供了有關 Azure 服務匯流排的一些常見問題 （FAQ） 的解答。
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760244"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure 服務匯流排 - 常見問題 （FAQ）

本文討論 Microsoft Azure 服務匯流排的一些常見問題解集。 您也可以造訪 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)，以取得一般的 Azure 價格和支援資訊。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>關於 Azure 服務匯流排的一般問題
### <a name="what-is-azure-service-bus"></a>什麼是 Azure 服務匯流排？
[Azure 服務匯流排](service-bus-messaging-overview.md)是一個非同步消息雲平臺，使您能夠在分離的系統之間發送資料。 Microsoft 以服務的形式提供此功能，這意謂著您無須裝載自己的硬體即可使用它。

### <a name="what-is-a-service-bus-namespace"></a>什麼是服務匯流排命名空間？
[命名空間](service-bus-create-namespace-portal.md)提供範圍容器，可在應用程式內定址服務匯流排資源。 必須建立命名空間才能使用服務匯流排，而且這也是開始使用的第一個步驟。

### <a name="what-is-an-azure-service-bus-queue"></a>什麼是 Azure 服務匯流排佇列？
[服務匯流排佇列](service-bus-queues-topics-subscriptions.md)是訊息儲存所在的實體。 如果您有多個應用程式，或多個需要彼此通訊的分散式應用程式部分，佇列很有用。 佇列和配送中心的類似之處在於，兩者都會接收多個產品 (訊息)，再從該處送出。

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>什麼是 Azure 服務匯流排主題和訂用帳戶？
主題可視覺化為佇列，而且在使用多個訂用帳戶時，主題會變成更豐富的訊息模型；基本上是一對多的通訊工具。 此發佈/訂閱模型 (或「pub/sub」**) 可讓應用程式將訊息傳送至具有多個訂用帳戶的主題，以便讓多個應用程式接收該訊息。

### <a name="what-is-a-partitioned-entity"></a>什麼是分割的實體？
傳統的佇列或主題由單一訊息代理程式處理並儲存在一個訊息存放區中。 只有基本和標準通訊層才支援，[分割的佇列或主題](service-bus-partitioning.md)會由多個訊息代理程式處理，並儲存在多個訊息存放區。 這項功能表示分割佇列或主題的整體輸送量不會再受到單一訊息代理程式或訊息存放區的效能所限制。 此外，即使訊息存放區暫時中斷也不會讓分割的佇列或主題無法使用。

使用分割實體時無法確保順序。 若分割無法使用，您仍可從其他分割傳送及接收訊息。

 [Premium SKU](service-bus-premium-messaging.md) 不再支援分割的實體。 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>我需要在防火牆上打開哪些埠？ 
可以使用以下與 Azure 服務匯流排的協定來發送和接收消息：

- 進階訊息佇列通訊協定 (AMQP)
- 服務匯流排傳訊通訊協定 (SBMP)
- HTTP

有關使用這些協定與 Azure 事件中心通信需要打開的出站埠，請參閱下表。 

| 通訊協定 | 連接埠 | 詳細資料 | 
| -------- | ----- | ------- | 
| AMQP | 5671 和 5672 | 請參閱[AMQP 協定指南](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 到 9354 | 請參閱[連接模式](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP、HTTPS | 80、443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>我需要哪些 IP 位址才能進行白名單？
要查找連接的右側 IP 位址到白清單，請按照以下步驟操作：

1. 從命令提示符運行以下命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 記下在 中返回的`Non-authoritative answer`IP 位址。 此 IP 位址是靜態的。 它唯一會更改的時間點是，如果將命名空間還原到其他群集。

如果對命名空間使用區域冗余，則需要執行一些其他步驟： 

1. 首先，在命名空間上運行 ns 查找。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 記下**非權威答案**部分中的名稱，該部分採用以下格式之一： 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. 運行每個帶有尾碼 s1、s2 和 s3 的 nslook，以獲取在三個可用性區域中運行的所有三個實例的 IP 位址， 


## <a name="best-practices"></a>最佳作法
### <a name="what-are-some-azure-service-bus-best-practices"></a>Azure 服務匯流排的最佳做法有哪些？
請參閱[使用服務匯流排的效能改進最佳作法][Best practices for performance improvements using Service Bus] - 這篇文章說明如何在交換訊息時將效能最佳化。

### <a name="what-should-i-know-before-creating-entities"></a>建立實體前的須知事項為何？
佇列和主題的下列屬性是不可變的。 在佈建實體時請將這個限制納入考量，因為若要修改這些屬性，就必須建立新的替代實體。

* 資料分割
* 工作階段
* 重複偵測
* 快速實體

## <a name="pricing"></a>定價
本節提供服務匯流排價格結構的一些常見問題解答。

[服務匯流排定價與計費](https://azure.microsoft.com/pricing/details/service-bus/)一文說明服務匯流排中的計費計量。 如需服務匯流排價格選項的特定資訊，請參閱[服務匯流排價格詳細資料](https://azure.microsoft.com/pricing/details/service-bus/)。

您也可以造訪 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)，以取得一般 Azure 價格資訊。 

### <a name="how-do-you-charge-for-service-bus"></a>服務匯流排的收費方式為何？
如需服務匯流排價格的完整資訊，請參閱[服務匯流排價格詳細資料][Pricing overview]。 除了註明的價格，您還需支付您的應用程式佈建所在資料中心外部的輸出相關資料傳輸費用。

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>何種服務匯流排用法需支付資料傳輸費用？ 何種不需？
指定的 Azure 區域內的任何資料傳輸都是免費提供，以及任何輸入的資料傳輸。 區域外部的資料傳送需要出口流量費用，可以在[這裡](https://azure.microsoft.com/pricing/details/bandwidth/)找到。

### <a name="does-service-bus-charge-for-storage"></a>服務匯流排是否會收取儲存體費用？
不會，服務匯流排不會收取儲存體費用。 不過，有配額會限制每個佇列/主題可保存的資料數量上限。 請參閱下一個常見問題。

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>我有一個服務匯流排標準命名空間。 為什麼我在資源組"$system"下看到費用？
Azure 服務匯流排最近升級了計費元件。 因此，如果您有服務匯流排標準命名空間，您可能會在資源組"$system"下看到資源"/訂閱/<azure_subscription_id>/資源組/$system/提供程式/Microsoft.ServiceBus/命名空間/$system"的行項。

這些費用表示預配服務匯流排標準命名空間的每個 Azure 訂閱的基本費用。 

請務必注意，這些不是新的費用，即它們也存在於以前的計費模型中。 唯一的更改是，它們現在列在"$system"下。 這是由於新計費系統中的相反項，這些帳戶在"$system"資源識別碼 下對訂閱級別費用進行分組，而不是與特定資源綁定。

## <a name="quotas"></a>配額

如需服務匯流排限制和配額的清單，請參閱[服務匯流排配額概觀][Quotas overview]。

### <a name="does-service-bus-have-any-usage-quotas"></a>服務匯流排是否有任何使用量配額？
根據預設，對於所有雲端服務，Microsoft 會設定針對所有客戶的訂用帳戶計算的彙總每月使用量配額。 若您需要的配額比這些限制來得多，您可以隨時與客戶服務部門連絡，以了解您的需求並適當地調整這些限制。 針對服務匯流排，彙總使用量配額為每個月 50 億則訊息。

雖然 Microsoft 有權停用在指定的月份內超出其使用量配額的客戶帳戶，但會傳送電子郵件通知並且在採取任何動作之前多次嘗試連絡客戶。 超出這些配額的客戶仍需負責支付超出配額的費用。

如同 Azure 上的其他服務，服務匯流排會強制執行一組特定的配額，以確保公平的資源使用量。 您可以在[服務匯流排配額概觀][Quotas overview]中找到更多關於這些配額的詳細資料。

### <a name="how-to-handle-messages-of-size--1-mb"></a>如何處理大小超過 1 MB 的訊息？
「服務匯流排」訊息服務 (佇列和主題/訂用帳戶) 可讓應用程式傳送大小最大達 256 KB (標準層) 或 1 MB (進階層) 的訊息。 如果您要處理大小超過 1 MB 的訊息，請使用[這篇部落格文章](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern) \(英文\) 中所述的宣告檢查模式。

## <a name="troubleshooting"></a>疑難排解
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>在從另一個訂用帳戶中刪除命名空間之後，我為何無法重新建立它？ 
在您從訂用帳戶刪除命名空間之後，必須先等候 4 個小時的時間，才能在另一個訂用帳戶中以相同的名稱重新建立它。 否則，您可能會收到下列錯誤訊息︰`Namespace already exists`。 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Azure 服務匯流排 API 所產生的例外狀況有哪些，其建議的動作為何？
如需可能的服務匯流排例外狀況，請參閱[例外狀況概觀][Exceptions overview]。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>什麼是共用存取簽章，何種語言可支援產生簽章？
共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 有關如何在 Node.js、PHP、JAVA、Python 和 C# 中生成您自己的簽名的資訊，請參閱[共用訪問簽名][Shared Access Signatures]一文。

## <a name="subscription-and-namespace-management"></a>訂用帳戶和命名空間管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何將命名空間移轉到另一個 Azure 訂用帳戶？

您可以使用 [Azure 入口網站](https://portal.azure.com)或 PowerShell 命令，將命名空間從一個 Azure 訂用帳戶移到另一個訂用帳戶。 若要執行此作業，命名空間必須已是作用中。 執行命令的使用者必須同時是來源和目標訂用帳戶上的系統管理員。

#### <a name="portal"></a>入口網站

若要使用 Azure 入口網站將「服務匯流排」移到另一個訂用帳戶，請依照[這裡](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)的指示操作。 

#### <a name="powershell"></a>PowerShell

下列 PowerShell 命令序列會將命名空間從一個 Azure 訂用帳戶移到另一個訂用帳戶。 若要執行這項作業，命名空間必須已經是作用中，而且執行 PowerShell 命令的使用者必須是來源與目標訂用帳戶的系統管理員。

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>後續步驟
若要深入了解服務匯流排，請參閱下列文章：

* [Azure 服務匯流排進階簡介 (部落格文章)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服務匯流排進階簡介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服務匯流排概述](service-bus-messaging-overview.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
