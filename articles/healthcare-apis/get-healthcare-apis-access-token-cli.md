---
title: 使用 Azure CLI Azure API for FHIR 取得存取權杖
description: 本文說明如何使用 Azure CLI 取得 Azure API for FHIR 的存取權杖。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: bdecf0dafa80f72eb528b4771c00befff8355e83
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072846"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>使用 Azure CLI 取得 Azure API for FHIR 的存取權杖

在本文中，您將瞭解如何使用 Azure CLI 取得 Azure API for FHIR 的存取權杖。 當您布建[Azure API for FHIR](fhir-paas-portal-quickstart.md)時，您要設定一組可存取服務的使用者或服務主體。 如果您的使用者物件識別碼位於允許的物件識別碼清單中，您可以使用使用 Azure CLI 取得的權杖來存取服務。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>使用 Azure CLI 登入

您必須先使用您想要取得權杖的使用者登入，才能取得權杖：

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>取得權杖

Azure API for FHIR 使用 `resource` 或 `Audience` ，其 URI 等於 FHIR 伺服器的 uri `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 。 您可以使用下列命令取得權杖，並將它儲存在變數中（名為 `$token` ）：

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>搭配 Azure API for FHIR 使用

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 Azure CLI 取得 Azure API for FHIR 的存取權杖。 若要了解如何使用 Postman 存取 FHIR API，請繼續進行 Postman 教學課程。

>[!div class="nextstepaction"]
>[使用 Postman 存取 FHIR API](access-fhir-postman-tutorial.md)
