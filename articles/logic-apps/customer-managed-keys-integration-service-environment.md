---
title: 設定客戶管理的金鑰，以加密 Ise 中的待用資料
description: 建立及管理您自己的加密金鑰，以保護 Azure Logic Apps 中整合服務環境（Ise）的待用資料
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127642"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>設定客戶管理的金鑰，以加密 Azure Logic Apps 中整合服務環境（Ise）的待用資料

Azure Logic Apps 依賴 Azure 儲存體來儲存及自動[加密待用資料](../storage/common/storage-service-encryption.md)。 此加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 根據預設，Azure 儲存體會使用 Microsoft 管理的金鑰來加密您的資料。 如需 Azure 儲存體加密運作方式的詳細資訊，請參閱待用[資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)和待用[Azure 資料加密](../security/fundamentals/encryption-atrest.md)。

當您建立[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)來裝載您的邏輯應用程式時，如果您想要更充分掌控 Azure 儲存體所使用的加密金鑰，您可以使用[Azure Key Vault](../key-vault/key-vault-overview.md)來設定、使用及管理您自己的金鑰。 這項功能也稱為「攜帶您自己的金鑰」（BYOK），而您的金鑰稱為「客戶管理的金鑰」。

本主題說明如何設定並指定您自己的加密金鑰，以在使用 Logic Apps REST API 建立 ISE 時使用。 如需透過 Logic Apps REST API 建立 ISE 的一般步驟，請參閱[使用 Logic Apps REST API 建立整合服務環境（ISE）](../logic-apps/create-integration-service-environment-rest-api.md)。

## <a name="considerations"></a>考量

* 目前，只有在下列 Azure 區域中才可使用適用于 ISE 的客戶管理金鑰支援：美國西部2、美國東部和美國中南部

* 只有在建立您的*ISE 時，才*可以指定客戶管理的金鑰，而不是之後。 建立 ISE 之後，即無法停用此金鑰。 目前不支援為 ISE 輪替客戶管理的金鑰。

