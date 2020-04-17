---
title: 設定客戶管理的金鑰以加密 ISEs 中靜態資料
description: 建立與管理自己的加密金鑰,以在 Azure 邏輯應用中保護整合服務環境 (ISEs) 的靜態資料
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 7314559849f0b2019820ec3cb4fb10c684d330d6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458432"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>設定客戶管理的金鑰,以加密 Azure 邏輯應用中的整合服務環境 (ISEs) 靜態資料

Azure 邏輯應用相依 Azure 儲存與自動[加密靜態資料](../storage/common/storage-service-encryption.md)。 此加密可保護您的數據,並説明您履行組織安全和合規性承諾。 默認情況下,Azure 儲存使用Microsoft管理的密鑰來加密數據。 有關 Azure 儲存加密的工作原理的詳細資訊,請參閱[靜態資料的 Azure 儲存加密](../storage/common/storage-service-encryption.md)與[靜態 Azure 資料加密](../security/fundamentals/encryption-atrest.md)。

當您為託管邏輯應用創建[整合服務環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)時,希望對 Azure 儲存使用的加密密鑰進行更多控制時,可以使用[Azure 金鑰保管庫](../key-vault/general/overview.md)設置、使用和管理自己的密鑰。 此功能也稱為「自帶密鑰」(BYOK),密鑰稱為「客戶管理密鑰」。

本主題示範如何使用邏輯應用 REST API 設置和指定自己的加密密鑰,以便使用 ISE 時使用。 有關透過邏輯應用 REST API 創建 ISE 的一般步驟,請參閱[使用邏輯應用 REST API 創建整合服務環境 (ISE)。](../logic-apps/create-integration-service-environment-rest-api.md)

## <a name="considerations"></a>考量

* 此時,ISE 的客戶管理的關鍵支援僅在這些 Azure 區域可用:美國西部 2、美國東部和美國中南部

* *只有在創建 ISE 時,才能*指定客戶管理的密鑰,而不是之後。 建立 ISE 後,無法禁用此密鑰。 目前,沒有用於旋轉 ISE 客戶管理金鑰的支援。

