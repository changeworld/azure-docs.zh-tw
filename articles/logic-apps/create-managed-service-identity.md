---
title: 使用受控識別進行驗證
description: 存取受 Azure Active Directory 保護的資源，而不需使用受控識別登入認證或秘密
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: 9ac8a23569d9a85787768419a0377967026e9bd9
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251559"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的受控識別驗證及存取 Azure 資源

若要輕鬆存取 Azure Active Directory (所保護的其他資源 Azure AD) 並驗證您的身分識別，您的邏輯應用程式可以使用 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) (先前的受控服務身分識別或 MSI) ，而不是認證、秘密或 Azure AD 權杖。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要管理秘密或直接使用 Azure AD 權杖。

Azure Logic Apps 支援 [*系統指派的*](../active-directory/managed-identities-azure-resources/overview.md)和 [*使用者指派的*](../active-directory/managed-identities-azure-resources/overview.md)受控識別。 您的邏輯應用程式或個別連接可以使用系統指派的身分識別或 *單一* 使用者指派的身分識別，您可以在一組邏輯應用程式之間共用，但不能同時在兩者之間共用。

## <a name="where-can-logic-apps-use-managed-identities"></a>邏輯應用程式可以在何處使用受控識別？

目前，只有 [特定的內建觸發程式和動作](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) ，以及支援 Azure AD OAuth 的 [特定受控連接器](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) 可以使用受控識別進行驗證。 例如，以下是一個選取專案：

**內建觸發程式和動作**

* Azure API 管理
* Azure App Service
* Azure Functions
* HTTP
* HTTP + Webhook

**受控連接器**

* Azure 自動化
* Azure Event Grid
* Azure Key Vault
* Azure 監視器記錄
* Azure Resource Manager
* 搭配使用 HTTP 與 Azure AD 

受控連接器的支援目前為預覽狀態。 如需最新清單，請參閱 [觸發程式的驗證類型和支援驗證的動作](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)。

本文說明如何為您的邏輯應用程式設定這兩種受控識別。 如需詳細資訊，請參閱下列主題：

