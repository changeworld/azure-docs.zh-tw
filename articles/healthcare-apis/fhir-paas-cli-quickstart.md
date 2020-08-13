---
title: 快速入門：使用 Azure CLI 部署 Azure API for FHIR
description: 在本快速入門中，您將了解如何使用 Azure CLI 在 Azure 中部署 Azure API for FHIR。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: e1aa3362217a4edcfcf547c69b9effde00459f7e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850524"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>快速入門：使用 Azure CLI 部署 Azure API for FHIR

在本快速入門中，您將了解如何使用 Azure CLI 在 Azure 中部署 Azure API for FHIR。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>新增 HealthcareAPIs 擴充功能

```azurecli-interactive
az extension add --name healthcareapis
```

取得 HealthcareAPIs 的命令清單：

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>建立 Azure 資源群組

挑選將包含 Azure API for FHIR 的資源群組名稱並加以建立：

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>部署 Azure API for FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>擷取 FHIR API 功能陳述式

從 FHIR API 取得功能陳述式：

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除資源群組：

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門手冊中，您已將 Azure API for FHIR 部署至您的訂用帳戶。 若要在 Azure API for FHIR 中設定其他設定，請繼續進行其他設定操作指南。

>[!div class="nextstepaction"]
>[Azure API for FHIR 中的其他設定](azure-api-for-fhir-additional-settings.md)
