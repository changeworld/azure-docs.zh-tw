---
title: 尋找驗證的識別物件識別碼-Azure API for FHIR
description: 本文說明如何找出為 Azure API for FHIR 設定驗證所需的身分識別物件識別碼。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 706d7e081743f2bab1f593e00dc792f218a000ea
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033620"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>尋找驗證設定的識別物件識別碼

在本文中，您將瞭解如何在設定 Azure API for FHIR 使用資料平面的 [外部或次要 Active Directory 租](configure-local-rbac.md) 使用者時，尋找所需的身分識別物件識別碼。

## <a name="find-user-object-id"></a>尋找使用者物件識別碼

如果您有具有使用者名稱的使用者 `myuser@contoso.com` ，您可以 `ObjectId` 使用下列 PowerShell 命令來找出使用者：

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

或者，您可以使用 Azure CLI：

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>尋找服務主體物件識別碼

假設您已註冊 [服務用戶端應用程式](register-service-azure-ad-client-app.md) ，而您想要讓此服務用戶端存取 Azure API for FHIR，您可以使用下列 PowerShell 命令來尋找用戶端服務主體的物件識別碼：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

其中 `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` 是服務用戶端應用程式識別碼。 或者，您可以使用 `DisplayName` 服務用戶端的：

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

如果您使用 Azure CLI，可以使用：

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>尋找安全性群組物件識別碼

如果您想要找出安全性群組的物件識別碼，您可以使用下列 PowerShell 命令：

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
其中 `mygroup` 是您感興趣的組名。

如果您使用 Azure CLI，可以使用：

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>接下來的步驟

在本文中，您已瞭解如何尋找將 Azure API for FHIR 設定為使用外部或次要 Azure Active Directory 租使用者所需的身分識別物件識別碼。 接下來，請閱讀如何使用物件識別碼來設定本機 RBAC 設定：
 
>[!div class="nextstepaction"]
>[設定本機 RBAC 設定](configure-local-rbac.md)
