---
title: 設置客戶管理的金鑰以加密 ISEs 中靜態資料
description: 創建和管理自己的加密金鑰，以在 Azure 邏輯應用中保護整合服務環境 （ISEs） 的靜態資料
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127642"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>設置客戶管理的金鑰，以加密 Azure 邏輯應用中的整合服務環境 （ISEs） 靜態資料

Azure 邏輯應用依賴于 Azure 存儲來存儲和自動[加密靜態資料](../storage/common/storage-service-encryption.md)。 此加密可保護您的資料，並説明您履行組織安全和合規性承諾。 預設情況下，Azure 存儲使用 Microsoft 管理的金鑰來加密資料。 有關 Azure 存儲加密的工作原理的詳細資訊，請參閱[靜態資料的 Azure 存儲加密](../storage/common/storage-service-encryption.md)和[靜態 Azure 資料加密](../security/fundamentals/encryption-atrest.md)。

當您為託管邏輯應用創建[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)時，希望對 Azure 存儲使用的加密金鑰進行更多控制時，可以使用[Azure 金鑰保存庫](../key-vault/key-vault-overview.md)設置、使用和管理自己的金鑰。 此功能也稱為"自帶金鑰"（BYOK），金鑰稱為"客戶管理金鑰"。

本主題演示如何使用邏輯應用 REST API 設置和指定自己的加密金鑰，以便使用 ISE 時使用。 有關通過邏輯應用 REST API 創建 ISE 的一般步驟，請參閱[使用邏輯應用 REST API 創建整合服務環境 （ISE）。](../logic-apps/create-integration-service-environment-rest-api.md)

## <a name="considerations"></a>考量

* 此時，ISE 的客戶管理的關鍵支援僅在這些 Azure 區域可用：美國西部 2、美國東部和美國中南部

* *只有在創建 ISE 時，才能*指定客戶管理的金鑰，而不是之後。 創建 ISE 後，無法禁用此金鑰。 目前，沒有用於旋轉 ISE 客戶管理金鑰的支援。

* 要支援客戶管理的金鑰，ISE 需要啟用其[系統分配的託管標識](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 此標識允許 ISE 對其他 Azure 活動目錄 （Azure AD） 租戶中的資源進行身份驗證訪問，這樣您就不必使用憑據登錄。

* 目前，要創建支援客戶託管金鑰並啟用系統分配標識的 ISE，必須使用 HTTPS PUT 請求調用邏輯應用 REST API。

* 在發送創建 ISE 的 HTTPS PUT 請求後的*30 分鐘內*，必須[授予金鑰保存庫對 ISE 系統分配的身份的存取權限](#identity-access-to-key-vault)。 否則，ISE 創建將失敗並引發許可權錯誤。

## <a name="prerequisites"></a>Prerequisites

* [啟用 ISE 訪問](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)[的先決條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和要求與在 Azure 門戶中創建 ISE 時相同

* 啟用 **"虛刪除**"和 **"不清除"** 屬性的 Azure 金鑰保存庫

  有關啟用這些屬性的詳細資訊，請參閱[Azure 金鑰保存庫虛刪除概述](../key-vault/key-vault-ovw-soft-delete.md)，以及[使用 Azure 金鑰保存庫配置客戶管理的金鑰](../storage/common/storage-encryption-keys-portal.md)。 如果您是 Azure 金鑰保存庫的新增功能，請瞭解如何使用 Azure 門戶或使用 Azure PowerShell 命令["New-AzKeyVault"](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)[創建金鑰保存庫](../key-vault/quick-create-portal.md#create-a-vault)。

* 在金鑰保存庫中，使用以下屬性值創建的金鑰：

  | 屬性 | 值 |
  |----------|-------|
  | **索引鍵類型** | RSA |
  | **RSA 金鑰大小** | 2048 |
  | **啟用** | 是 |
  |||

  ![創建客戶託管的加密金鑰](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  有關詳細資訊，請參閱使用 Azure 金鑰保存庫或 Azure PowerShell 命令["添加 AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)"[配置客戶管理的金鑰](../storage/common/storage-encryption-keys-portal.md)。

* 一種工具，可用於使用 HTTPS PUT 請求調用邏輯應用 REST API 來創建 ISE。 例如，您可以使用[Postman](https://www.getpostman.com/downloads/)，也可以生成執行此任務的邏輯應用。

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>使用金鑰保存庫和託管標識支援創建 ISE

要通過調用邏輯應用 REST API 創建 ISE，請發出此 HTTPS PUT 請求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 邏輯應用 REST API 2019-05-01 版本要求您對 ISE 連接器發出自己的 HTTP PUT 請求。

部署通常需要兩個小時才能完成。 有時，部署可能需要長達四個小時。 要檢查部署狀態，請在 Azure 工具列上的[Azure 門戶](https://portal.azure.com)中，選擇通知圖示，該圖示將打開通知窗格。

> [!NOTE]
> 如果部署失敗或您刪除了 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 此延遲意味著您可能需要等待，然後再在另一個 ISE 中重用這些子網。
>
> 如果刪除虛擬網路，Azure 通常會在釋放子網之前最多需要兩個小時，但此操作可能需要更長時間。 
> 刪除虛擬網路時，請確保沒有資源仍然連接。 
> 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

### <a name="request-header"></a>要求標頭

在請求標頭中，包括以下屬性：

* `Content-type`：將此屬性值設置為`application/json`。

* `Authorization`：為有權訪問要使用的 Azure 訂閱或資源組的客戶設置此屬性值。

### <a name="request-body"></a>Request body

在請求正文中，通過在 ISE 定義中提供它們的資訊來支援這些附加項：

* ISE 用於訪問金鑰保存庫的系統分配的託管標識
* 要使用金鑰保存庫和客戶管理的金鑰

#### <a name="request-body-syntax"></a>要求本文語法

下面是請求正文語法，它描述了創建 ISE 時要使用的屬性：

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>請求正文示例

此示例請求正文顯示示例值：

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權

在發送 HTTP PUT 請求以創建 ISE 後*30 分鐘內*，必須為 ISE 的系統分配標識向金鑰保存庫添加訪問策略。 否則，為 ISE 創建失敗，並且您會收到許可權錯誤。 

對於此任務，可以使用 Azure PowerShell[集-AzKeyVaultAccessPolicy 命令](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)，也可以在 Azure 門戶中執行以下步驟：

1. 在[Azure 門戶](https://portal.azure.com)中，打開 Azure 金鑰保存庫。

1. 在金鑰保存庫功能表上，選擇 **"訪問策略** > **添加訪問策略**"，例如：

   ![為系統分配的託管標識添加訪問策略](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. 打開 **"添加訪問策略"** 窗格後，按照以下步驟操作：

   1. 選擇以下選項：

      | 設定 | 值 |
      |---------|--------|
      | **從範本（可選）清單配置** | 金鑰管理 |
      | **金鑰許可權** | - **金鑰管理操作**：獲取、清單 <p><p>- **加密操作**： 解包金鑰， 包裝金鑰 |
      |||

      ![選擇"金鑰管理">"金鑰許可權"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. 對於 **"選擇主體**"，請選擇 **"無選擇**"。 打開 **"主體"** 窗格後，在搜索框中查找並選擇 ISE。 完成後，**選擇"** > **添加**"。

      ![選擇 ISE 作為主體](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. 完成 **"訪問策略"** 窗格後，選擇"**保存**"。

有關詳細資訊，請參閱[使用託管標識提供金鑰保存庫身份驗證](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Key Vault](../key-vault/key-vault-overview.md)