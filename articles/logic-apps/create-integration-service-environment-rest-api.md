---
title: '使用 Logic Apps REST API 建立整合服務環境 (Ise) '
description: 使用 Logic Apps REST API 建立整合服務環境 (ISE) ，讓您可以從 (存取 Azure 虛擬網路) Vnet Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/05/2020
ms.openlocfilehash: 783431c4888a68e24cf3d2603c541c4797ea65d8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741094"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>使用 Logic Apps REST API 建立整合服務環境 (ISE)

在您的邏輯應用程式和整合帳戶需要存取 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的案例中，您可以使用 Logic Apps REST API，建立 [ (ISE) 的 *整合服務環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 。 若要深入了解 ISE，請參閱[從 Azure Logic Apps 存取 Azure 虛擬網路資源](connect-virtual-network-vnet-isolated-environment-overview.md)。

本文說明如何使用一般 Logic Apps REST API 來建立 ISE。 您也可以選擇在 ISE 上啟用 [系統指派或使用者指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) ，但只能使用 Logic Apps REST API。 此身分識別可讓您的 ISE 驗證受保護資源（例如虛擬機器和其他系統或服務）的存取權，這些資源位於或連接至 Azure 虛擬網路。 如此一來，您就不需要使用您的認證登入。

如需有關建立 ISE 之其他方式的詳細資訊，請參閱下列文章：

* [使用 Azure 入口網站建立 ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [使用範例 Azure Resource Manager 快速入門範本建立 ISE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [建立支援使用客戶管理金鑰來加密待用資料的 ISE](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>先決條件

* 與您在 Azure 入口網站中建立 ISE 時相同的[必要條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和[存取需求](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* 您想要搭配 ISE 使用的任何其他資源，以便在 ISE 定義中包含其資訊，例如： 

  * 若要啟用自我簽署憑證支援，您必須在 ISE 定義中包含該憑證的相關資訊。

  * 若要啟用使用者指派的受控識別，您需要事先建立該身分識別，並在 `objectId` `principalId` `clientId` ISE 定義中包含、和屬性及其值。 如需詳細資訊，請參閱 [Azure 入口網站中的建立使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

* 一種工具，可讓您藉由使用 HTTPS PUT 要求呼叫 Logic Apps REST API 來建立 ISE。 例如，您可以使用 [Postman](https://www.getpostman.com/downloads/)，也可以建立可執行這項工作的邏輯應用程式。

## <a name="create-the-ise"></a>建立 ISE

若要藉由呼叫 Logic Apps REST API 來建立 ISE，請提出此 HTTPS PUT 要求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 版本需要您針對 ISE 連接器建立自己的 HTTP PUT 要求。

部署通常會在兩個小時內完成。 有時候，部署需要的時間可能高達四小時。 若要檢查部署狀態，請在 [ [Azure 入口網站](https://portal.azure.com)的 Azure 工具列上選取 [通知] 圖示，以開啟 [通知] 窗格。

> [!NOTE]
> 如果部署失敗或您刪除 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 這個延遲表示，您可能必須稍等，才能在另一個的 ISE 中重複使用這些子網路。
>
> 如果您刪除虛擬網路，Azure 通常最多需要兩小時的時間，才能釋出您的子網路，但此作業可能需要更長的時間。 
> 刪除虛擬網路時，請確定沒有任何資源仍處於連線狀態。 
> 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

## <a name="request-header"></a>要求標頭

在要求標頭中，包含下列屬性：

* `Content-type`：將此屬性值設定為 `application/json` 。

* `Authorization`：將此屬性值設定為可存取您想要使用之 Azure 訂用帳戶或資源群組之客戶的持有人權杖。

<a name="request-body"></a>

## <a name="request-body"></a>Request body

在要求主體中，提供用來建立 ISE 的資源定義，包括您想要在 ISE 上啟用之其他功能的資訊，例如：

* 若要建立 ISE 以允許使用在該位置安裝的自我簽署憑證 `TrustedRoot` ，請在 `certificates` ise 定義的區段內包含該物件 `properties` ，如本文稍後所述。

  若要在現有 ISE 上啟用這項功能，您可以只傳送物件的 PATCH 要求 `certificates` 。 如需有關使用自我簽署憑證的詳細資訊，請參閱 [安全存取和資料存取，以存取其他服務和系統的輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)。

* 若要建立使用系統指派或使用者指派的受控識別的 ISE，請 `identity` 在 ise 定義中包含具有受控識別類型和其他必要資訊的物件，如本文稍後所述。

* 若要建立使用客戶管理金鑰和 Azure Key Vault 來加密待用資料的 ISE，請包含可提供 [客戶管理金鑰支援的資訊](customer-managed-keys-integration-service-environment.md)。 您只能 *在建立時* 設定客戶管理的金鑰，而不是之後設定。

### <a name="request-body-syntax"></a>要求本文語法

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
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
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
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>要求主體範例

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
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [將資源新增到整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
