---
title: 取得資源變更
description: 瞭解如何找出變更資源的時間、取得已變更的屬性清單，以及評估差異。
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873024"
---
# <a name="get-resource-changes"></a>取得資源變更

資源會在每日使用、重新設定，甚至重新部署的過程中有所變更。
變更可以來自個人或自動化程式。 大部分的變更都是根據設計，但有時不會。 在過去14天的變更歷程記錄中，Azure Resource Graph 可讓您：

- 尋找在 Azure Resource Manager 屬性上偵測到變更的時間
- 如需每項資源變更，請參閱屬性變更詳細資料。
- 查看偵測到的變更前後的資源完整比較

變更偵測和詳細資料對於下列範例案例很有用：

- 在事件管理期間，瞭解_潛在_的相關變更。 在特定時間範圍內查詢變更事件，並評估變更詳細資料。
- 將設定管理資料庫（稱為 CMDB）保持在最新狀態。 並不是以排程的頻率重新整理所有資源及其完整的屬性集，而是只會取得變更的內容。
- 瞭解當資源變更合規性狀態時，其他哪些屬性可能已變更。 評估這些額外的屬性可提供其他可能需要透過 Azure 原則定義來管理的屬性的深入解析。

本文說明如何透過 Resource Graph 的 SDK 收集這項資訊。 若要在 Azure 入口網站中查看這項資訊，請參閱 Azure 原則的[變更歷程記錄](../../policy/how-to/determine-non-compliance.md#change-history-preview)或 Azure 活動記錄[變更歷程記錄](../../../azure-monitor/platform/activity-log-view.md#azure-portal)。
如需將應用程式從基礎結構層變更為應用程式部署的詳細資訊，請參閱在 Azure 監視器中[使用應用程式變更分析（預覽）](../../../azure-monitor/app/change-analysis.md) 。

> [!NOTE]
> Resource Graph 中的變更詳細資料適用于 Resource Manager 屬性。 如需追蹤虛擬機器內部的變更，請參閱 Azure 自動化的[變更追蹤](../../../automation/automation-change-tracking.md)或 Azure 原則的[vm 來賓](../../policy/concepts/guest-configuration.md)設定。

> [!IMPORTANT]
> Azure Resource Graph 中的變更歷程記錄處於公開預覽狀態。

## <a name="find-detected-change-events-and-view-change-details"></a>尋找偵測到的變更事件並查看變更詳細資料

查看資源變更的第一個步驟，是在一段時間內尋找與該資源相關的變更事件。 每個變更事件也會包含資源變更的詳細資料。 此步驟是透過**resourceChanges** REST 端點完成。

**ResourceChanges**端點會接受要求主體中的下列參數：

- **resourceId** \[需要\]resourceId：要在其中尋找變更的 Azure 資源。
- **interval** \[需要\]間隔：具有_開始_和_結束_日期的屬性，可供何時使用**祖魯時區（Z）** 檢查變更事件。
- **fetchPropertyChanges** （選擇性）：布林值屬性，如果回應物件包含屬性變更，則設定。

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

使用上述要求主體時， **resourceChanges**的 REST API URI 為：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

回應看起來類似此範例：

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

**ResourceId**的每個偵測到的變更事件都具有下列屬性：

- **changeId** -此值對該資源而言是唯一的。 雖然**changeId**字串有時可能會包含其他屬性，但它只保證是唯一的。
- **beforeSnapshot** -包含偵測到變更之前所取得之資源快照集的**snapshotId**和**時間戳記**。
- **afterSnapshot** -包含偵測到變更之後所建立之資源快照集的**snapshotId**和**時間戳記**。
- **changeType** -描述在**beforeSnapshot**和**afterSnapshot**之間針對整個變更記錄偵測到的變更類型。 值為： _Create_、 _Update_和_Delete_。 只有在**changeType**是_Update_時，才會包含**propertyChanges**屬性陣列。
- **propertyChanges** -此屬性陣列會詳細說明**beforeSnapshot**與**afterSnapshot**之間已更新的所有資源屬性：
  - **propertyName** -已改變之資源屬性的名稱。
  - **changeCategory** -描述進行變更的內容。 值為： [_系統_] 和 [_使用者_]。
  - **changeType** -描述針對個別資源屬性偵測到的變更類型。
    值為： _Insert_、 _Update_、 _Remove_。
  - **beforeValue** - **beforeSnapshot**中 resource 屬性的值。 當**changeType**為_Insert_時，不會顯示。
  - **afterValue** - **afterSnapshot**中 resource 屬性的值。 當**ChangeType** _移除_時，不會顯示。

## <a name="compare-resource-changes"></a>比較資源變更

透過**resourceChanges**端點的**changeId** ，就會使用**resourceChangeDetails** REST 端點來取得已變更資源的前後快照集。

**ResourceChangeDetails**端點需要要求主體中的兩個參數：

- **resourceId**：用來比較變更的 Azure 資源。
- **changeId**：從**ResourceChanges**收集的**resourceId**的唯一變更事件。

要求本文範例：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

使用上述要求主體時， **resourceChangeDetails**的 REST API URI 為：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

回應看起來類似此範例：

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

**beforeSnapshot**和**afterSnapshot**會分別提供快照集的建立時間，以及當時的屬性。 這項變更會發生在這些快照集之間的某個時間點。 查看上述範例，我們可以看到變更的屬性已**supportsHttpsTrafficOnly**。

若要比較結果，請使用**resourceChanges**中的 [**變更**] 屬性，或評估**resourceChangeDetails**中每個快照集的**內容**部分，以判斷差異。 如果您比較快照集，則**時間戳記**一律會顯示為差異，而不是預期。

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言。
- 請參閱 advanced[查詢](../samples/advanced.md)中的 advanced 使用。
- 深入了解如何[探索資源](../concepts/explore-resources.md)。