---
title: 使用參數建立動態藍圖
description: 瞭解靜態和動態參數以及如何使用它們創建安全和動態藍圖。
ms.date: 03/12/2019
ms.topic: conceptual
ms.openlocfilehash: 36735d71b746301819e5079aba1697b55fe5e183
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677575"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>透過參數建立動態藍圖

使用各種成品 (例如，資源群組、Resource Manager 範本、原則或角色指派) 完整定義的藍圖，可以在 Azure 中快速並一致地建立物件。 為了能夠彈性使用這些可重複使用的設計模式與容器，Azure 藍圖支援參數。 參數會在定義與指派期間建立彈性，以變更藍圖所部署成品上的屬性。

資源群組成品即為一個簡單的範例。 建立資源群組時，必須提供兩個必要的值：名稱與位置。 將資源組添加到藍圖時,如果參數不存在,則每次使用藍圖時都會定義該名稱和位置。 此種重複導致每次使用藍圖時，就會在相同資源群組中建立成品。 該資源群組內的群組會變成重複且導致衝突。

> [!NOTE]
> 對於兩個不同的藍圖來說，包含相同名稱的資源群組並不會造成任何問題。
> 如果藍圖內含的資源群組已經存在，則藍圖會繼續在該資源群組中建立相關成品。 這可能導致衝突，因為具有相同名稱與資源類型的兩個資源不能存在於一個訂用帳戶內。

這個問題的解決方式為參數。 Azure 藍圖允許您在分配到訂閱期間定義專案的每個屬性的值。 此參數讓您能夠重複使用藍圖，在單一訂用帳戶中建立資源群組與其他資源，而不會發生衝突。

## <a name="blueprint-parameters"></a>藍圖參數

