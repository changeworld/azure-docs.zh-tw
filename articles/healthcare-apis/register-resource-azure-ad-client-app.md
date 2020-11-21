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
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024477"
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

如果您使用適用于 Azure 的開放原始碼 FHIR 伺服器，請遵循 [GitHub](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) 存放庫中的步驟來註冊資源應用程式。 

## <a name="next-steps"></a>下一步

在本文中，您已瞭解如何在 Azure Active Directory 中註冊資源應用程式。 接下來，註冊您的機密用戶端應用程式。
 
>[!div class="nextstepaction"]
>[註冊機密用戶端應用程式](register-confidential-azure-ad-client-app.md)