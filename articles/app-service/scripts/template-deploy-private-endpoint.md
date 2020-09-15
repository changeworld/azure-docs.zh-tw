---
title: 使用 Azure 資源管理員範本來部署 Web 應用程式的私人端點
description: 了解如何使用 ARM 範本為您的 Web 應用程式部署私人端點。
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652020"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立 App Service 應用程式和部署私人端點

在本快速入門中，您將使用 Azure Resource Manager (ARM) 範本建立 Web 應用程式並與私人端點公開。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>必要條件

您需要具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-private-endpoint"></a>建立私人端點

此範本會建立 Azure Web 應用程式執行的私人端點。

### <a name="review-the-template"></a>檢閱範本

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>部署範本

以下是將 Azure Resource Manager 範本部署至 Azure 的方法：

1. 若要登入 Azure 並開啟範本，請選取此連結：[部署至 Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)。 此範本會建立虛擬網路、Web 應用程式、私人端點和私人 DNS 區域。
2. 選取或建立資源群組。
3. 輸入 Web 應用程式的名稱、Azure App Service 方案和私人端點。
5. 閱讀條款及條件聲明。 若同意，請選取 [我同意上方所述的條款及條件] > [購買]。 部署需要幾分鐘才能完成。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為私人端點建立的資源時，請刪除資源群組。 這會移除私人端點和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

- 您可以在 [ARM 範本範例](../samples-resource-manager-templates.md)中找到適用於 Azure App Service Web Apps 的其他 Azure Resource Manager 範本。