透過 REST API，可以在藍圖本身建立參數。 這些參數與每個支援的成品上的參數不同。 在藍圖上建立參數時，可透過該藍圖中的成品使用該參數。 用來為資源群組命名的前置詞即為範例之一。 成品可以使用藍圖參數來建立「主要動態」參數。 由於此參數也可以在指派期間內定義，所以此模式會考量到遵守命名規則的一致性。 如需步驟，請參閱[設定靜態參數 - 藍圖層級參數](#blueprint-level-parameter)。

### <a name="using-securestring-and-secureobject-parameters"></a>使用 secureString 與 secureObject 參數

雖然 Resource Manager 範本「成品」__ 支援 **secureString** 與 **secureObject** 類型的參數，Azure 藍圖要求每個都要與 Azure Key Vault 連線。
此安全性措施可防止將祕密與藍圖儲存在一起的這種不安全做法，並鼓勵採用安全模式。 Azure 藍圖支援此種安全性措施，並在 Resource Manager 範本「成品」__ 中偵測是否包含任何一個安全參數。 服務接著會在指派期間，針對每個偵測到的安全參數提示下列 Key Vault 屬性：

- Key Vault 資源識別碼
- Key Vault 祕密名稱
- Key Vault 祕密版本

如果藍圖分配使用**系統分配的託管標識**,則引用的密鑰保管庫_必須_存在於藍圖定義分配給的同一訂閱中。

如果藍圖分配使用**使用者分配的託管標識**,則引用的密鑰保管庫_可能_存在於集中式訂閱中。 在分配藍圖之前,必須授予託管標識在密鑰保管庫上的適當許可權。

> [!IMPORTANT]
> 在這兩種情況下,金鑰保管庫都必須具有「**存取策略**」 頁上設定**的樣本部署的 Azure 資源管理員存取權限**。 如需如何啟用此功能的相關指示，請參閱 [Key Vault - 啟用範本部署](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment)。

如需 Azure Key Vault 的詳細資訊，請參閱 [Azure Key Vault 概觀](../../../key-vault/key-vault-overview.md)。

## <a name="parameter-types"></a>參數類型

### <a name="static-parameters"></a>靜態參數

藍圖定義中所定義的參數值稱為**靜態參數**，因為每次使用藍圖時都會使用該靜態值來部署成品。 在資源群組範例中，雖然這對資源群組名稱不具任何意義，但它對位置來說可能是有意義的。 接著，每個藍圖指派都會在相同的位置建立資源群組，而無論指派期間是否會呼叫它。 此彈性可讓您選擇要視需要定義哪些內容，以及可在指派期間變更哪些內容。

#### <a name="setting-static-parameters-in-the-portal"></a>在入口網站中設定靜態參數

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 在頁面左側選取 [藍圖定義]  。

1. 按下現有藍圖,然後按下 **「編輯藍圖**」或按下「**創建藍圖」** 並填寫 **「基礎知識」** 選項卡上的資訊。

1. 按一下 [下一步: 成品]****，或按一下 [成品]**** 索引標籤。

1. 新增到具有參數選項之藍圖的成品會在 [參數]**** 欄中顯示 [已填入 X 個參數 (共 Y 個)]****。 按一下成品列來編輯成品參數。

   ![藍圖定義的藍圖參數](../media/parameters/parameter-column.png)

1. [編輯成品]**** 頁面會顯示適用於所按下之成品的值選項。 成品中的每個參數都有標題、值方塊與核取方塊。 將此方塊設定為未選取狀態，以使其成為**靜態參數**。 在下列範例中，只有「位置」__ 是**靜態參數**，因為它已取消選取且 [資源群組名稱]__ 已選取。

   ![藍圖工件上的靜態參數藍圖](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>從 REST API 設定靜態參數

在每個 REST API URI 中有一些變數，需要您以自己的值取代它們：

- `{YourMG}` - 以您的管理群組名稱取代
- `{subscriptionId}` - 以您的訂用帳戶識別碼取代

##### <a name="blueprint-level-parameter"></a>藍圖層級參數

透過 REST API 建立藍圖時，就能夠建立[藍圖參數](#blueprint-parameters)。 若要這樣做，請使用下列 REST API URI 與主體格式：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

建立藍圖層級參數之後，就能在已新增至該藍圖的成品中使用。
下列 REST API 範例會在藍圖上建立角色指派成品，並使用藍圖層級參數。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

在此範例中，**principalIds** 屬性會利用 `[parameters('owners')]` 的值來使用**擁有者**藍圖層級參數。 使用藍圖層級參數在成品上設定參數，仍然是一個**靜態參數**範例。 藍圖層級參數無法在藍圖指派期間設定，而且在每個指派上都會是相同的值。

##### <a name="artifact-level-parameter"></a>成品層級參數

在成品上建立**靜態參數**的方式很類似，但會採用直接值，而非使用 `parameters()` 函式。 下列範例會建立兩個靜態參數，亦即 **tagName** 與 **tagValue**。 每個值都是直接提供的，不會使用函式呼叫。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>動態參數

靜態參數的相反是**動態參數**。 此參數並未定義於藍圖上，而是改為在藍圖的每個指派期間內定義。 在資源群組範例中，**動態參數**適合用於資源群組名稱。 它可為藍圖的每個指派提供不同的名稱。 有關藍圖函數的清單,請參閱[藍圖函數](../reference/blueprint-functions.md)引用。

#### <a name="setting-dynamic-parameters-in-the-portal"></a>在入口網站中設定動態參數

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 在頁面左側選取 [藍圖定義]  。

1. 以滑鼠右鍵按一下您想要指派的藍圖。 選擇 **「分配藍圖**」或單擊要分配的藍圖,然後單擊「**分配藍圖」** 按鈕。

1. 在 **「分配藍圖」** 頁上,查找 **「工件」參數**部分。 至少具有一個**動態參數**的每個成品都會顯示成品與設定選項。 指派藍圖之前，請先為參數提供所需的值。 在下列範例中，「名稱」__ 是必須先定義才能完成藍圖指派的**動態參數**。

   ![藍圖分配期間的藍圖動態參數](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>從 REST API 設定動態參數

在指派期間設定**動態參數**，可透過直接輸入值來完成。 提供的值不是使用函數(如[參數(),](../reference/blueprint-functions.md#parameters)而是適當的字串。 資源群組的成品是使用「範本名稱」與**名稱**和**位置**屬性來定義。 內含成品的所有其他參數都定義在 **parameters** 之下，並具備 **\<name\>** 和 **value** 金鑰組。 如果已針對指派期間未提供的動態參數設定藍圖，指派將會失敗。

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>後續步驟

- 請參閱[藍圖函數](../reference/blueprint-functions.md)清單。
- 瞭解[藍圖生命週期](lifecycle.md)。
- 了解如何自訂[藍圖排序順序](sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](resource-locking.md)。
- 瞭解如何[更新現有工作](../how-to/update-existing-assignments.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。