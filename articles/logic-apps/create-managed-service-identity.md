---
title: 使用受控識別進行驗證
description: 使用受控識別來存取其他 Azure Active Directory 租使用者中的資源，而不需以認證或秘密進行登入
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117200"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>使用 Azure Logic Apps 中的受控識別來驗證對 Azure 資源的存取

若要存取其他 Azure Active Directory （Azure AD）租使用者中的資源並驗證您的身分識別而不登入，您的邏輯應用程式可以使用[受控識別](../active-directory/managed-identities-azure-resources/overview.md)（先前稱為受控服務識別或 MSI），而不是認證或秘密。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。

Azure Logic Apps 同時支援[*系統指派*](../active-directory/managed-identities-azure-resources/overview.md)和[*使用者指派的*](../active-directory/managed-identities-azure-resources/overview.md)受控識別。 您的邏輯應用程式可以使用系統指派的身分識別或*單一*使用者指派的身分識別，您可以在一組邏輯應用程式之間共用，但不能同時在兩者之間共用。 目前，只有[特定內建的觸發程式和動作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)支援受控識別，而非受控連接器或連接，例如：

* HTTP
* Azure Functions
* Azure API 管理
* Azure App Service

本文說明如何為您的邏輯應用程式設定這兩種受控識別。 如需詳細資訊，請參閱下列主題：