* [支援受控識別的觸發和動作](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [邏輯應用程式的受控識別限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [使用受控識別支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 受控識別與您需要指派存取權的目標 Azure 資源必須使用相同的 Azure 訂用帳戶。

* 若要為受控識別授與 Azure 資源的存取權，您必須將角色新增至該身分識別的目標資源。 若要新增角色，您需要 [Azure AD 系統管理員權限](../active-directory/roles/permissions-reference.md)才能將角色指派給對應 Azure AD 租用戶中的身分識別。

* 您想要存取的目標 Azure 資源。 在此資源上，您將新增受控識別的角色，以協助邏輯應用程式驗證對目標資源的存取。

* 您要在其中使用 [觸發程式或支援受控](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)識別之動作的邏輯應用程式。

## <a name="enable-managed-identity"></a>啟用受控識別

若要設定您想要使用的受控識別，請遵循該身分識別的連結：

* [系統指派的身分識別](#system-assigned)
* [使用者指派的身分識別](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>啟用系統指派的身分識別

不同於使用者指派的受控識別，您不需要手動建立系統指派的身分識別。 若要為您的邏輯應用程式設定系統指派的身分識別，以下是您可以使用的選項：

* [Azure 入口網站](#azure-portal-system-logic-app)
* [Azure 資源管理員範本](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 入口網站中啟用系統指派的身分識別

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [身分識別]。 選取 [系統指派] > [開啟] > [儲存]。 Azure 提示您確認時，請選取 [是]。

   ![啟用系統指派的身分識別](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > 如果您收到錯誤，表示您只能有一個受控識別，則您的邏輯應用程式已與使用者指派的身分識別建立關聯。 在您可以新增系統指派的身分識別之前，您必須先從邏輯應用程式 *移除* 使用者指派的身分識別。

   您的邏輯應用程式現在可以使用系統指派的身分識別，該身分識別會在 Azure AD 中註冊，並以物件識別碼表示。

   ![系統指派身分識別的物件識別碼](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **物件識別碼** | <*identity-resource-ID*> | 代表 Azure AD 租用戶中邏輯應用程式的系統指派身分識別的全域唯一識別碼 (GUID) |
   ||||

1. 現在跟隨本主題稍後的步驟，[將該身分識別存取權授與資源](#access-other-resources)。

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中啟用系統指派的身分識別

若要將建立並部署邏輯應用程式等資源的作業自動化，您可以使用 [Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 若要在範本中為您的邏輯應用程式啟用系統指派的受控識別，請將 `identity` 物件和 `type` 子屬性新增至範本中邏輯應用程式的資源定義，例如：

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

當 Azure 建立您的邏輯應用程式資源定義時，`identity` 物件會取得這些額外的屬性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 屬性 (JSON) | 值 | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | 受控識別之服務主體物件的全域唯一識別碼 (GUID)，代表 Azure AD 租用戶中的邏輯應用程式。 此 GUID 有時候會顯示為「物件識別碼」或 `objectID`。 |
| `tenantId` | <*Azure-AD-tenant-ID*> | 代表邏輯應用程式現在已是其成員的 Azure AD 租用戶的全域唯一識別碼 (GUID)。 在 Azure AD 租用戶中，服務主體會有與邏輯應用程式執行個體相同的名稱。 |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>啟用使用者指派的身分識別

若要為您的邏輯應用程式設定使用者指派的受控識別，您必須先將該身分識別建立為個別的獨立 Azure 資源。 以下是您可以使用的選項︰

* [Azure 入口網站](#azure-portal-user-identity)
* [Azure 資源管理員範本](#template-user-identity)
* Azure PowerShell
  * [Create user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [新增角色指派](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Create user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [新增角色指派](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [Create user-assigned identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [新增角色指派](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>在 Azure 入口網站中建立使用者指派的識別

1. 在 [Azure 入口網站](https://portal.azure.com)任何頁面的 [搜尋] 方塊中，輸入 `managed identities`，然後選取 [受控識別]。

   ![尋找並選取 [受控識別]](./media/create-managed-service-identity/find-select-managed-identities.png)

1. 在 [受控識別] 底下，選取 [新增]。

   ![新增受控識別](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 提供受控識別的相關資訊，然後選取 [ **審核 + 建立**]，例如：

   ![建立使用者指派的受控識別](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | 屬性 | 必要 | 值 | 說明 |
   |----------|----------|-------|-------------|
   | **訂用帳戶** | 是 | <*Azure-subscription-name*> | 要使用的 Azure 訂用帳戶的名稱 |
   | **資源群組** | 是 | <*Azure-resource-group-name*> | 輸入要使用的資源名稱。 建立新的群組，或選取現有的群組。 這個範例會建立名為的新群組 `fabrikam-managed-identities-RG` 。 |
   | **區域** | 是 | <*Azure-region*> | 用來存放資源相關資訊的 Azure 區域。 此範例使用「美國西部」。 |
   | **名稱** | 是 | <*user-assigned-identity-name*> | 要提供給使用者指派身分識別的名稱。 此範例會使用 `Fabrikam-user-assigned-identity`。 |
   |||||

   驗證這些詳細資料之後，Azure 會建立您的受控身分識別。 現在您可以將使用者指派的身分識別新增至邏輯應用程式。 您無法將多個使用者指派的身分識別新增至您的邏輯應用程式。

1. 在 Azure 入口網站的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [身分識別]，然後選取 [使用者指派] > [新增]。

   ![新增使用者指派的受控識別](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. 在 [新增使用者指派的受控識別] 窗格上，從 [訂用帳戶] 清單中選取您的Azure 訂用帳戶 (如果尚未選取)。 從顯示該訂用帳戶中 *全部* 受控識別的清單中，尋找並選取您想要的使用者指派身分識別。 若要篩選清單，請在 [使用者指派的受控識別] 搜尋方塊中，輸入身分識別或資源群組的名稱。 完成後，選取 [新增]。

   ![選取要使用的使用者指派身分識別](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > 如果您收到錯誤，表示您只能有一個受控識別，則您的邏輯應用程式已與系統指派的身分識別建立關聯。 在您新增使用者指派的身分識別之前，必須先在邏輯應用程式上停用系統指派的身分識別。

   您的邏輯應用程式現在與使用者指派的受控識別相關聯。

   ![與使用者指派的身分識別產生關聯](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 現在跟隨本主題稍後的步驟，[將該身分識別存取權授與資源](#access-other-resources)。

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中建立使用者指派的身分識別

若要自動建立並部署邏輯應用程式之類的 Azure 資源，您可以使用 [Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，這可支援[使用者指派身分識別的驗證](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)。 在範本的 `resources` 區段中，邏輯應用程式的資源定義需要下列項目：

* `type` 屬性設定為 `UserAssigned` 的 `identity` 物件

* 子 `userAssignedIdentities` 物件，指定使用者指派的資源和名稱。

這個範例會顯示 HTTP PUT 要求的邏輯應用程式資源定義，並包含非參數化的 `identity` 物件。 PUT 要求和後續 GET 作業的回應也會有此 `identity` 物件：

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

如果您的範本也包含受控識別的資源定義，您可以將 `identity` 物件參數化。 這個範例會顯示子 `userAssignedIdentities` 物件如何參考您在範本的 `variables` 區段中定義的 `userAssignedIdentity` 變數。 此變數會參考使用者指派的身分識別所用的資源識別碼。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>授與資源的身分識別存取權

在您可以使用邏輯應用程式的受控識別進行驗證之前，請先在您打算使用該身分識別的 Azure 資源上，設定該身分識別的存取權。 若要完成這項工作，請在目標 Azure 資源上將適當的角色指派給該身分識別。 以下是您可以使用的選項︰

* [Azure 入口網站](#azure-portal-assign-access)
* [Azure Resource Manager 範本](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) -如需詳細資訊，請參閱[使用 Azure RBAC 新增角色指派和 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)。
* Azure CLI ([az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true#az-role-assignment-create)) -如需詳細資訊，請參閱[使用 Azure RBAC 新增角色指派和 Azure CLI](../role-based-access-control/role-assignments-cli.md)。
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>透過 Azure 入口網站指派存取權

在您要讓受控識別具有存取權的目標 Azure 資源上，授與該身分識別以角色為基礎的目標資源存取權。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您想要受控識別可存取的 Azure 資源。

1. 從資源的功能表中，選取 [存取控制 (IAM)] > [角色指派]，您可以在其中檢查該資源目前的角色指派。 在工具列上，選取 [+新增] > [新增角色指派]。

   ![選取 [新增] > [新增角色指派]](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 如果 [新增角色指派] 選項已停用，很可能表示您沒有權限。 如需可讓您管理資源角色的權限的詳細資訊，請參閱 [Azure Active Directory 中的系統管理員角色權限](../active-directory/roles/permissions-reference.md)。

1. 在 [新增角色指派] 中，選取能為您的身分識別提供目標資源必要存取權的 **角色**。

   在本主題的範例中，您的身分識別需要 [可存取 Azure 儲存體容器中 blob 的角色](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)，因此請選取受控識別的 **儲存體 blob 資料參與者** 角色。

   ![選取「儲存體 Blob 資料參與者」角色](./media/create-managed-service-identity/select-role-for-identity.png)

1. 針對受控識別，請遵循下列步驟：

   * **系統指派的身分識別**

     1. 在 [存取權指派對象] 方塊中，選取 [邏輯應用程式]。 當 [訂用帳戶] 屬性出現時，請選取與您的身分識別相關聯的 Azure 訂用帳戶。

        ![選取系統指派的身分識別所用的存取權](./media/create-managed-service-identity/assign-access-system.png)

     1. 在 [選取] 方塊底下，從清單中選取您的邏輯應用程式。 如果清單太長，請使用 [選取] 方塊來篩選清單。

        ![選取系統指派的身分識別所用的邏輯應用程式](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **使用者指派的身分識別**

     1. 在 [存取權指派對象] 方塊中，選取 [使用者指派的受控識別]。 當 [訂用帳戶] 屬性出現時，請選取與您的身分識別相關聯的 Azure 訂用帳戶。

        ![選取使用者指派的身分識別所用的存取權](./media/create-managed-service-identity/assign-access-user.png)

     1. 在 [選取] 方塊中，從清單中選取您的身分識別。 如果清單太長，請使用 [選取] 方塊來篩選清單。

        ![選取使用者指派的身分識別](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. 完成時，選取 [儲存]。

   目標資源的角色指派清單現在會顯示所選取的受控識別和角色。 這個範例會示範如何將系統指派的身分識別用於一個邏輯應用程式，並將使用者指派的身分識別用於另一個邏輯應用程式群組。

   ![已將受控識別和角色新增至目標資源](./media/create-managed-service-identity/added-roles-for-identities.png)

   如需詳細資訊，請參閱[使用 Azure 入口網站為受控識別指派對資源的存取權](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。

1. 在支援受控識別的觸發或動作中，現在遵循[使用身分識別驗證存取權的步驟](#authenticate-access-with-identity)。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用受控識別驗證存取權

當您[啟用邏輯應用程式的受控識別](#azure-portal-system-logic-app)並[授與目標資源或實體的身分識別存取權](#access-other-resources)時，您可以在[支援受控識別的觸發和動作](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)中使用該身分識別。

> [!IMPORTANT]
> 如果您有想要使用系統指派身分識別的Azure 函式，請先[啟用 Azure Function 的驗證功能](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions)。

這些步驟示範如何透過 Azure 入口網站，使用受控識別搭配觸發或動作。 若要在觸發或動作的基礎 JSON 定義中指定受控識別，請參閱[受控識別驗證](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 如果您尚未這麼做，請新增[支援受控識別的觸發或動作](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)。

   > [!NOTE]
   > 並非所有的觸發和動作都支援讓您新增驗證類型。 如需詳細資訊，請參閱 [觸發程式的驗證類型和支援驗證的動作](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)。

1. 在您新增的觸發程式或動作上，依照下列步驟執行：

   * **內建觸發程式和支援使用受控識別的動作**

     1. 如果屬性尚未出現，請新增 [ **驗證** ] 屬性。

     1. 在 [ **驗證類型**] 下，選取 [ **受控識別**]。

     如需詳細資訊，請參閱 [範例：使用受控識別來驗證內建觸發程式或動作](#authenticate-built-in-managed-identity)。
 
   * **受控連接器觸發程式和支援使用受控識別的動作**

     1. 在 [租使用者選取] 頁面上，選取 **[使用受控識別連線]**。

     1. 在下一個頁面上，提供連接名稱。

        根據預設，受控識別清單只會顯示目前已啟用的受控識別，因為邏輯應用程式一次只支援啟用一個受控識別，例如：

        ![顯示 [連線名稱] 頁面和所選受控識別的螢幕擷取畫面。](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     如需詳細資訊，請參閱 [範例：使用受控識別來驗證受控連接器觸發程式或動作](#authenticate-managed-connector-managed-identity)。

     您建立來使用受控識別的連線是特殊的連線類型，僅適用于受控識別。 在執行時間，連線會使用邏輯應用程式上啟用的受控識別。 如果已啟用使用者指派的身分識別，此設定會儲存在邏輯應用程式資源定義的 `parameters` 物件中，其中包含的 `$connections` 物件會包含連線資源識別碼的指標，以及身分識別的資源識別碼。

     此範例顯示當邏輯應用程式啟用系統指派的受控識別時，設定看起來的樣子：

     ```json
     "parameters": {
        "$connections": {
           "value": {
              "<action-name>": {
                 "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
                 "connectionName": "{connection-name}",
                 "connectionProperties": {
                    "authentication": {
                       "type": "ManagedServiceIdentity"
                    }
                 },
                 "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
              }
           }
        }
     }
     ```

     此範例顯示當邏輯應用程式啟用使用者指派的受控識別時，設定看起來的樣子：

     ```json
     "parameters": {
        "$connections": {
           "value": {
              "<action-name>": {
                 "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
                 "connectionName": "{connection-name}",
                 "connectionProperties": {
                    "authentication": {
                       "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                       "type": "ManagedServiceIdentity"
                    }
                 },
                 "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
              }
           }
        }
     }
     ```

     在執行時間期間，Logic Apps 服務會檢查是否已將邏輯應用程式中的任何受控連接器觸發程式和動作設定為使用受控識別，而且所有必要的許可權都已設定為使用受控識別來存取觸發程式和動作所指定的目標資源。 如果成功，Logic Apps 服務會抓取與受控識別相關聯的 Azure AD 權杖，並使用該身分識別來驗證對目標資源的存取，並在觸發程式和動作中執行設定的作業。

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>範例：使用受控識別來驗證內建觸發程式或動作

HTTP 觸發程式或動作可以使用您為邏輯應用程式啟用的系統指派身分識別。 一般來說，HTTP 觸發或動作會使用這些屬性來指定您想要存取的資源或實體：

| 屬性 | 必要 | 描述 |
|----------|----------|-------------|
| **方法** | 是 | 您想要執行之作業所使用的 HTTP 方法 |
| **URI** | 是 | 用於存取目標 Azure 資源或實體的端點 URL。 URI 語法通常包含 Azure 資源或服務的[資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
| **標頭** | 否 | 您需要包含在連出要求中的任何標題值，例如內容類型 |
| **查詢** | 否 | 您需要包含在要求中的任何查詢參數，例如特定作業的參數，或您想要執行之作業的 API 版本 |
| **驗證** | 是 | 用來驗證對目標資源或實體存取的驗證類型 |
||||

在特定的範例中，假設您想要在先前設定身分識別存取權的 Azure 儲存體帳戶中，於 Blob 上執行[快照集 Blob 作業](/rest/api/storageservices/snapshot-blob)。 不過，[Azure Blob 儲存體連接器](/connectors/azureblob/)目前不提供這種操作。 相反地，您可以使用 [HTTP 動作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)或其他 [Blob 服務 REST API 作業](/rest/api/storageservices/operations-on-blobs)來執行這項作業。

> [!IMPORTANT]
> 若要使用 HTTP 要求和受控識別存取防火牆後方的 Azure 儲存體帳戶，請確定您也使用[允許受信任Microsoft 服務存取的例外狀況](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)設定儲存體帳戶。

若要執行[快照集 Blob 作業](/rest/api/storageservices/snapshot-blob)，HTTP 動作會指定下列屬性：

| 屬性 | 必要 | 範例值 | 描述 |
|----------|----------|---------------|-------------|
| **方法** | 是 | `PUT`| 快照集 Blob 操作所使用的HTTP 方法 |
| **URI** | 是 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Azure 全域 (公用) 環境中 Azure Blob 儲存體檔案的資源識別碼會使用此語法 |
| **標頭** | 針對 Azure 儲存體 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r'}` | `x-ms-blob-type` `x-ms-version` `x-ms-date` Azure 儲存體作業需要、和標頭值。 <p><p>**重要**：在 Azure 儲存體的傳出 HTTP 觸發和動作要求中，標題需要您要執行的作業所用的 `x-ms-version` 屬性和 API 版本。 `x-ms-date`必須是目前的日期。 否則，您的邏輯應用程式會失敗，並出現 `403 FORBIDDEN` 錯誤。 若要取得所需格式的目前日期，您可以使用範例值中的運算式。 <p>如需詳細資訊，請參閱下列主題： <p><p>- [要求標題 - 快照集 Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Azure 儲存體服務的版本設定](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **查詢** | 僅適用于快照集 Blob 作業 | `comp` = `snapshot` | 運算的查詢參數名稱和值。 |
|||||

以下是範例 HTTP 動作，其中顯示所有的屬性值：

![新增 HTTP 動作以存取 Azure 資源](./media/create-managed-service-identity/http-action-example.png)

1. 新增 HTTP 動作之後，請將 [ **驗證** ] 屬性新增至 HTTP 動作。 從 [新增參數] 清單中，選取 [驗證]。

   ![將「Authentication」屬性新增至 HTTP 動作](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 並非所有的觸發和動作都支援讓您新增驗證類型。 如需詳細資訊，請參閱 [觸發程式的驗證類型和支援驗證的動作](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)。

1. 從 [驗證類型] 清單中，選取 [受控識別]。

   ![對於 [驗證]，選取 [受控識別]](./media/create-managed-service-identity/select-managed-identity.png)

1. 從 [受控識別] 清單中，根據您的案例從可用的選項中選取。

   * 如果您設定系統指派的身分識別，請選取 [系統指派的受控識別] (如果尚未選取)。

     ![選取 [系統指派的受控識別]](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * 如果您設定使用者指派的身分識別，請選取該身分識別 (如果尚未選取)。

     ![選取使用者指派的身分識別](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   此範例會繼續使用 **系統指派的受控識別**。

1. 在某些觸發和動作上，也會顯示 [物件] 屬性，讓您設定目標資源識別碼。 將 [物件] 屬性設定為[目標資源或服務的資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 否則，根據預設，[物件] 屬性會使用 `https://management.azure.com/` 資源識別碼，也就是 Azure Resource Manager 的資源識別碼。

   > [!IMPORTANT]
   > 請確定目標資源識別碼 *完全符合* Azure Active Directory (AD) 所預期的值，包括任何必要的後置斜線。 例如，所有 Azure Blob 儲存體帳戶的資源識別碼都需要後置斜線。 不過，特定儲存體帳戶的資源識別碼不需要後置斜線。 檢查[支援 Azure AD 的 Azure 服務所用的資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   這個範例會將 [物件] 屬性設定為 `https://storage.azure.com/`，讓用於驗證的存取權杖對所有儲存體帳戶都有效。 不過，您也可以指定特定儲存體帳戶的根服務 URL `https://fabrikamstorageaccount.blob.core.windows.net`。

   ![將 [物件] 屬性設定為 [目標資源識別碼]](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   如需使用 Azure 儲存體的Azure AD 授權存取的詳細資訊，請參閱下列主題：

   * [使用 Azure Active Directory 來授權 Azure Blob 和佇列的存取權](../storage/common/storage-auth-aad.md)
   * [使用 Azure Active Directory 授與 Azure 儲存體的存取權](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 繼續按照您想要的方式建置邏輯應用程式。

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>範例：使用受控識別來驗證受控連接器觸發程式或動作

Azure Resource Manager 動作（ **讀取資源**）可以使用您為邏輯應用程式啟用的受控識別。 此範例說明如何使用系統指派的受控識別。

1. 將動作新增至工作流程之後，請在 [租使用者選取] 頁面上選取 **[使用受控識別連線]**。

   ![顯示 Azure Resource Manager 動作和選取 [使用受控識別連線] 的螢幕擷取畫面。](./media/create-managed-service-identity/select-connect-managed-identity.png)

   動作現在會顯示具有受控識別清單的 [連線名稱] 頁面，其中包含邏輯應用程式上目前已啟用的受控識別類型。

1. 在 [連接名稱] 頁面上，提供連接的名稱。 從 [受控識別] 清單中，選取受控識別（在此範例中為 **系統指派的受控識別** ），然後選取 [ **建立**]。 如果您已啟用使用者指派的受控識別，請改為選取該身分識別。

   ![顯示 Azure Resource Manager 動作的螢幕擷取畫面，其中已選取 [連線名稱] 和 [系統指派的受控識別]。](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   如果未啟用受控識別，則當您嘗試建立連接時，會出現下列錯誤：

   *您必須為您的邏輯應用程式啟用受控識別，然後將所需的存取權授與目標資源中的身分識別。*

   ![螢幕擷取畫面，顯示未啟用受控識別時，有錯誤的 Azure Resource Manager 動作。](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. 成功建立連接之後，設計工具可以使用受控識別驗證來提取任何動態值、內容或架構。

1. 繼續按照您想要的方式建置邏輯應用程式。

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>停用受控識別

若要停止為您的邏輯應用程式使用受控識別，您有下列選項：

* [Azure 入口網站](#azure-portal-disable)
* [Azure 資源管理員範本](#template-disable)
* Azure PowerShell
  * [移除角色指派](../role-based-access-control/role-assignments-powershell.md)
  * [刪除使用者指派的身分識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [移除角色指派](../role-based-access-control/role-assignments-cli.md)
  * [刪除使用者指派的身分識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [移除角色指派](../role-based-access-control/role-assignments-rest.md)
  * [刪除使用者指派的身分識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

如果您刪除邏輯應用程式，Azure 會自動從 Azure AD 移除移除受控識別。

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>在 Azure 入口網站中停用受控識別

在 Azure 入口網站中，先[對於您的目標資源](#disable-identity-target-resource)移除身分識別的存取權。 接著，關閉系統指派的身分識別，或從邏輯應用程式[移除](#disable-identity-logic-app)使用者指派的身分識別。

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>從資源移除身分識別存取

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您想要針對受控識別移除存取權的目標 Azure 資源。

1. 從目標資源的功能表中，選取 [存取控制 (IAM)]。 在工具列底下，選取 [角色指派]。

1. 在角色清單中，選取您想要移除的受控識別。 在工具列上，選取 [移除]。

   > [!TIP]
   > 如果 [移除] 選項已停用，您很可能沒有權限。 如需可讓您管理資源角色的權限的詳細資訊，請參閱 [Azure Active Directory 中的系統管理員角色權限](../active-directory/roles/permissions-reference.md)。

受控識別現在已移除，且不再具有目標資源的存取權。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>停用邏輯應用程式上的受控識別

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [身分識別]，然後依照您的身分識別步驟執行：

   * 選取 [系統指派] > [開啟] > [儲存]。 Azure 提示您確認時，請選取 [是]。

     ![停用系統指派的身分識別](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * 選取 [使用者指派] 和 [受控識別]，然後選取 [移除]。 Azure 提示您確認時，請選取 [是]。

     ![移除使用者指派的身分識別](./media/create-managed-service-identity/remove-user-assigned-identity.png)

受控識別現在已在邏輯應用程式上停用。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>停用 Azure Resource Manager 範本中的受控識別

如果您是使用 Azure Resource Manager 範本建立邏輯應用程式的受控識別，請將 `identity` 物件的 `type` 子屬性設定為 `None`。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>後續步驟

* [保護 Azure Logic Apps 中的存取權和資料](../logic-apps/logic-apps-securing-a-logic-app.md)
