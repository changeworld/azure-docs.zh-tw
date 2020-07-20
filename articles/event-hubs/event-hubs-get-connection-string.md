---
title: 取得連接字串 - Azure 事件中樞 | Microsoft Docs
description: 本文提供指示，說明如何取得可供用戶端連線至「Azure 事件中樞」的連接字串。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 271d4dcc1f2fab08dc7f625ce36e021eda225b87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320488"
---
# <a name="get-an-event-hubs-connection-string"></a>取得事件中樞連接字串

若要使用事件中樞，您必須建立事件中樞命名空間。 命名空間是適用於多個事件中樞或 Kafka 主題的範圍容器。 此命名空間可為您提供唯一的 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) \(英文\)。 一旦建立命名空間之後，您就可以獲得與事件中樞通訊所需的連接字串。

Azure 事件中樞的連接字串會在其中內嵌下列元件：

* FQDN = 您所建立之 EventHubs 命名空間的 FQDN (它包含 EventHubs 命名空間名稱，後面接著 servicebus.windows.net)
* SharedAccessKeyName = 您為應用程式 SAS 金鑰選擇的名稱
* SharedAccessKey = 產生的金鑰值。

連接字串範本看起來如下
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

範例連接字串可能看起來像是 `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

本文將為您介紹獲得連接字串的各種方式。

## <a name="get-connection-string-from-the-portal"></a>從入口網站取得連接字串
1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 選取左側導覽功能表上的 [所有服務]****。 
3. 在 **Analytics** 區段中選取 [事件中樞]****。 
4. 在事件中樞清單中，選取您的事件中樞。
6. 在 [事件中樞命名空間]**** 頁面中，選取左側功能表上的 [共用存取原則]****。

    ![共用存取原則功能表項目](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. 在原則清單中選取**共用存取原則**。 預設值為： **RootManageSharedAccessPolicy**。 您可以新增具有適當權限 (讀取、 寫入) 的原則，並使用該原則。 

    ![事件中樞共用存取原則](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. 選取 [連接字串 - 主要金鑰]**** 欄位旁的 [複製]**** 按鈕。 

    ![事件中樞 - 取得連接字串](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>使用 Azure PowerShell 取得連接字串

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以使用[AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey)來取得特定原則/規則名稱的連接字串，如下所示：

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>使用 Azure CLI 取得連接字串
您可以使用下列命令來取得命名空間的連接字串：

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

或者，您可以使用下列專案來取得 EventHub 實體的連接字串：

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

如需事件中樞的 Azure CLI 命令的相關資訊，請參閱[適用於事件中樞的 Azure CLI](/cli/azure/eventhubs)。

## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)
