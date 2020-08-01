---
title: 管理 Azure 儲存體生命週期
description: 了解如何建立生命週期原則規則，以將過時資料從「經常性」層轉換到「非經常性」層和「封存」層。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 624b8e18f8c0fb523c27c41ce9c10af93c8b6190
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446670"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>管理 Azure Blob 儲存體生命週期

資料集具有唯一的生命週期。 在早期的生命週期中，使用者經常會存取某些資料。 但資料的存取需求會隨著資料存在的時間越來越久而大幅降低。 有些資料在雲端維持閒置狀態，而且在儲存後就很少存取。 有些資料集會在建立後幾天或幾個月過期，而其他資料集在其生命週期內會積極地讀取及修改。 Azure Blob 儲存體生命週期管理為 GPv2 和 Blob 儲存體帳戶提供豐富、以規則為基礎的原則。 使用原則可將資料轉換到適當的存取層，或在資料的生命週期結束時過期。

生命週期管理原則可達成以下事項：

- 將 Blob 轉換到較少存取的儲存層 (經常性到非經常性、經常性到封存或非經常性到封存)，以最佳化效能和成本
- 在其生命週期結束時刪除 Blob
- 定義每天要在儲存體帳戶層級執行一次的規則
- 將規則套用至容器或 blob 的子集（使用名稱前置詞或[blob 索引標記](storage-manage-find-blobs.md)做為篩選準則）

假設資料在生命週期的早期階段中經常存取，但只是在兩周後才會取得。 第一個月過後，就已經很少會存取該資料集。 在這種情況下，經常性儲存層最適合早期階段。 非經常性儲存體最適合偶爾存取。 封存儲存體是每個月的資料年齡後的最佳層選項。 藉由根據資料存在時間來調整儲存層，您就可以按照自己的需求設計最便宜的儲存體選項。 若要達成這項轉換，可使用生命週期管理原則規則將過時資料移至較少存取的階層。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>儲存體帳戶支援

生命週期管理原則適用于一般用途 v2 （GPv2）帳戶、Blob 儲存體帳戶，以及 Premium 區塊 Blob 儲存體帳戶。 在 Azure 入口網站中，您可以將現有的一般用途（GPv1）帳戶升級至 GPv2 帳戶。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。  

## <a name="pricing"></a>定價