* 要支援客戶管理的金鑰,ISE 需要啟用其[系統分配的託管識別](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 此標識允許 ISE 對其他 Azure 活動目錄 (Azure AD) 租戶中的資源進行身份驗證訪問,這樣您就不必使用認證登入。

* 目前,要創建支援客戶託管金鑰並啟用系統分配標識的 ISE,必須使用 HTTPS PUT 請求調用邏輯應用 REST API。

* 在傳送建立 ISE 的 HTTPS PUT 要求後*的 30 分鐘內*,必須[授予金鑰保管庫對 ISE 系統分配的身份的存取權限](#identity-access-to-key-vault)。 否則,ISE 創建將失敗並引發許可權錯誤。

## <a name="prerequisites"></a>Prerequisites

* [開啟 ISE 存取](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)[的先決條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和要求與在 Azure 門戶中建立 ISE 時相同

* 開啟 **'軟刪除**' 屬性**的**Azure 金鑰保存庫

  關於啟用這些屬性的詳細資訊,請參閱[Azure 金鑰保管庫軟刪除概述](../key-vault/general/overview-soft-delete.md),以及[使用 Azure 金鑰保存式庫設定客戶管理的金鑰](../storage/common/storage-encryption-keys-portal.md)。 如果您是 Azure 金鑰保管庫的新增功能,請瞭解如何使用 Azure 門戶或使用 Azure PowerShell 命令["New-AzKeyVault'](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)[建立金鑰保管庫](../key-vault/secrets/quick-create-portal.md#create-a-vault)。

* 在金鑰保存的金鑰:

  | 屬性 | 值 |
  |----------|-------|
  | **索引鍵類型** | RSA |
  | **RSA 金鑰大小** | 2048 |
  | **已啟用** | 是 |
  |||

  ![建立客戶託管的加密金鑰](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  關於詳細資訊,請參閱使用 Azure 金鑰保管庫或 Azure PowerShell 命令['新增 AzKeyVaultKey'](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)[設定客戶管理的金鑰](../storage/common/storage-encryption-keys-portal.md)。

* 一種工具,可用於使用 HTTPS PUT 請求呼叫邏輯應用 REST API 來建立 ISE。 例如,您可以使用[Postman](https://www.getpostman.com/downloads/),也可以生成執行此任務的邏輯應用。

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>使用金鑰保存庫和管理識別支援建立 ISE

要透過呼叫邏輯應用程式 REST API 建立 ISE,請發出此 HTTPS PUT 請求:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 邏輯應用 REST API 2019-05-01 版本要求您對 ISE 連接器發出自己的 HTTP PUT 請求。

部署通常需要兩個小時才能完成。 有時,部署可能需要長達四個小時。 要檢查部署狀態,請在 Azure 工具列上的[Azure 門戶](https://portal.azure.com)中,選擇通知圖示,該圖示將打開通知窗格。

> [!NOTE]
> 如果部署失敗或您刪除了 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 此延遲意味著您可能需要等待,然後再在另一個 ISE 中重用這些子網。
>
> 如果刪除虛擬網路,Azure 通常會在釋放子網之前最多需要兩個小時,但此操作可能需要更長時間。 
> 刪除虛擬網路時,請確保沒有資源仍然連接。 
> 請參考[虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

### <a name="request-header"></a>要求標頭

在請求標頭中,包括以下屬性:

* `Content-type`:將此屬性值設定為`application/json`。

* `Authorization`:為有權訪問要使用的 Azure 訂閱或資源組的客戶設置此屬性值。

### <a name="request-body"></a>Request body

在請求正文中,透過在 ISE 定義中提供的資訊來支援這些附加項:

* ISE 用於存取金鑰保存庫的系統分配的託管識別
* 要使用金鑰保存庫與客戶管理的金鑰

#### <a name="request-body-syntax"></a>要求本文語法

下面是請求正文語法,它描述了創建 ISE 時要使用的屬性:

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

#### <a name="request-body-example"></a>要求正文示例

此範例要求正文顯示範例值:

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

在發送 HTTP PUT 請求以創建 ISE 後*30 分鐘內*,必須為 ISE 的系統分配標識向金鑰保管庫添加訪問策略。 否則,為 ISE 創建失敗,並且您會收到許可權錯誤。 

對於此工作,可以使用 Azure PowerShell[集-AzKeyVaultAccessPolicy 命令](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy),也可以在 Azure 門戶中執行以下步驟:

1. 在[Azure 門戶](https://portal.azure.com)中,打開 Azure 密鑰保管庫。

1. 在金鑰保管庫選單上,選擇 **「存取策略** > **」,** 例如:

   ![為系統配置的託管識別加入存取原則](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. 開啟 **'新增存取策略'** 窗格後,按照以下步驟操作:

   1. 選擇以下選項:

      | 設定 | 值 |
      |---------|--------|
      | **從樣本(選擇性的)清單設定** | 金鑰管理 |
      | **金鑰權限** | - **金鑰管理操作**:取得、清單 <p><p>- **加密操作**: 解包金鑰, 包裝金鑰 |
      |||

      ![選擇"金鑰管理">"金鑰許可權"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. 對於 **「選擇主體**」,請選擇 **「無選擇**」。 開啟 **「主體」** 窗格後,在搜尋框中尋找並選擇 ISE。 完成後,**選擇「** > **添加**」。

      ![選擇 ISE 作為主體](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. 完成 **「存取策略」** 窗格後,選擇「**儲存**」。

關於詳細資訊,請參閱[使用託管識別提供金鑰保管庫身份驗證](../key-vault/general/managed-identity.md#grant-your-app-access-to-key-vault)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Key Vault](../key-vault/general/overview.md)