---
title: 在 Azure 事件中樞以動態方式將分割區新增至事件中樞
description: 本文說明如何在 Azure 事件中樞以動態方式將分割區新增至事件中樞。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 4a729147eaa11497c66f82a9764dfee9492786b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87002534"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>在 Azure 事件中樞以動態方式將分割區新增至事件中樞 (Apache Kafka 主題)
事件中樞透過分割取用者模式來提供訊息串流，每位取用者只會讀取訊息串流的特定子集 (即資料分割)。 此模式能水平擴充事件處理規模，並提供佇列和主題缺少的其他串流導向功能。 資料分割是經過排序且保存在事件中樞內的事件序列。 當較新的事件送達時，系統會將其新增至序列的結尾。 如需關於分割區的一般詳細資訊，請參閱[分割區](event-hubs-scalability.md#partitions)

您可以在建立事件中樞時指定分割區的數目。 在某些情況下，您可能需要在建立事件中樞後新增分割區。 本文說明如何以動態方式將分割區新增至現有的事件中樞。 

> [!IMPORTANT]
> 動態新增分割區的功能僅適用於**專用**事件中樞叢集。

> [!NOTE]
> 針對 Apache Kafka 用戶端，請使用對應於**事件中樞**的 **Kafka 主題**。 若要了解 Azure 事件中樞與 Apache Kafka 之間的更多對應，請參閱 [Kafka 和事件中樞概念對應](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>更新分割區計數
本節說明如何以不同的方式 (PowerShell、CLI 等等) 更新事件中樞的分割區計數。

### <a name="powershell"></a>PowerShell
使用 [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub?view=azurermps-6.13.0) PowerShell 命令更新事件中樞內的分割區。 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
使用 [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub?view=azure-cli-latest#az-eventhubs-eventhub-update) CLI 命令來更新事件中樞內的磁碟分割。 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager 範本
更新 Resource Manager 範本中的 `partitionCount` 屬性值，並重新部署範本以更新資源。 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
使用 `AlterTopics` API (例如，透過 **kafka-topics** CLI 工具) 增加分割區計數。 如需詳細資訊，請參閱[修改 Kafka 主題](http://kafka.apache.org/documentation/#basic_ops_modify_topic)。 

## <a name="event-hubs-clients"></a>事件中樞用戶端
我們來看看事件中樞用戶端在事件中樞上的分割區計數更新時會有何行為。 

當您將資料分割新增至現有的「偶數」中樞時，事件中樞用戶端 `MessagingException` 會從服務接收，告知用戶端實體中繼資料 (實體是您的事件中樞，而中繼資料則是) 已經改變的分割區資訊。 用戶端會自動重新開啟 AMQP 連結，而該連結將會收取已變更的中繼資料資訊。 此時用戶端就會正常運作。

### <a name="senderproducer-clients"></a>傳送者/生產者用戶端
事件中樞提供三個傳送者選項：

- **分割區傳送者** – 在此案例中，用戶端會將事件直接傳送至分割區。 雖然分割區是可識別的，且事件可以直接傳送至該處，但我們不建議採用此模式。 在此案例中，新增分割區並不會有影響。 建議您重新啟動應用程式，使其能夠偵測到新增的分割區。 
- **分割區索引鍵傳送者** – 在此案例中，用戶端會以索引鍵傳送事件，讓所有屬於該索引鍵的事件最終都會在相同的分割區中。 在此情況下，服務會對索引鍵進行雜湊，並路由到對應的分割區。 因為雜湊變更，所以分割區計數更新可能會造成順序不足的問題。 因此，如果您在意排序，請在應用程式用完現有分割區中的所有事件後，再增加分割區計數。
- **循環配置資源傳送者 (預設)** – 在此案例中，事件中樞服務會為各個分割區的事件循環配置資源。 事件中樞服務可感知分割區計數變更，並會在分割區計數改變後的幾秒內傳送至新的分割區。

### <a name="receiverconsumer-clients"></a>接收者/取用者用戶端
事件中樞提供直接接收者和簡單的取用者程式庫，名為[事件處理器主機 (舊的 SDK)](event-hubs-event-processor-host.md) 或[事件處理器 (新的 SDK)](event-processor-balance-partition-load.md)。

- **直接接收者** – 直接接收者會接聽特定的分割區。 事件中樞的分割區進行擴增時，其執行階段行為將不受影響。 使用直接接收者的應用程式必須負責收取新的分割區，並據以指派接收者。
- **事件處理器主機** – 此用戶端不會自動重新整理實體中繼資料。 因此，在分割區計數增加時並不會收取資料。 重新建立事件處理器執行個體，將會導致收取實體中繼資料的動作，繼而為新增的分割區建立新的 Blob。 既有的 Blob 將不受影響。 建議您重新啟動所有事件處理器執行個體，以確保所有執行個體均可感知新增的分割區，且取用者之間能夠有正確的負載平衡處理。

    如果您使用舊版的 .NET SDK ([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/))，若檢查點中的分割區計數與從服務擷取的分割區計數不相符，則事件處理器主機將會在重新啟動後移除現有的檢查點。 此行為可能會對您的應用程式造成影響。 

## <a name="apache-kafka-clients"></a>Apache Kafka 用戶端
本節說明在事件中樞的分割區計數更新時，使用 Azure 事件中樞之 Kafka 端點的 Apache Kafka 用戶端將有何行為。 

使用事件中樞與 Apache Kafka 通訊協定的 Kafka 用戶端，在行為上與使用 AMQP 通訊協定的事件中樞用戶端並不相同。 Kafka 用戶端每隔 `metadata.max.age.ms` 毫秒會更新其中繼資料一次。 您可在用戶端設定中指定此值。 `librdkafka` 程式庫也使用相同的設定。 中繼資料更新會在服務有所變更時通知用戶端，包括分割區計數增加。 如需設定的清單，請參閱 [適用于事件中樞的 Apache Kafka](apache-kafka-configurations.md)設定。

### <a name="senderproducer-clients"></a>傳送者/生產者用戶端
生產者一律會指定傳送要求須包含每一組產生記錄的分割區目的地。 因此，所有產生分割都會在用戶端上透過生產者的訊息代理程式中繼資料檢視來完成。 新的分割區新增至生產者的中繼資料檢視後，即可供產生者要求使用。

### <a name="consumerreceiver-clients"></a>取用者/接收者用戶端
當取用者群組成員執行中繼資料重新整理並收取新建立的分割區時，該成員會起始群組重新平衡。 然後，所有群組成員的取用者中繼資料都將重新整理，而新的分割區將由分配的重新平衡領導者指派。

## <a name="recommendations"></a>建議

- 如果您搭配使用分割區索引鍵與生產者應用程式，並仰賴金鑰雜湊來確保分割區中的排序，則不建議以動態方式新增分割區。 

    > [!IMPORTANT]
    > 雖然現有的資料會保留排序，但對於在分割區計數變更後進行雜湊處理的訊息，分割區雜湊將會因分割區的增加而遭到破壞。
- 在下列情況下，建議您將分割區新增至現有主題或事件中樞執行個體：
    - 當您使用循環配置資源 (預設) 方法傳送事件時
     - Kafka 預設資料分割策略，範例–粘滯 Assignor 策略


## <a name="next-steps"></a>後續步驟
如需分割區的詳細資訊，請參閱[分割區](event-hubs-scalability.md#partitions)。

