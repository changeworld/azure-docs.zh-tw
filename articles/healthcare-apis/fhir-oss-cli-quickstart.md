---
title: Azure CLI：部署適用於 Azure 的開放原始碼 FHIR 伺服器 – Azure API for Azure
description: 本快速入門說明如何部署適用於 Azure 的 Microsoft Open Source FHIR 伺服器。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "84819972"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>快速入門：使用 Azure CLI 部署開放原始碼 FHIR 伺服器

在本快速入門中，您將了解如何使用 Azure CLI 在 Azure 中部署開放原始碼 FHIR&reg; 伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>建立資源群組

挑選將包含已佈建資源的資源群組名稱並加以建立：

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>部署範本

適用於 Azure [GitHub 存放庫](https://github.com/Microsoft/fhir-server)的 Microsoft FHIR Server 包含會部署所有必要資源的範本。 部署方式：

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>確認 FHIR 伺服器正在執行

從 FHIR 伺服器取得功能陳述式：

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

這需要一分鐘左右的時間，伺服器才會第一次回應。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除資源群組：

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將適用於 Azure 的 Microsoft 開放原始碼 FHIR 伺服器部署至您的訂用帳戶。 若要了解如何使用 Postman 存取 FHIR API，請繼續進行 Postman 教學課程。
 
>[!div class="nextstepaction"]
>[使用 Postman 存取 FHIR API](access-fhir-postman-tutorial.md)