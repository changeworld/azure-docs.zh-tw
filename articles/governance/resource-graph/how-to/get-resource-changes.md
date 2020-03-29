---
title: 取得資源變更
description: 瞭解如何查找資源何時更改、獲取已更改的屬性的清單以及評估差異。
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873024"
---
# <a name="get-resource-changes"></a>取得資源變更

通過日常使用、重新配置甚至重新部署，資源會發生變化。
更改可能來自個人或自動化流程。 大多數變化都是設計，但有時不是。 使用最近 14 天的更改歷史記錄，Azure 資源圖使您能夠：

- 查找在 Azure 資源管理器屬性上檢測到更改時
- 對於每個資源更改，請參閱屬性更改詳細資訊
- 查看檢測到的更改之前和之後的資源的完整比較

更改檢測和詳細資訊對於以下示例方案非常有價值：

- 在事件管理期間瞭解_潛在的_相關更改。 查詢特定時間時段中的變更事件並評估更改詳細資訊。
- 保持配置管理資料庫（稱為 CMDB）最新。 而不是刷新所有資源及其完整屬性集在計畫的頻率，只得到什麼更改。
- 瞭解資源更改符合性狀態時可能更改的其他屬性。 對這些附加屬性的評估可以提供對可能需要通過 Azure 策略定義進行管理的其他屬性的見解。

本文演示如何通過資源圖的 SDK 收集此資訊。 要查看 Azure 門戶中的此資訊，請參閱 Azure 策略的[更改歷史記錄](../../policy/how-to/determine-non-compliance.md#change-history-preview)或 Azure 活動日誌[更改歷史記錄](../../../azure-monitor/platform/activity-log-view.md#azure-portal)。
有關從基礎結構層到應用程式部署的更改的詳細資訊，請參閱在 Azure 監視器[中使用應用程式更改分析（預覽）。](../../../azure-monitor/app/change-analysis.md)

> [!NOTE]
> 資源圖中的更改詳細資訊適用于資源管理器屬性。 有關在虛擬機器內跟蹤更改，請參閱 Azure 自動化的[更改跟蹤](../../../automation/automation-change-tracking.md)或 Azure 策略的[VM 來賓配置](../../policy/concepts/guest-configuration.md)。

> [!IMPORTANT]
> "Azure 資源圖"中的更改歷史記錄處於公共預覽中。

## <a name="find-detected-change-events-and-view-change-details"></a>查找檢測到的變更事件並查看更改詳細資訊

查看資源上更改內容的第一步是在時間視窗內查找與該資源相關的變更事件。 每個變更事件還包括有關資源上更改的詳細資訊。 此步驟通過**資源更改**REST 終結點完成。

**資源更改**終結點接受請求正文中的以下參數：

- **資源**\[Id\]必需 ：要查找更改的 Azure 資源。
- \]**需要間隔**\[：具有_開始_日期和_結束_日期的屬性，用於何時使用**祖魯時區 （Z）** 檢查變更事件。
- **提取屬性更改**（可選）：如果回應物件包含屬性更改，則設置布林屬性。

要求本文範例：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

對於上述請求正文，**用於資源更改**的 REST API URI 是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

回應與此示例類似：

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

**對於 resourceId，** 每個檢測到的變更事件都具有以下屬性：

- **更改 Id** - 此值對該資源是唯一的。 雖然**changeId**字串有時可能包含其他屬性，但它僅保證是唯一的。
- **快照之前**- 包含檢測到更改之前拍攝的資源快照的**快照 Id**和**時間戳記**。
- **快照後**- 包含檢測到更改後拍攝的資源快照的**快照 Id**和**時間戳記**。
- **更改類型**- 描述**在快照之前**和**快照之後**之間檢測到整個更改記錄的更改類型。 值為：_創建_、_更新_和_刪除_。 **屬性"更改屬性**陣列"僅在**更改類型**為 _"更新_"時才包含。
- **屬性更改**- 此屬性陣列詳細介紹了**在快照之前**和**快照之後**之間更新的所有資源屬性 ：
  - **屬性名稱**- 已更改的資源屬性的名稱。
  - **更改類別**- 描述更改的原因。 值為：_系統和__使用者_。
  - **更改類型**- 描述為各個資源屬性檢測到的更改類型。
    值為：_插入_、_更新_、_刪除_。
  - **之前值**-**快照 前**的資源屬性的值。 **當更改類型**為 _"插入_"時，不顯示。
  - **後值**-**後快照**中資源屬性的值。 當**更改類型**_為"刪除_"時，不顯示。

## <a name="compare-resource-changes"></a>比較資源更改

使用**來自資源更改**終結點的**changeId**時，**資源更改詳細資訊**REST 終結點將用於獲取已更改的資源的快照的前後快照。

**資源更改詳細資訊**終結點需要請求正文中的兩個參數：

- **資源 Id**：要比較更改的 Azure 資源。
- **更改 Id**： 從**資源更改**中收集的**資源 Id**的唯一變更事件。

要求本文範例：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

對於上述請求正文，**用於資源更改詳細資訊**的 REST API URI 是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

回應與此示例類似：

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**在快照之前**和**快照後**，每個都給出快照拍攝的時間和當時的屬性。 更改發生在這些快照之間的某個點。 查看上面的示例，我們可以看到更改的屬性是**支援 HttpsTrafficOnly。**

要比較結果，請使用**資源更改**中的**更改**屬性，或者計算**資源更改詳細資訊**中每個快照**的內容**部分以確定差異。 如果比較快照，**時間戳記**始終顯示為差異，儘管是預期的。

## <a name="next-steps"></a>後續步驟

- 請參閱[初學者查詢](../samples/starter.md)中正在使用的語言。
- 請參閱[高級查詢](../samples/advanced.md)中的高級用途。
- 深入了解如何[探索資源](../concepts/explore-resources.md)。