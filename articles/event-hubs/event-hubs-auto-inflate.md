---
title: 自動相應增加輸送量單位 - Azure 事件中樞 | Microsoft Docs
description: 在命名空間上啟用自動擴充，以自動相應增加輸送量單位。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 382d407bde61a4ca24ab5833ea51d540c8904adc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322548"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>自動相應增加 Azure 事件中樞輸送量單位
Azure 事件中樞為可高度擴充的資料串流平台。 因此，事件中樞使用量通常會在開始使用服務之後增加。 這類使用量需要增加預先決定的[輸送量單位](event-hubs-scalability.md#throughput-units)，以調整事件中樞並處理較大的傳輸速率。 「事件中樞」的「自動擴充」**** 功能會自動增加輸送量單位數以進行相應增加，進而符合使用量需求。 增加輸送量單位可避免發生節流情況，其中：

* 資料輸入速率會超出所設定的輸送量單位。
* 資料輸出要求速率會超出所設定的輸送量單位。

「事件中樞」服務可在負載超過最低閾值時增加輸送量，不會有任何要求因為發生 ServerBusy 錯誤而失敗。

## <a name="how-auto-inflate-works"></a>自動擴充的運作方式

事件中樞的流量會受到 [輸送量單位](event-hubs-scalability.md#throughput-units)控制。 單一輸送量單位可允許每秒 1 MB 的輸入及此數量兩倍的輸出。 標準事件中樞可以設定為 1-20 個輸送量單位。 自動擴充可讓您於開始時使用您所選擇的基本必要輸送量單位。 該功能接著會根據流量的提升，自動擴充至您所需的輸送量單位上限。 自動擴充提供下列優點：

- 能視需求進行相應增加的有效擴充機制。
- 自動擴充至指定的上限，以避免產生節流問題。
- 進一步控制調整，因為您可以控制調整的時機和程度。

## <a name="enable-auto-inflate-on-a-namespace"></a>在命名空間上啟用自動擴充

您可以使用下列其中一種方法，在標準層事件中樞的命名空間上啟用或停用自動擴充：

- [Azure 入口網站](https://portal.azure.com)。
- 一個 [Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)。

> [!NOTE]
> 基本層事件中樞命名空間不支援自動擴充。

### <a name="enable-auto-inflate-through-the-portal"></a>透過入口網站啟用自動擴充


#### <a name="enable-at-the-time-of-creation"></a>在建立時啟用 
您可以**在建立事件中樞命名空間時**，啟用「自動擴充」功能：
 
![在建立事件中樞命名空間時啟用自動擴充功能](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

啟用此選項時，您可以於開始時使用較小的輸送量單位，並隨著使用量需求的提升進行相應增加。 擴充的上限並不會立即影響價格，因為價格會依每小時使用的輸送量單位數來計算。

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>為現有的事件中樞啟用自動擴充功能
您也可以依據下列指示，啟用「自動擴充」功能並修改其設定： 
 
1. 在 [事件中樞命名空間]**** 頁面上，於 [自動擴充輸送量單位]**** 底下選取 [已停用]****。  

    ![在 [事件中樞命名空間] 頁面上選取輸送量單位](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. 在 [調整規模設定]**** 頁面中，選取 [啟用]**** 的核取方塊 (如果未啟用自動調整規模功能)。

    ![選取 [啟用]](./media/event-hubs-auto-inflate/scale-settings.png)
3. 輸入輸送量單位數目**上限**，或使用捲軸來設定值。 
4. (選擇性) 更新此頁面上半部的輸送量單位數目**下限**。 


> [!NOTE]
> 當您套用自動擴充設定以增加輸送量單位時，「事件中樞」服務會發出診斷記錄，您可以為其提供有關輸送量增加原因與時間的相關資訊。 若要為事件中樞啟用診斷記錄，請在 Azure 入口網站中 [事件中樞] 頁面左側功能表上選取 [診斷設定]****。 如需詳細資訊，請參閱[ Azure 事件中樞設定診斷記錄](event-hubs-diagnostic-logs.md)。 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本啟用自動擴充

您可以在 Azure Resource Manager 範本部署期間啟用自動擴充。 例如，將 `isAutoInflateEnabled` 屬性設定為 **true**，並將 `maximumThroughputUnits` 設定為 10。 例如：

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

如需完整的範本，請參閱 GitHub 上的[建立事件中樞命名空間並啟用擴充](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) \(英文\) 範本。


## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](event-hubs-what-is-event-hubs.md)