* 為了支援客戶管理的金鑰，您的 ISE 要求必須啟用其[系統指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 此身分識別可讓 ISE 驗證其他 Azure Active Directory （Azure AD）租使用者中資源的存取權，讓您不需要使用您的認證登入。

* 目前，若要建立支援客戶管理金鑰的 ISE，並啟用其系統指派的身分識別，您必須使用 HTTPS PUT 要求呼叫 Logic Apps REST API。

* 在您傳送建立 ISE 的 HTTPS PUT 要求之後的*30 分鐘*內，您必須將[金鑰保存庫的存取權授與您 ise 系統指派](#identity-access-to-key-vault)的身分識別。 否則，ISE 建立會失敗，並擲回許可權錯誤。

## <a name="prerequisites"></a>Prerequisites

* 相同的[必要條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和需求，可讓您在 Azure 入口網站中建立 ise 時，[啟用 ise 的存取權。](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* 已啟用「虛**刪除**」和「**不要清除**」屬性的 Azure 金鑰保存庫

  如需有關啟用這些屬性的詳細資訊，請參閱[Azure Key Vault 虛刪除總覽](../key-vault/key-vault-ovw-soft-delete.md)和[使用 Azure Key Vault 設定客戶管理的金鑰](../storage/common/storage-encryption-keys-portal.md)。 如果您不熟悉 Azure Key Vault，請瞭解[如何使用 Azure 入口網站建立金鑰保存庫](../key-vault/quick-create-portal.md#create-a-vault)，或使用 Azure PowerShell 命令[AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)。

* 在您的金鑰保存庫中，使用這些屬性值建立的金鑰：

  | 屬性 | 值 |
  |----------|-------|
  | **索引鍵類型** | RSA |
  | **RSA 金鑰大小** | 2048 |
  | **已啟用** | 是 |
  |||

  ![建立客戶管理的加密金鑰](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  如需詳細資訊，請參閱使用 Azure Key Vault 或 Azure PowerShell 命令來[設定客戶管理的金鑰](../storage/common/storage-encryption-keys-portal.md) [AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)。

* 一種工具，可讓您使用 HTTPS PUT 要求呼叫 Logic Apps REST API 來建立 ISE。 例如，您可以使用[Postman](https://www.getpostman.com/downloads/)，也可以建立執行這項工作的邏輯應用程式。

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>建立具有 key vault 和受控識別支援的 ISE

若要藉由呼叫 Logic Apps REST API 來建立您的 ISE，請提出此 HTTPS PUT 要求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 版要求您自行建立 ISE 連接器的 HTTP PUT 要求。

部署通常會在兩個小時內完成。 有時候，部署可能需要多達四小時。 若要檢查部署狀態，請在  [Azure 入口網站](https://portal.azure.com)的 Azure 工具列上，選取 [通知] 圖示，這會開啟 [通知] 窗格。

> [!NOTE]
> 如果部署失敗或刪除您的 ISE，Azure 可能需要一小時的時間，才能釋出您的子網。 這種延遲表示您可能必須等待，才能在另一個 ISE 中重複使用這些子網。
>
> 如果您刪除虛擬網路，Azure 通常需要兩小時的時間，才能釋出您的子網，但此作業可能需要較長的時間。 
> 刪除虛擬網路時，請確定沒有任何資源仍處於線上狀態。 
> 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

### <a name="request-header"></a>要求標頭

在要求標頭中，包含下列屬性：

* `Content-type`：將此屬性值設定為 `application/json`。

* `Authorization`：將此屬性值設定為可存取您想要使用之 Azure 訂用帳戶或資源群組之客戶的持有人權杖。

### <a name="request-body"></a>Request body

在要求本文中，藉由在您的 ISE 定義中提供資訊，以啟用這些額外專案的支援：

* 您的 ISE 用來存取金鑰保存庫的系統指派受控識別
* 您的金鑰保存庫和您想要使用的客戶管理金鑰

#### <a name="request-body-syntax"></a>要求本文語法

以下是要求本文語法，其中描述建立 ISE 時要使用的屬性：

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

#### <a name="request-body-example"></a>要求本文範例

這個範例要求主體會顯示範例值：

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

在您傳送 HTTP PUT 要求以建立 ISE 後的*30 分鐘*內，您必須針對 ISE 系統指派的身分識別，將存取原則新增至您的金鑰保存庫。 否則，為您的 ISE 建立作業會失敗，而且會出現許可權錯誤。 

針對這項工作，您可以使用 Azure PowerShell [set-azkeyvaultaccesspolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)命令，也可以依照 Azure 入口網站中的下列步驟執行：

1. 在[Azure 入口網站](https://portal.azure.com)中，開啟您的 Azure 金鑰保存庫。

1. 在 [金鑰保存庫] 功能表上，選取 [**存取原則**] > [**新增存取原則**]，例如：

   ![為系統指派的受控識別新增存取原則](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. [**新增存取原則**] 窗格開啟之後，請依照下列步驟執行：

   1. 選取下列選項：

      | 設定 | 值 |
      |---------|--------|
      | **從範本設定（選擇性）清單** | 金鑰管理 |
      | **金鑰許可權** | - **金鑰管理作業**：取得、列出 <p><p>- **密碼編譯作業**：解除包裝金鑰，換行金鑰 |
      |||

      ![選取 [金鑰管理] > [金鑰許可權]](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. 針對 [**選取主體**]，選取 [**未選取**]。 **主體**窗格開啟之後，在搜尋方塊中，尋找並選取您的 ISE。 當您完成時，請選擇 [**選取** > **新增**]。

      ![選取您的 ISE 以作為主體](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. 當您完成 [**存取原則**] 窗格之後，請選取 [**儲存**]。

如需詳細資訊，請參閱[使用受控識別提供 Key Vault 驗證](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Key Vault](../key-vault/key-vault-overview.md)