* [支援受控識別的觸發程式和動作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [輸出呼叫上支援的驗證類型](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [邏輯應用程式的受控識別限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [支援使用受控識別進行 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 受控識別和您需要存取的目標 Azure 資源，都必須使用相同的 Azure 訂用帳戶。

* 若要為受控識別授與 Azure 資源的存取權，您必須將角色新增至該身分識別的目標資源。 若要新增角色，您需要[Azure AD 系統管理員許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)，才能將角色指派給對應 Azure AD 租使用者中的身分識別。

* 您想要存取的目標 Azure 資源。 在此資源上，您將新增受控識別的角色，以協助邏輯應用程式驗證對目標資源的存取。

* 您想要在其中使用[觸發程式或支援受控](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)識別之動作的邏輯應用程式

## <a name="enable-managed-identity"></a>啟用受控識別

若要設定您想要使用的受控識別，請遵循該身分識別的連結：

* [系統指派的身分識別](#system-assigned)
* [使用者指派的身分識別](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>啟用系統指派的身分識別

不同于使用者指派的身分識別，您不需要手動建立系統指派的身分識別。 若要為您的邏輯應用程式設定系統指派的身分識別，以下是您可以使用的選項：

* [Azure 入口網站](#azure-portal-system-logic-app)
* [Azure 資源管理員範本](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 入口網站中啟用系統指派的身分識別

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定]**** 下，選取 [身分識別]****。 選取 [**儲存**時**指派** > **的** > 系統]。 當 Azure 提示您確認時，請選取 **[是]**。

   ![啟用系統指派的身分識別](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > 如果您收到一則錯誤，表示您只能有一個受控識別，則您的邏輯應用程式已與使用者指派的身分識別建立關聯。 在您可以新增系統指派的身分識別之前，必須先從邏輯應用程式*移除*使用者指派的身分識別。

   您的邏輯應用程式現在可以使用系統指派的身分識別，它會向 Azure Active Directory 註冊，並以物件識別碼表示。

   ![系統指派之身分識別的物件識別碼](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **物件識別碼** | <*身分識別-資源識別碼*> | 全域唯一識別碼（GUID），代表 Azure AD 租使用者中邏輯應用程式的系統指派身分識別 |
   ||||

1. 現在依照本主題稍後的[為該身分識別存取資源的步驟](#access-other-resources)進行。

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中啟用系統指派的身分識別

若要自動建立和部署 Azure 資源（例如邏輯應用程式），您可以使用[Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 若要在範本中為您的邏輯應用程式啟用系統指派的受控識別， `identity`請將物件`type`和子屬性新增至範本中邏輯應用程式的資源定義，例如：

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

當 Azure 建立您的`identity`邏輯應用程式資源定義時，物件會取得下列其他屬性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | 值 | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*主體識別碼*> | 受控識別之服務主體物件的全域唯一識別碼（GUID），代表 Azure AD 租使用者中的邏輯應用程式。 此 GUID 有時候會顯示為「物件識別碼」或`objectID`。 |
| `tenantId` | <*Azure-AD-租使用者識別碼*> | 全域唯一識別碼（GUID），代表邏輯應用程式現在是成員的 Azure AD 租使用者。 在 Azure AD 租用戶中，服務主體會有與邏輯應用程式執行個體相同的名稱。 |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>啟用使用者指派的身分識別

若要為您的邏輯應用程式設定使用者指派的受控識別，您必須先將該身分識別建立為個別的獨立 Azure 資源。 以下是您可以使用的選項：

* [Azure 入口網站](#azure-portal-user-identity)
* [Azure 資源管理員範本](#template-user-identity)
* Azure PowerShell
  * [建立使用者指派的身分識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [新增角色指派](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [建立使用者指派的身分識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [新增角色指派](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [建立使用者指派的身分識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [新增角色指派](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>在 Azure 入口網站中建立使用者指派的身分識別

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，于任何頁面的 [搜尋] 方塊`managed identities`中，輸入，然後選取 [**受控**識別]。

   ![尋找並選取 [受控識別]](./media/create-managed-service-identity/find-select-managed-identities.png)

1. 在 [**受控**識別] 底下，選取 [**新增**]。

   ![新增受控識別](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 提供受控識別的相關資訊，然後選取 [**建立**]，例如：

   ![建立使用者指派的受控識別](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **資源名稱** | 是 | <*使用者指派-身分識別-名稱*> | 要提供給使用者指派身分識別的名稱。 這個範例會使用「Fabrikam-使用者指派的身分識別」。 |
   | **訂用帳戶** | 是 | <*Azure-訂用帳戶-名稱*> | 要使用的 Azure 訂用帳戶的名稱 |
   | **資源群組** | 是 | <*Azure-資源群組-名稱*> | 要使用之資源群組的名稱。 建立新的群組，或選取現有的群組。 這個範例會建立名為「fabrikam 管理-身分識別-RG」的新群組。 |
   | **位置** | 是 | <*Azure-區域*> | Azure 區域，用來儲存資源的相關資訊。 此範例使用「美國西部」。 |
   |||||

   現在您可以將使用者指派的身分識別新增至邏輯應用程式。 您無法將多個使用者指派的身分識別新增至您的邏輯應用程式。

1. 在 Azure 入口網站的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [**設定**] 底下，選取 [身分**識別**]，然後選取 [**使用者指派** > 的**新增**]。

   ![新增使用者指派的受控識別](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. 在 [**新增使用者指派的受控識別**] 窗格中，從 [**訂**用帳戶] 清單中選取您的 Azure 訂用帳戶（如果尚未選取）。 從顯示該訂用帳戶中*所有*受控識別的清單中，尋找並選取您想要的使用者指派身分識別。 若要篩選清單，請在 [**使用者指派的受控**識別] 搜尋方塊中，輸入身分識別或資源群組的名稱。 當您完成時，請選取 [**新增**]。

   ![選取要使用的使用者指派身分識別](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > 如果您收到一則錯誤，表示您只能有一個受控識別，則您的邏輯應用程式已與系統指派的身分識別建立關聯。 在您可以新增使用者指派的身分識別之前，必須先在邏輯應用程式上停用系統指派的身分識別。

   您的邏輯應用程式現在與使用者指派的受控識別相關聯。

   ![與使用者指派的身分識別關聯](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 現在依照本主題稍後的[為該身分識別存取資源的步驟](#access-other-resources)進行。

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中建立使用者指派的身分識別

若要自動建立和部署 Azure 資源（例如邏輯應用程式），您可以使用[Azure Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，其支援[使用者指派的身分識別來進行驗證](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)。 在範本的`resources`區段中，邏輯應用程式的資源定義需要下列專案：

* 屬性`identity`設定為的`type`物件。`UserAssigned`

* 子`userAssignedIdentities`物件，指定身分識別的資源識別碼，也就是具有`principalId`和`clientId`屬性的另一個子物件。

這個範例會顯示 HTTP PUT 要求的邏輯應用程式資源定義，並包含非參數化`identity`的物件。 PUT 要求和後續 GET 作業的回應也有這個`identity`物件：

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
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
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

| Property (JSON) | 值 | 描述 |
|-----------------|-------|-------------|
| `principalId` | <*主體識別碼*> | Azure AD 租使用者中使用者指派受控識別的全域唯一識別碼（GUID） |
| `clientId` | <*用戶端識別碼*> | 邏輯應用程式新身分識別的全域唯一識別碼（GUID），用於執行時間期間的呼叫 |
||||

如果您的範本也包含受控識別的資源定義，您可以參數化`identity`物件。 這個範例會顯示子`userAssignedIdentities`物件如何參考您`userAssignedIdentity`在範本的`variables`區段中定義的變數。 此變數會參考使用者指派之身分識別的資源識別碼。

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
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Property (JSON) | 值 | 描述 |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-租使用者識別碼*> | 全域唯一識別碼（GUID），代表使用者指派的身分識別現在是成員的 Azure AD 租使用者。 在 Azure AD 租使用者內，服務主體的名稱與使用者指派的身分識別名稱相同。 |
| `principalId` | <*主體識別碼*> | Azure AD 租使用者中使用者指派受控識別的全域唯一識別碼（GUID） |
| `clientId` | <*用戶端識別碼*> | 邏輯應用程式新身分識別的全域唯一識別碼（GUID），用於執行時間期間的呼叫 |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>授與資源的身分識別存取權

在您可以使用邏輯應用程式的受控識別進行驗證之前，請先在您打算使用該身分識別的 Azure 資源上，設定該身分識別的存取權。 若要完成這項工作，請在目標 Azure 資源上將適當的角色指派給該身分識別。 以下是您可以使用的選項：

* [Azure 入口網站](#azure-portal-assign-access)
* [Azure Resource Manager 範本](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell （[new-azroleassignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)）-如需詳細資訊，請參閱[使用 Azure RBAC 和 Azure PowerShell 新增角色指派](../role-based-access-control/role-assignments-powershell.md)。
* Azure CLI （[az role 指派 create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)）-如需詳細資訊，請參閱[使用 Azure RBAC 和 Azure CLI 新增角色指派](../role-based-access-control/role-assignments-cli.md)。
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>在 Azure 入口網站中指派存取權

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，移至您想要讓受控識別擁有存取權的 Azure 資源。

1. 從資源的功能表中，選取 [**存取控制（IAM）** > **角色指派**]，您可以在其中檢查該資源目前的角色指派。 在工具列上，選取 [**新增** > ] [**新增角色指派**]。

   ![選取 [新增] > [新增角色指派]](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > 如果 [**新增角色指派**] 選項已停用，您很可能沒有許可權。 如需可讓您管理資源角色之許可權的詳細資訊，請參閱[Azure Active Directory 中的系統管理員角色許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

1. 在 [**新增角色指派**] 底下，選取一個**角色**，讓您的身分識別具有目標資源的必要存取權。

   在本主題的範例中，您的身分識別需要[可存取 Azure 儲存體容器中之 blob 的角色](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)。

   ![選取 [儲存體 Blob 資料參與者] 角色](./media/create-managed-service-identity/select-role-for-identity.png)

1. 針對受控識別，請遵循下列步驟：

   * **系統指派的身分識別**

     1. 在 [**指派存取**權] 方塊中，選取 [**邏輯應用程式**]。 當 [**訂**用帳戶] 屬性出現時，請選取與您的身分識別相關聯的 Azure 訂用帳戶。

        ![針對系統指派的身分識別選取存取權](./media/create-managed-service-identity/assign-access-system.png)

     1. 在 [**選取**] 方塊底下，從清單中選取您的邏輯應用程式。 如果清單太長，請使用 [**選取**] 方塊來篩選清單。

        ![針對系統指派的身分識別選取邏輯應用程式](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **使用者指派的身分識別**

     1. 在 [**指派存取**權] 方塊中，選取 [**使用者指派的受控識別**]。 當 [**訂**用帳戶] 屬性出現時，請選取與您的身分識別相關聯的 Azure 訂用帳戶。

        ![選取使用者指派身分識別的存取權](./media/create-managed-service-identity/assign-access-user.png)

     1. 在 [**選取**] 方塊底下，從清單中選取您的身分識別。 如果清單太長，請使用 [**選取**] 方塊來篩選清單。

        ![選取使用者指派的身分識別](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. 完成時，選取 [儲存]****。

   目標資源的 [角色指派] 清單現在會顯示選取的受控識別和角色。 這個範例會示範如何將系統指派的身分識別用於一個邏輯應用程式，並將使用者指派的身分識別用於另一個邏輯應用程式群組。

   ![已將受控識別和角色新增至目標資源](./media/create-managed-service-identity/added-roles-for-identities.png)

   如需詳細資訊，請[使用 Azure 入口網站，將受控識別存取權指派給資源](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。

1. 現在請遵循下列步驟，在支援受控識別的觸發程式或動作中，[使用身分識別來驗證存取權](#authenticate-access-with-identity)。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用受控識別來驗證存取權

為[邏輯應用程式啟用受控識別](#azure-portal-system-logic-app)，並將[該身分識別存取權授與目標資源或實體](#access-other-resources)之後，您就可以在支援受控識別的觸發程式[和動作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)中使用該身分識別。

> [!IMPORTANT]
> 如果您有想要使用系統指派身分識別的 Azure 函式，請先[啟用 azure 函式的驗證功能](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

這些步驟示範如何透過 Azure 入口網站，使用受控識別搭配觸發程式或動作。 若要在觸發程式或動作的基礎 JSON 定義中指定受控識別，請參閱[受控識別驗證](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在[Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟邏輯應用程式。

1. 如果您還沒有這麼做，請新增[支援受控識別的觸發程式或動作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

   例如，HTTP 觸發程式或動作可以使用您為邏輯應用程式啟用的系統指派身分識別。 一般來說，HTTP 觸發程式或動作會使用這些屬性來指定您想要存取的資源或實體：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **方法** | 是 | 您想要執行之作業所使用的 HTTP 方法 |
   | **URI** | 是 | 用於存取目標 Azure 資源或實體的端點 URL。 URI 語法通常包含 Azure 資源或服務的[資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   | **headers** | 否 | 您需要包含在連出要求中的任何標頭值，例如內容類型 |
   | **查詢** | 否 | 您需要包含在要求中的任何查詢參數，例如特定作業的參數，或您想要執行之作業的 API 版本 |
   | **驗證** | 是 | 用來驗證對目標資源或實體之存取的驗證類型 |
   ||||

   在特定的範例中，假設您想要在您先前設定身分識別存取權的 Azure 儲存體帳戶中，于 Blob 上執行[快照集 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)作業。 不過， [Azure Blob 儲存體連接器](https://docs.microsoft.com/connectors/azureblob/)目前不提供這種操作。 相反地，您可以使用[HTTP 動作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)或另一個[Blob 服務 REST API](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)作業來執行此作業。

   > [!IMPORTANT]
   > 若要使用 HTTP 要求和受控識別存取防火牆後方的 Azure 儲存體帳戶，請確定您也使用[允許受信任的 Microsoft 服務存取的例外](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)狀況來設定儲存體帳戶。

   若要執行[快照集 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)作業，HTTP 動作會指定下列屬性：

   | 屬性 | 必要 | 範例值 | 描述 |
   |----------|----------|---------------|-------------|
   | **方法** | 是 | `PUT`| 快照集 Blob 操作所使用的 HTTP 方法 |
   | **URI** | 是 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Azure 全域（公用）環境中 Azure Blob 儲存體檔案的資源識別碼，其使用此語法 |
   | **headers** | 是，適用于 Azure 儲存體 | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure 儲存體`x-ms-blob-type`作業`x-ms-version`所需的和標頭值。 <p><p>**重要**事項：在 Azure 儲存體的傳出 HTTP 觸發程式和動作要求中，標`x-ms-version`頭需要您想要執行之作業的屬性和 API 版本。 <p>如需詳細資訊，請參閱下列主題： <p><p>- [要求標頭-快照集 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Azure 儲存體服務的版本控制](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **查詢** | 是，適用于這種操作 | `comp` = `snapshot` | 快照集 Blob 作業的查詢參數名稱和值。 |
   |||||

   以下是範例 HTTP 動作，其中顯示所有的屬性值：

   ![新增 HTTP 動作以存取 Azure 資源](./media/create-managed-service-identity/http-action-example.png)

1. 現在，將 [**驗證**] 屬性新增至 HTTP 動作。 從 [**加入新的參數**] 清單中，選取 [**驗證**]。

   ![將 "Authentication" 屬性新增至 HTTP 動作](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > 並非所有的觸發程式和動作都支援讓您新增驗證類型。 如需詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 從 [**驗證類型**] 清單中，選取 [**受控識別**]。

   ![針對 [驗證]，選取 [受控識別]](./media/create-managed-service-identity/select-managed-identity.png)

1. 從 [受控識別] 清單中，根據您的案例從可用的選項中選取。

   * 如果您設定了系統指派的身分識別，請選取 [**系統指派的受控識別**] （如果尚未選取）。

     ![選取 [系統指派的受控識別]](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * 如果您設定使用者指派的身分識別，請選取該身分識別（如果尚未選取）。

     ![選取使用者指派的身分識別](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   此範例會繼續使用**系統指派的受控識別**。

1. 在某些觸發程式和動作上，也會顯示 [**物件**] 屬性，讓您設定目標資源識別碼。 將 [**物件**] 屬性設定為[目標資源或服務的資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 否則，根據預設，**物件**屬性會使用`https://management.azure.com/`資源識別碼，也就是 Azure Resource Manager 的資源識別碼。

   > [!IMPORTANT]
   > 請確定目標資源識別碼*完全符合*AZURE ACTIVE DIRECTORY （AD）所預期的值，包括任何必要的尾端斜線。 例如，所有 Azure Blob 儲存體帳戶的資源識別碼都需要尾端斜線。 不過，特定儲存體帳戶的資源識別碼不需要尾端斜線。 檢查[支援 Azure AD 之 Azure 服務的資源識別碼](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   這個範例會將**物件**屬性設定`https://storage.azure.com/`為，讓用於驗證的存取權杖對所有儲存體帳戶有效。 不過，您也可以指定特定儲存體帳戶的根`https://fabrikamstorageaccount.blob.core.windows.net`服務 URL。

   ![將 [物件] 屬性設定為 [目標資源識別碼]](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   如需使用 Azure 儲存體的 Azure AD 授權存取的詳細資訊，請參閱下列主題：

   * [使用 Azure Active Directory 授權存取 Azure blob 和佇列](../storage/common/storage-auth-aad.md)
   * [使用 Azure Active Directory 授權 Azure 儲存體的存取權](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 以您想要的方式繼續建立邏輯應用程式。

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

如果您刪除邏輯應用程式，Azure 會自動從 Azure AD 移除受控識別。

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>停用 Azure 入口網站中的受控識別

在 Azure 入口網站中，先移除身分識別對[您目標資源](#disable-identity-target-resource)的存取權。 接下來，關閉系統指派的身分識別，或從[邏輯應用程式](#disable-identity-logic-app)中移除使用者指派的身分識別。

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>從資源移除身分識別存取

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您想要移除受控識別存取權的目標 Azure 資源。

1. 從目標資源的功能表中，選取 [**存取控制（IAM）**]。 在工具列底下，選取 [**角色指派**]。

1. 在 [角色] 清單中，選取您想要移除的受控識別。 在工具列上，選取 [**移除**]。

   > [!TIP]
   > 如果停用 [**移除**] 選項，您很可能沒有許可權。 如需可讓您管理資源角色之許可權的詳細資訊，請參閱[Azure Active Directory 中的系統管理員角色許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

受控識別現在已移除，且不再具有目標資源的存取權。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>在邏輯應用程式上停用受控識別

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [**設定**] 底下，選取 [身分**識別**]，然後依照您的身分識別步驟執行：

   * 選取 [**儲存**時**指派** > **的** > 系統]。 當 Azure 提示您確認時，請選取 **[是]**。

     ![停用系統指派的身分識別](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * 選取 [**使用者指派**] 和 [受控識別]，然後選取 [**移除**]。 當 Azure 提示您確認時，請選取 **[是]**。

     ![移除使用者指派的身分識別](./media/create-managed-service-identity/remove-user-assigned-identity.png)

受控識別現在已在邏輯應用程式上停用。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>停用 Azure Resource Manager 範本中的受控識別

如果您使用 Azure Resource Manager 範本建立邏輯應用程式的受控識別，請將`identity`物件的`type`子屬性設定為。 `None` 針對系統管理的身分識別，此動作也會刪除 Azure AD 中的主體識別碼。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>後續步驟

* [保護 Azure Logic Apps 中的存取和資料](../logic-apps/logic-apps-securing-a-logic-app.md)