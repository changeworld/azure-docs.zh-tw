---
title: 使用 Azure CLI Azure API for FHIR 取得存取權杖
description: 本文說明如何使用 Azure CLI 取得 Azure API for FHIR 的存取權杖。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: 4d1c4cfcb15d97a2c54a04344f0bd098f65c1392
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660367"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>使用 Azure CLI 取得 Azure API for FHIR 的存取權杖

在本文中，您將瞭解如何使用 Azure CLI 取得 Azure API for FHIR 的存取權杖。 當您布建 [Azure API for FHIR](fhir-paas-portal-quickstart.md)時，您可以設定一組可以存取服務的使用者或服務主體。 如果您的使用者物件識別碼位於允許的物件識別碼清單中，您可以使用 Azure CLI 取得的權杖來存取服務。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>取得權杖

Azure API for FHIR 使用 `resource`  或，其 `Audience` URI 等於 FHIR 伺服器的 uri `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 。 您可以使用下列命令來取得權杖，並將它儲存在名為) 的變數 (中 `$token` ：

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
