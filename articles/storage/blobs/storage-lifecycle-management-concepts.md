---
title: 管理 Azure 存儲生命週期
description: 了解如何建立生命週期原則規則，以將過時資料從「經常性」層轉換到「非經常性」層和「封存」層。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598301"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>管理 Azure Blob 儲存體生命週期

資料集具有唯一的生命週期。 在早期的生命週期中，使用者經常會存取某些資料。 但資料的存取需求會隨著資料存在的時間越來越久而大幅降低。 有些資料在雲端維持閒置狀態，而且在儲存後就很少存取。 有些資料集會在建立後幾天或幾個月過期，而其他資料集在其生命週期內會積極地讀取及修改。 Azure Blob 存儲生命週期管理為 GPv2 和 Blob 存儲帳戶提供了豐富的基於規則的策略。 使用原則可將資料轉換到適當的存取層，或在資料的生命週期結束時過期。

生命週期管理原則可達成以下事項：

- 將 Blob 轉換到較少存取的儲存層 (經常性到非經常性、經常性到封存或非經常性到封存)，以最佳化效能和成本
- 在其生命週期結束時刪除 Blob
- 定義每天要在儲存體帳戶層級執行一次的規則
- 將規則套用至容器或 Blob 子集 (使用前置詞作為篩選)

請考慮以下方案：資料在生命週期的早期階段頻繁訪問，但僅在兩周後偶爾訪問。 第一個月過後，就已經很少會存取該資料集。 在這種情況下，經常性儲存層最適合早期階段。 冷存儲最適合偶爾訪問。 存檔存儲是資料老化超過一個月後的最佳層選項。 藉由根據資料存在時間來調整儲存層，您就可以按照自己的需求設計最便宜的儲存體選項。 若要達成這項轉換，可使用生命週期管理原則規則將過時資料移至較少存取的階層。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>儲存體帳戶支援

生命週期管理原則可用於通用 v2 （GPv2） 帳戶、Blob 存儲帳戶和高級塊 Blob 存儲帳戶。 在 Azure 門戶中，您可以將現有的通用 （GPv1） 帳戶升級到 GPv2 帳戶。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。  

## <a name="pricing"></a>定價

