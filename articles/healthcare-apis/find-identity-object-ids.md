---
title: 尋找用於驗證的識別物件識別碼-Azure API for FHIR
description: 本文說明如何找出設定驗證所需的身分識別物件識別碼 Azure API for FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 88c0349ce220229920a39ba4cb6640f3d4dde93b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871198"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>尋找驗證設定的識別物件識別碼

在本文中，您將瞭解如何在設定 Azure API for FHIR 以使用資料平面的[外部或次要 Active Directory 租](configure-local-rbac.md)使用者時，尋找所需的識別物件識別碼。

## <a name="find-user-object-id"></a>尋找使用者物件識別碼

如果您的使用者具有使用者名稱 `myuser@consoso.com` ，您可以 `ObjectId` 使用下列 PowerShell 命令來尋找使用者：

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

或者，您可以使用 Azure CLI：

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id"></a>尋找服務主體物件識別碼

假設您已註冊[服務用戶端應用程式](register-service-azure-ad-client-app.md)，而且想要允許此服務用戶端存取 Azure API for FHIR，您可以使用下列 PowerShell 命令來尋找用戶端服務主體的物件識別碼：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

其中 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` 是服務用戶端應用程式識別碼。 或者，您可以使用 `DisplayName` 服務用戶端的：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

如果您使用 Azure CLI，您可以使用：

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="find-a-security-group-object-id"></a>尋找安全性群組物件識別碼

如果您想要找出安全性群組的物件識別碼，可以使用下列 PowerShell 命令：

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
其中 `mygroup` 是您感興趣的組名。

如果您使用 Azure CLI，您可以使用：

```azurecli-interactive
az ad group show --group "mygroup" | jq -r .objectId
```

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何尋找設定 Azure API for FHIR 以使用外部或次要 Azure Active Directory 租使用者所需的身分識別物件識別碼。 接下來，閱讀如何使用物件識別碼來設定本機 RBAC 設定：
 
>[!div class="nextstepaction"]
>[設定本機 RBAC 設定](configure-local-rbac.md)