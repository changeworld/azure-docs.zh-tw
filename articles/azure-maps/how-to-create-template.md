---
title: 使用 ARM 範本建立您的 Azure 地圖服務帳戶 |Microsoft Azure 對應
description: 瞭解如何使用 Azure Resource Manager (ARM) 範本建立 Azure 地圖服務帳戶。
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92525077"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>使用 ARM 範本建立您的 Azure 地圖服務帳戶

您可以使用 Azure Resource Manager (ARM) 範本來建立 Azure 地圖服務帳戶。 在取得帳戶後，您即可在網站或行動裝置應用程式中實作 API。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

若要完成此文章：

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-maps-create/)。

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

此範本中定義了 Azure 地圖服務帳戶資源：

* [**Microsoft. Maps/accounts**](/azure/templates/microsoft.maps/accounts)：建立 Azure 地圖服務帳戶。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 範本會建立 Azure 地圖服務帳戶。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. 選取或輸入下列值。

    ![ARM 範本部署入口網站](./media/how-to-create-template/create-account-using-template-portal.png)

    除非已指定，否則請使用預設值來建立您的 Azure 地圖服務帳戶。

    * **訂用帳戶**：選取 Azure 訂用帳戶。
    * [資源群組]選取 [新建]，輸入資源群組的唯一名稱，然後按一下 [確認]。
    * **位置**：選取位置。 例如， **美國西部 2**。
    * **帳戶名稱**：輸入您的 Azure 地圖服務帳戶的名稱，此名稱必須是全域唯一的。
    * **定價層**：選取適當的定價層，範本的預設值為 S0。

3. 選取 [檢閱 + 建立]。 
4. 在 [審核] 頁面上確認您的設定，然後按一下 [ **建立**]。 成功部署您的 Azure 地圖服務之後，您會收到通知：

    ![ARM 範本部署入口網站通知](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Azure 入口網站用來部署您的範本。 您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

您可以使用 Azure 入口網站來檢查 Azure 地圖服務帳戶並查看您的金鑰。 您也可以使用下列 Azure CLI 腳本來列出您的帳戶金鑰。

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除資源群組，這也會刪除 Azure 地圖服務帳戶。 使用 Azure CLI 來刪除資源群組：

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 地圖服務和 Azure Resource Manager，請繼續閱讀下列文章。

- 建立 Azure 地圖服務 [示範應用程式](quick-demo-map-app.md)
- 深入瞭解 [ARM 範本](../azure-resource-manager/templates/overview.md)