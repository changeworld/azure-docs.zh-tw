---
title: 快速入門 - 使用 ARM 範本建立 Azure API 管理執行個體
description: 了解如何使用 Azure Resource Manager 範本 (ARM 範本)，在開發人員層中建立 Azure API 管理執行個體。
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792237"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>快速入門：使用 ARM 範本建立新的 Azure API 管理服務執行個體

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure API 管理 (APIM) 服務執行個體。 APIM 可協助組織將 API 發佈給外部、合作夥伴和內部開發人員，以發揮其資料與服務的潛在效益。 「API 管理」提供的核心專長認證，透過開發人員參與、商務洞察力、分析、安全性和保護，可確保 API 程式獲致成功。 APIM 可讓您為裝載於任何位置的現有後端服務，建立和管理新式 API 閘道。 如需詳細資訊，請參閱[概觀](api-management-key-concepts.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/)。

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

範本中已定義下列資源：

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

在 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular)中，可找到更多 Azure API 管理範本範例。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立 API 管理服務執行個體，其具有自動產生的名稱。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    在此範例中，執行個體設定於開發人員層，此經濟實惠的選項用以評估 Azure API 管理。 此階層不適用於生產環境。 如需有關調整 API 管理層規模的詳細資訊，請參閱[升級和調整規模](upgrade-and-scale.md)。

1. 選取或輸入下列值。
    - **訂用帳戶** ：選取 Azure 訂用帳戶。
    - **資源群組** ：選取 [新建]，輸入資源群組的唯一名稱，然後選取 [確認]。
    - **區域** ：選取資源群組的位置。 範例： **美國中部** 。
    - **發行者電子郵件** ：輸入電子郵件地址以接收通知。
    - **發行者名稱** ：輸入您為 API 發行者選擇的名稱。
    - **Sku** ：接受 [開發人員] 的預設值。
    - **Sku 計數** ：接受預設值。
    - **位置** ：接受針對 API 管理服務產生的位置。

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="API 管理範本屬性":::

1. 選取 [檢閱 + 建立]，然後檢閱條款及條件。 如果您同意，請選取 [建立]。

    > [!TIP]
    >  這可能需要 30 到 40 分鐘的時間，才能在開發人員層中建立及啟用 API 管理服務。

1. 成功部署執行個體之後，您會收到通知：

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="API 管理範本屬性":::

 Azure 入口網站用於部署範本。 除了 Azure 入口網站以外，您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-cli.md)。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

使用 Azure 入口網站來檢查已部署的資源，或使用 Azure CLI 或 Azure PowerShell 之類的工具來列出已部署的資源。

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [API 管理服務]，然後選取您建立的服務執行個體。
1. 在 [概觀] 頁面上，檢閱服務的屬性。

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="API 管理範本屬性":::

當您的 APIM 服務執行個體上線時，您就可以開始使用了。 從[匯入和發佈第一個 API](import-and-publish.md) 教學課程開始。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行後續的教學課程，您可以讓 API 管理執行個體留在原處。 如果不再需要，請刪除資源群組，這會刪除資源群組中的資源。

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [資源群組]。 您也可以在  。
1. 在 [資源群組]  頁面中，選取您的資源群組。
1. 在 [資源群組] 頁面中，選取 [刪除資源群組]  。

    刪除資源群組
1. 輸入您的資源群組名稱，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：匯入和發佈您的第一個 API](import-and-publish.md)
