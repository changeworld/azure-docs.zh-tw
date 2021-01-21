---
title: 設定客戶管理的金鑰，以加密 Ise 中的靜止資料
description: 建立及管理您自己的加密金鑰，以保護 (Ise) Azure Logic Apps 中的整合服務環境的待用資料
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: d31fbd813f0c5d63ee9eddbff5b299209618626b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629669"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>設定客戶管理的金鑰，以在 Azure Logic Apps 中 (Ise) 為整合服務環境加密待用資料

Azure Logic Apps 依賴 Azure 儲存體來儲存和自動 [加密待用資料](../storage/common/storage-service-encryption.md)。 此加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 根據預設，Azure 儲存體會使用 Microsoft 管理的金鑰來加密您的資料。 如需 Azure 儲存體加密如何運作的詳細資訊，請參閱 [Azure 儲存體待用資料加密](../storage/common/storage-service-encryption.md) 和 [Azure 資料靜態](../security/fundamentals/encryption-atrest.md)加密。

當您建立 [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 來裝載您的邏輯應用程式，而且您想要更充分掌控 Azure 儲存體所使用的加密金鑰時，可以使用 [Azure Key Vault](../key-vault/general/overview.md)來設定、使用和管理您自己的金鑰。 這項功能稱為「攜帶您自己的金鑰」 (BYOK) ，而您的金鑰稱為「客戶管理的金鑰」。 利用這項功能，Azure 儲存體可針對金鑰 [使用平臺管理的金鑰，自動啟用雙重加密或 *基礎結構加密*](../security/fundamentals/double-encryption.md) 。 若要深入瞭解，請參閱 [使用基礎結構加密的雙向加密資料](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)。

本主題說明如何設定和指定您自己的加密金鑰，以在使用 Logic Apps REST API 建立 ISE 時使用。 如需透過 Logic Apps REST API 建立 ISE 的一般步驟，請參閱 [使用 Logic Apps REST API 建立整合服務環境 (ISE) ](../logic-apps/create-integration-service-environment-rest-api.md)。

## <a name="considerations"></a>考量

* 目前，客戶管理的 ISE 金鑰支援僅適用于下列 Azure 區域：美國西部2、美國東部和美國中南部

* 您 *只能在建立 ISE 時* 指定客戶管理的金鑰，而不能在之後指定。 您無法在 ISE 建立之後停用此金鑰。 目前不支援為 ISE 輪替客戶管理的金鑰。

* 若要支援客戶管理的金鑰，您的 ISE 要求您啟用 [系統指派或使用者指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)。 此身分識別可讓您的 ISE 驗證受保護資源（例如虛擬機器和其他系統或服務）的存取權，這些資源位於或連接至 Azure 虛擬網路。 如此一來，您就不需要使用您的認證登入。

* 目前，若要建立支援客戶管理金鑰且已啟用受控識別類型的 ISE，您必須使用 HTTPS PUT 要求來呼叫 Logic Apps REST API。

* 您必須將 [金鑰保存庫的存取權授與 ISE 的受控識別](#identity-access-to-key-vault)，但時間取決於您所使用的受控識別。

  * **系統指派的受控識別**：在您傳送建立 ISE 的 HTTPS PUT 要求 *之後的30分鐘* 內，您必須將 [金鑰保存庫的存取權授與 ise 的受控識別](#identity-access-to-key-vault)。 否則，ISE 建立會失敗，而且您會收到許可權錯誤。

  * **使用者指派的受控識別**：在您傳送建立 ISE 的 HTTPS PUT 要求之前，請先將 [金鑰保存庫的存取權授與 ise 的受控識別](#identity-access-to-key-vault)。

## <a name="prerequisites"></a>必要條件

* [啟用 ise 存取權](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)的相同[必要條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和需求，如同當您在 Azure 入口網站中建立 ise 時

* 已啟用虛 **刪除** 和 **未清除** 屬性的 Azure key vault

  如需有關啟用這些屬性的詳細資訊，請參閱 [Azure Key Vault 虛刪除總覽](../key-vault/general/soft-delete-overview.md) 和 [使用 Azure Key Vault 設定客戶管理的金鑰](../storage/common/customer-managed-keys-configure-key-vault.md)。 如果您不熟悉 [Azure Key Vault](../key-vault/general/overview.md)，請瞭解如何使用 [Azure 入口網站](../key-vault/general/quick-create-portal.md)、 [Azure CLI](../key-vault/general/quick-create-cli.md)或 [Azure PowerShell](../key-vault/general/quick-create-powershell.md)來建立金鑰保存庫。

* 在您的金鑰保存庫中，使用這些屬性值建立的金鑰：

  | 屬性 | 值 |
  |----------|-------|
  | **索引鍵類型** | RSA |
  | **RSA 金鑰大小** | 2048 |
  | **Enabled** | Yes |
  |||

  ![建立客戶管理的加密金鑰](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  如需詳細資訊，請參閱 [使用 Azure Key Vault 設定客戶管理的金鑰](../storage/common/customer-managed-keys-configure-key-vault.md) 或 Azure PowerShell 命令 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。

* 一種工具，可讓您藉由使用 HTTPS PUT 要求呼叫 Logic Apps REST API 來建立 ISE。 例如，您可以使用 [Postman](https://www.getpostman.com/downloads/)，也可以建立可執行這項工作的邏輯應用程式。

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>使用 key vault 和受控識別支援建立 ISE

若要藉由呼叫 Logic Apps REST API 來建立 ISE，請提出此 HTTPS PUT 要求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 版要求您針對 ISE 連接器建立自己的 HTTPS PUT 要求。

部署通常會在兩個小時內完成。 有時候，部署需要的時間可能高達四小時。 若要檢查部署狀態，請在 [ [Azure 入口網站](https://portal.azure.com)的 Azure 工具列上選取 [通知] 圖示，以開啟 [通知] 窗格。

> [!NOTE]
> 如果部署失敗或您刪除 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 這個延遲表示，您可能必須稍等，才能在另一個的 ISE 中重複使用這些子網路。
>
> 如果您刪除虛擬網路，Azure 通常最多需要兩小時的時間，才能釋出您的子網路，但此作業可能需要更長的時間。 
> 刪除虛擬網路時，請確定沒有任何資源仍處於連線狀態。 
> 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

### <a name="request-header"></a>要求標頭

在要求標頭中，包含下列屬性：

* `Content-type`：將此屬性值設定為 `application/json` 。

* `Authorization`：將此屬性值設定為可存取您想要使用之 Azure 訂用帳戶或資源群組之客戶的持有人權杖。

### <a name="request-body"></a>要求本文

在要求主體中，藉由在 ISE 定義中提供這些額外專案的資訊來啟用支援：

* 您 ISE 用來存取金鑰保存庫的受控識別
* 您要使用的金鑰保存庫和客戶管理的金鑰

#### <a name="request-body-syntax"></a>要求本文語法

以下是要求本文語法，其中描述當您建立 ISE 時要使用的屬性：

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
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

#### <a name="request-body-example"></a>要求主體範例

此範例要求主體會顯示範例值：

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
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

雖然時間與您使用的受控識別不同，但您必須將 [金鑰保存庫的存取權授與 ISE 的受控識別](#identity-access-to-key-vault)。

* **系統指派的受控識別**：在您傳送建立 ISE 的 HTTPS PUT 要求 *之後的30分鐘* 內，您必須為 ISE 系統指派的受控識別，將存取原則新增至您的金鑰保存庫。 否則，您 ISE 的建立會失敗，而且您會收到許可權錯誤。

* **使用者指派的受控識別**：在您傳送建立 ISE 的 HTTPS PUT 要求之前，請先針對 ISE 的使用者指派受控識別，將存取原則新增至您的金鑰保存庫。

針對這項工作，您可以使用 Azure PowerShell [>set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 命令，也可以在 Azure 入口網站中遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的 Azure 金鑰保存庫。

1. 在 [金鑰保存庫] 功能表上，選取 [**存取** 原則  >  **新增存取原則**]，例如：

   ![新增系統指派的受控識別的存取原則](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. [ **新增存取原則** ] 窗格開啟之後，請遵循下列步驟：

   1. 選取下列選項：

      | 設定 | 值 |
      |---------|--------|
      | **從範本設定 (選用) 清單** | 金鑰管理 |
      | **金鑰許可權** | - **金鑰管理作業**： Get、List <p><p>- **密碼編譯作業**：解除包裝金鑰、包裝金鑰 |
      |||

      ![選取 [金鑰管理] > [金鑰許可權]](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. 針對 [ **選取主體**]，選取 [ **未** 選取]。 在 [ **主體** ] 窗格開啟之後，在 [搜尋] 方塊中尋找並選取您的 ISE。 當您完成時，請選擇 [**選取**  >  **新增**]。

      ![選取您的 ISE 以作為主體](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. 當您完成 [ **存取原則** ] 窗格時，請選取 [ **儲存**]。

如需詳細資訊，請參閱 [如何驗證 Key Vault](../key-vault/general/authentication.md) 並 [指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-portal.md)。

## <a name="next-steps"></a>下一步

* 深入了解 [Azure Key Vault](../key-vault/general/overview.md)
