---
title: 在 Azure AD Azure API for FHIR 中註冊資源應用程式
description: 在 Azure Active Directory 中註冊資源 (或 API) 應用程式，讓用戶端應用程式可以在驗證時要求資源的存取權。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 3c5486e31dd3227cfcc8bd3b433602ce58cfa4fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852985"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>在 Azure Active Directory 中註冊資源應用程式

在本文中，您將瞭解如何在 Azure Active Directory 中註冊資源 (或 API) 應用程式。 資源應用程式是 FHIR 伺服器 API 本身的 Azure Active Directory 標記法，而且用戶端應用程式可以在驗證時要求資源的存取權。 資源應用程式也稱為 OAuth 說法中的 *物件* 。

## <a name="azure-api-for-fhir"></a>適用於 FHIR 的 Azure API

如果您使用 Azure API for FHIR，當您部署服務時，就會自動建立資源應用程式。 只要您是在部署應用程式時，在相同的 Azure Active Directory 租使用者中使用 Azure API for FHIR，就可以跳過本操作指南，並改為部署 Azure API for FHIR 以開始著手。

如果您使用不同的 Azure Active Directory 租使用者 (未與您的訂用帳戶) 建立關聯，您可以使用 PowerShell 將 Azure API for FHIR 資源應用程式匯入您的租使用者：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

或者，您可以使用 Azure CLI：

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>適用于 Azure 的 FHIR 伺服器

如果您使用適用于 Azure 的開放原始碼 FHIR 伺服器，請遵循下列步驟來註冊資源應用程式。

### <a name="app-registrations-in-azure-portal"></a>Azure 入口網站中的應用程式註冊

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]****。

2. 在 [ **Azure Active Directory** ] 分頁中，按一下 [ **應用程式註冊**：

    ![Azure 入口網站。 新的應用程式註冊。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 按一下 [ **新增註冊**]。

### <a name="add-a-new-application-registration"></a>新增應用程式註冊

填入新應用程式的詳細資料。 顯示名稱沒有特定的需求，但將其設定為 FHIR 伺服器的 URI 可讓您輕鬆找到：

![新增應用程式註冊](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>設定識別碼 URI 和定義範圍

資源應用程式的識別碼 URI (應用程式識別碼 URI) ，用戶端可以在要求存取資源時使用此 uri。 此值會填入 `aud` 存取權杖的宣告。 建議您將此 URI 設定為 FHIR 伺服器的 URI。 針對智慧型 FHIR 應用程式，假設 *物件* 是 FHIR 伺服器的 URI。

1. 按一下 [**公開 API** ]

2. 按一下 [*應用程式識別碼 URI*] 旁的 [**設定**]。

3. 輸入識別碼 URI，然後按一下 [ **儲存**]。 良好的識別碼 URI 會是您 FHIR 伺服器的 URI。

4. 按一下 [ **新增範圍** ]，然後新增您想要為 API 定義的任何範圍。 您必須新增至少一個範圍，才能在未來將許可權授與您的資源應用程式。 如果您沒有想要新增的特定範圍，您可以新增 user_impersonation 作為範圍。

![物件和範圍](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>定義應用程式角色

適用于 Azure 的 Azure API for FHIR 和 OSS FHIR 伺服器使用 [Azure Active Directory 應用程式角色](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) 進行角色型存取控制。 若要定義哪些角色應可供您的 FHIR 伺服器 API 使用，請開啟資源應用程式的 [資訊清單](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)：

1. 按一下 [ **資訊清單**]：

    ![應用程式角色](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. 在 `appRoles` 屬性中，新增您想要讓使用者或應用程式擁有的角色：

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Active Directory 中註冊資源應用程式。 接下來，部署 Azure API for FHIR。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)