生命週期管理功能是免費的。 客戶需支付[設定 Blob 層](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)API 呼叫的一般作業成本。 刪除作業是免費的。 如需定價的詳細資訊，請參閱[區塊 Blob 價格](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability"></a>區域可用性

生命週期管理功能適用于所有 Azure 區域。

## <a name="add-or-remove-a-policy"></a>新增或移除原則

您可以使用下列任何一種方法來新增、編輯或移除原則：

* [Azure 入口網站](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

原則可以完整讀取或寫入。 不支援部分更新。 

> [!NOTE]
> 如果您啟用儲存體帳戶的防火牆規則，可能會封鎖生命週期管理要求。 您可以藉由提供信任的 Microsoft 服務的例外狀況來解除封鎖這些要求。 如需詳細資訊，請參閱[設定防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)中的＜例外狀況＞一節。

本文說明如何使用入口網站和 PowerShell 方法來管理原則。  

# <a name="portal"></a>[入口網站](#tab/azure-portal)

有兩種方式可透過 Azure 入口網站新增原則。 

* [Azure 入口網站清單視圖](#azure-portal-list-view)
* [Azure 入口網站程式碼視圖](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure 入口網站清單視圖

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 [Azure 入口網站中，搜尋並選取您的儲存體帳戶。 

3. 在 [ **Blob 服務**] 底下，選取 [**生命週期管理**] 以查看或變更您的規則。

4. 選取 [**清單視圖**] 索引標籤。

5. 選取 [**新增規則**]，然後填寫 [**動作集**表單] 欄位。 在下列範例中，如果 blob 未修改為30天，則會將它們移至非經常性儲存體。

   ![Azure 入口網站中的生命週期管理動作集頁面](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. 選取 [**篩選設定**] 以新增選擇性的篩選準則。 然後，選取 **[流覽]** 以指定要用來篩選的容器和資料夾。

   ![Azure 入口網站中的 [生命週期管理] 篩選設定頁面](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. 選取 [審核] [ **+ 新增**] 以審查原則設定。

9. 選取 **[新增]** 以新增原則。

#### <a name="azure-portal-code-view"></a>Azure 入口網站程式碼視圖
1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 [Azure 入口網站中，搜尋並選取您的儲存體帳戶。

3. 在 [ **Blob 服務**] 底下，選取 [**生命週期管理**] 以查看或變更您的原則。

4. 下列 JSON 是可以貼入 [程式**代碼視圖**] 索引標籤中的原則範例。

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

5. 選取 [儲存]  。

6. 如需有關此 JSON 範例的詳細資訊，請參閱[原則](#policy)和[規則](#rules)章節。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

下列 PowerShell 腳本可以用來將原則新增至您的儲存體帳戶。 `$rgname` 變數必須以您的資源群組名稱進行初始化。 `$accountName` 變數必須以您的儲存體帳戶名稱進行初始化。

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

您可以使用 Azure Resource Manager 範本來定義生命週期管理。 以下是使用生命週期管理原則部署 RA GRS GPv2 儲存體帳戶的範例範本。

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

原則是一組規則：

| 參數名稱 | 參數類型 | 備忘稿 |
|----------------|----------------|-------|
| `rules`        | 規則物件的陣列 | 原則中至少需要一個規則。 您最多可以在原則中定義100個規則。|

原則中的每個規則都有數個參數：

| 參數名稱 | 參數類型 | 備忘稿 | 必要 |
|----------------|----------------|-------|----------|
| `name`         | String |規則名稱最多可包含256個英數位元。 規則名稱會區分大小寫。  它在原則內必須是唯一的。 | 是 |
| `enabled`      | Boolean | 選擇性布林值，允許暫時停用規則。 如果未設定，預設值為 true。 | 否 | 
| `type`         | 列舉值 | 目前的有效類型為 `Lifecycle` 。 | 是 |
| `definition`   | 定義生命週期規則的物件 | 每個定義是由篩選集和動作集組成。 | 是 |

## <a name="rules"></a>規則

每個規則定義包含篩選集和動作集。 [篩選集](#rule-filters)會將規則動作限制在容器或物件名稱內的一組特定物件。 [動作集](#rule-actions)會將階層或刪除動作套用至已篩選的一組物件。

### <a name="sample-rule"></a>範例規則

下列範例規則會篩選帳戶，以針對存在於內 `container1` 且開頭為的物件執行動作 `foo` 。  

>[!NOTE]
>- 生命週期管理僅支援區塊 blob 類型。<br>
>- 生命週期管理不會影響系統容器，例如 $logs 和 $web。

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

| 篩選名稱 | 篩選類型 | 備忘稿 | 必要 |
|-------------|-------------|-------|-------------|
| blobTypes   | 預先定義的列舉值陣列。 | 目前的版本支援 `blockBlob` 。 | 是 |
| prefixMatch | 要比對之前置詞的字串陣列。 每個規則最多可以定義10個首碼。 前置詞字串必須以容器名稱開頭。 例如，如果您想要比對底下的所有 blob `https://myaccount.blob.core.windows.net/container1/foo/...` ，則 prefixMatch 為 `container1/foo` 。 | 如果您未定義 prefixMatch，此規則會套用至儲存體帳戶內的所有 blob。  | 否 |
| blobIndexMatch | 包含要比對之 Blob 索引標記索引鍵和值條件的字典值陣列。 每個規則最多可以定義10個 Blob 索引標記條件。 例如，如果您想要將規則的所有 blob 與 `Project = Contoso` 下的 `https://myaccount.blob.core.windows.net/` 進行比對，則 blobIndexMatch 為 `{"name": "Project","op": "==","value": "Contoso"}` 。 | 如果您未定義 blobIndexMatch，此規則會套用至儲存體帳戶內的所有 blob。 | 否 |

> [!NOTE]
> Blob 索引處於公開預覽狀態，並適用於**法國中部**和**法國南部**區域。 若要深入了解這項功能以及已知的問題和限制，請參閱[使用 Blob 索引 (預覽) 來管理和尋找 Azure Blob 儲存體上的資料](storage-manage-find-blobs.md)。

### <a name="rule-actions"></a>規則動作

當符合執行條件時，動作會套用至篩選的 blob。

生命週期管理支援分層及刪除 blob 和刪除 blob 快照集。 在 Blob 或 Blob 快照集上每項規則至少需定義一個動作。

| 動作        | 基底 Blob                                   | 快照式      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 支援目前在經常性儲存層的 Blob         | 不支援 |
| tierToArchive | 支援目前在經常儲存性或非經常性儲存層的 Blob | 不支援 |
| delete        | 支援                                   | 支援     |

>[!NOTE]
>如果在同一個 Blob 上定義多個動作，生命週期管理會將最便宜的動作套用至 Blob。 例如，動作 `delete` 比動作 `tierToArchive` 更便宜。 而動作 `tierToArchive` 比動作 `tierToCool` 更便宜。

執行條件是以年齡為基礎。 基底 Blob 使用上次修改時間來追蹤存在時間，而 Blob 快照集使用快照集建立時間來追蹤存在時間。

| 動作執行條件             | 條件值                          | 說明                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | 表示存在時間的整數值 (以天數為單位) | 基底 blob 動作的條件     |
| daysAfterCreationGreaterThan     | 表示存在時間的整數值 (以天數為單位) | Blob 快照集動作的條件 |

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

### <a name="archive-data-after-ingest"></a>內嵌後保存資料

有些資料在雲端維持閒置狀態，而且儲存後就很少存取。 下列生命週期原則設定為在內嵌後不久就封存資料。 這個範例會將容器內儲存體帳戶中的區塊 blob 轉換 `archivecontainer` 成封存層。 轉換的完成方式是在上次修改時間的0天后對 blob 採取行動：

> [!NOTE] 
> 建議您直接將 blob 上傳到封存層，以提高效率。 您可以使用[PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob)或[PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list)的 x 毫秒存取層標頭搭配 REST 2018-11-09 和更新版本，或我們最新的 blob 儲存體用戶端程式庫。 

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

某些資料預期會在建立後的幾天或幾個月後到期。 您可以設定生命週期管理原則，根據資料存在時間進行刪除，以便使資料過期。 下列範例顯示的原則會刪除365天之前的所有區塊 blob。

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

### <a name="delete-data-with-blob-index-tags"></a>使用 Blob 索引標記來刪除資料
只有在明確標示為刪除時，某些資料才會過期。 您可以設定生命週期管理原則，使標記為 blob 索引鍵/值屬性的資料過期。 下列範例顯示的原則會刪除標記為的所有區塊 blob `Project = Contoso` 。 若要深入了解 Blob 索引，請參閱[使用 Blob 索引 (預覽) 來管理和尋找 Azure Blob 儲存體上的資料](storage-manage-find-blobs.md)。

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
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

**我建立了新的原則，為什麼動作不會立即執行？**  
平台會每天執行一次生命週期原則。 一旦設定原則，第一次執行某些動作最多可能需要24小時的時間。  

**如果我更新現有的原則，執行動作需要多久的時間？**  
更新後的原則最多需要24小時才會生效。 原則生效後，最多可能需要24小時的時間來執行動作。 因此，原則動作最多可能需要48小時的時間才能完成。   

**我手動解除凍結封存的 blob，如何避免暫時將它移回封存層？**  
將 blob 從某個存取層移至另一個時，其上次修改時間不會變更。 如果您手動將封存的 blob 解除凍結至經常性存取層，則生命週期管理引擎會將其移回封存層。 暫時停用影響此 blob 的規則，以避免再次封存它。 當 blob 可以安全地移回封存層時，請重新啟用該規則。 如果 blob 需要永久保留在經常性存取或非經常性存取層，您也可以將它複製到另一個位置。

## <a name="next-steps"></a>後續步驟

了解如何復原意外刪除的資料：

- [Azure 儲存體 Blob 的虛刪除](../blobs/storage-blob-soft-delete.md)

瞭解如何使用 Blob 索引來管理和尋找資料：

- [使用 Blob 索引來管理和尋找 Azure Blob 儲存體上的資料](storage-manage-find-blobs.md)