生命週期管理功能是免費的。 客戶需針對 [List Blobs ](https://docs.microsoft.com/rest/api/storageservices/list-blobs) (列出 Blob) 和[Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) (設定 Blob 層) API 呼叫的一般作業成本支付費用。 刪除操作是免費的。 如需定價的詳細資訊，請參閱[區塊 Blob 價格](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability"></a>區域可用性

生命週期管理功能在所有 Azure 區域都可用。

## <a name="add-or-remove-a-policy"></a>新增或移除原則

可以使用以下任一方法添加、編輯或刪除策略：

* [Azure 門戶](https://portal.azure.com)
* [Azure 電源外殼](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST APIs](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

可以完整閱讀或寫入策略。 不支援部分更新。 

> [!NOTE]
> 如果您啟用儲存體帳戶的防火牆規則，可能會封鎖生命週期管理要求。 您可以通過為受信任的 Microsoft 服務提供異常來取消阻止這些請求。 如需詳細資訊，請參閱[設定防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)中的＜例外狀況＞一節。

本文演示如何使用門戶和 PowerShell 方法管理原則。  

# <a name="portal"></a>[入口網站](#tab/azure-portal)

可通過 Azure 門戶添加策略有兩種方法。 

* [Azure 門戶清單視圖](#azure-portal-list-view)
* [Azure 門戶代碼視圖](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure 門戶清單視圖

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 在 Azure 門戶中，搜索並選擇存儲帳戶。 

3. 在**Blob 服務**下，選擇**生命週期管理**以查看或更改規則。

4. 選擇"**清單視圖**"選項卡。

5. 選擇 **"添加規則**"，然後填寫 **"操作集**"表單欄位。 在下面的示例中，如果 blob 在 30 天內未被修改，則將其移動到冷卻存儲。

   ![Azure 門戶中的生命週期管理操作集頁](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. 選擇**篩選器集**以添加可選篩選器。 然後，選擇 **"流覽"** 以指定要篩選的容器和資料夾。

   ![Azure 門戶中的生命週期管理篩選器集頁](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. 選擇 **"查看 + 添加**"以查看策略設置。

9. 選擇 **"添加"** 以添加新策略。

#### <a name="azure-portal-code-view"></a>Azure 門戶代碼視圖
1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 在 Azure 門戶中，搜索並選擇存儲帳戶。

3. 在**Blob 服務**下，選擇**生命週期管理**以查看或更改策略。

4. 以下 JSON 是可粘貼到 **"代碼"視圖**選項卡中的策略示例。

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. 選取 [儲存]****。

6. 有關此 JSON 示例的詳細資訊，請參閱[策略](#policy)和[規則](#rules)部分。

# <a name="powershell"></a>[電源殼](#tab/azure-powershell)

以下 PowerShell 腳本可用於向存儲帳戶添加策略。 必須`$rgname`使用資源組名稱初始化該變數。 必須`$accountName`使用存儲帳戶名稱初始化該變數。

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[範本](#tab/template)

可以使用 Azure 資源管理器範本定義生命週期管理。 下面是一個示例範本，用於部署具有生命週期管理原則的 RA-GRS GPv2 存儲帳戶。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>原則

生命週期管理原則是 JSON 文件中的規則集合：

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

策略是規則的集合：

| 參數名稱 | 參數類型 | 注意 |
|----------------|----------------|-------|
| `rules`        | 規則物件的陣列 | 策略中至少需要一個規則。 在策略中最多可以定義 100 個規則。|

策略中的每個規則都有幾個參數：

| 參數名稱 | 參數類型 | 注意 | 必要 |
|----------------|----------------|-------|----------|
| `name`         | String |規則名稱最多可以包含 256 個字母數位字元。 規則名稱會區分大小寫。  它在原則內必須是唯一的。 | True |
| `enabled`      | Boolean | 允許臨時禁用規則的可選布林。 如果未設置預設值，則預設值為 true。 | False | 
| `type`         | 列舉值 | 當前有效類型為`Lifecycle`。 | True |
| `definition`   | 定義生命週期規則的物件 | 每個定義是由篩選集和動作集組成。 | True |

## <a name="rules"></a>規則

每個規則定義包含篩選集和動作集。 [篩選集](#rule-filters)會將規則動作限制在容器或物件名稱內的一組特定物件。 [動作集](#rule-actions)會將階層或刪除動作套用至已篩選的一組物件。

### <a name="sample-rule"></a>範例規則

以下示例規則篩選帳戶以運行存在於 內部`container1`的物件的操作，然後以`foo`開頭。  

>[!NOTE]
>生命週期管理僅支援塊 Blob 類型。  

- 在上次修改 30 天後將 Blob 分層到「非經常性」層
- 在上次修改 90 天後將 Blob 分層到「封存」層
- 上次修改 2,555 天 (七年) 後刪除 Blob
- 快照集建立 90 天後刪除 Blob 快照集

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>規則篩選

篩選會將規則動作限制為儲存體帳戶內的 Blob 子集。 如果定義一個以上的篩選條件，則邏輯 `AND` 會在所有篩選器上執行。

篩選器包括：

| 篩選名稱 | 篩選類型 | 注意 | 必要 |
|-------------|-------------|-------|-------------|
| blobTypes   | 預先定義的列舉值陣列。 | 當前版本支援`blockBlob`。 | 是 |
| prefixMatch | 要比對前置詞的字串陣列。 每個規則最多可以定義 10 個首碼。 前置詞字串必須以容器名稱開頭。 例如，如果要匹配規則下`https://myaccount.blob.core.windows.net/container1/foo/...`的所有 blob，則首碼Match為`container1/foo`。 | 如果不定義首碼Match，則規則將應用於存儲帳戶中的所有 Blob。  | 否 |

### <a name="rule-actions"></a>規則動作

當滿足運行條件時，操作將應用於篩選的 Blob。

生命週期管理支援分層和刪除 blob 以及刪除 Blob 快照。 在 Blob 或 Blob 快照集上每項規則至少需定義一個動作。

| 動作        | 基底 Blob                                   | 快照式      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 支援目前在經常性儲存層的 Blob         | 不支援 |
| tierToArchive | 支援目前在經常儲存性或非經常性儲存層的 Blob | 不支援 |
| delete        | 支援                                   | 支援     |

>[!NOTE]
>如果在同一個 Blob 上定義多個動作，生命週期管理會將最便宜的動作套用至 Blob。 例如，動作 `delete` 比動作 `tierToArchive` 更便宜。 而動作 `tierToArchive` 比動作 `tierToCool` 更便宜。

運行條件基於年齡。 基底 Blob 使用上次修改時間來追蹤存在時間，而 Blob 快照集使用快照集建立時間來追蹤存在時間。

| 操作運行條件             | 條件值                          | 描述                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | 表示存在時間的整數值 (以天數為單位) | 基本 blob 操作的條件     |
| daysAfterCreationGreaterThan     | 表示存在時間的整數值 (以天數為單位) | Blob 快照操作的條件 |

## <a name="examples"></a>範例

下列範例示範如何解決生命週期原則規則的常見案例。

### <a name="move-aging-data-to-a-cooler-tier"></a>將過時資料移至較少存取的階層

此範例示範了如何轉換前置詞為 `container1/foo` 或 `container2/bar` 的區塊 Blob。 該原則會將超過 30 天未修改的 Blob 轉換到非經常性儲存體，並將 90 天內未修改的 Blob 轉換到封存層：

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-after-ingest"></a>攝錄後存檔資料

有些資料在雲端維持閒置狀態，而且儲存後就很少存取。 以下生命週期策略配置為在引入資料後不久存檔資料。 本示例將容器`archivecontainer`記憶體儲帳戶中的塊 Blob 轉換為存檔層。 轉換是通過在上次修改時間後 0 天對 blob 進行代理來完成的：

> [!NOTE] 
> 建議直接上載 blob 的存檔層以提高效率。 您可以將[putBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob)或[PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list)的 x-ms-acess 層標頭用於 REST 版本 2018-11-09 以及更新或我們最新的 Blob 存儲用戶端庫。 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>根據存在時間使資料過期

某些資料預計在創建後數天或數月過期。 您可以設定生命週期管理原則，根據資料存在時間進行刪除，以便使資料過期。 下面的示例顯示了刪除所有早于 365 天的塊 Blob 的策略。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>刪除舊的快照集

針對在其生命週期內定期修改及存取的資料，通常會使用快照集來追蹤舊版資料。 您可以建立原則，根據快照集存在時間來刪除舊的快照集。 快照集存在時間是透過評估快照集建立時間來判斷。 此原則規則會將快照集建立後超過 90 天 (含) 的容器 `activedata` 內區塊 Blob 快照集刪除。

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>常見問題集

**我創建了一個新策略，為什麼操作不立即運行？**  
平台會每天執行一次生命週期原則。 配置策略後，某些操作可能需要長達 24 小時才能首次運行。  

**如果我更新現有策略，操作運行需要多長時間？**  
更新後的策略最多需要 24 小時才能生效。 策略生效後，操作最多可能需要 24 小時。 因此，策略操作可能需要長達 48 小時才能完成。   

**我手動重新凍結存檔的 Blob，如何防止它暫時移回存檔層？**  
當 Blob 從一個訪問層移動到另一個訪問層時，其上次修改時間不會更改。 如果手動將存檔的 Blob 重新化為熱層，則生命週期管理引擎會將其移回存檔層。 禁用暫時影響此 blob 的規則，以防止它再次存檔。 當 Blob 可以安全地移回存檔層時，重新啟用規則。 如果需要永久保持熱層或冷層，也可以將 Blob 複製到其他位置。

## <a name="next-steps"></a>後續步驟

了解如何復原意外刪除的資料：

- [Azure 儲存體 Blob 的虛刪除](../blobs/storage-blob-soft-delete.md)
