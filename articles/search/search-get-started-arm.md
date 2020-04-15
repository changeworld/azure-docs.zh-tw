---
title: 使用 Azure Resource Manager 範本部署您的服務。
titleSuffix: Azure Cognitive Search
description: 您可使用 Azure Resource Manager 範本快速部署 Azure 認知搜尋服務執行個體。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80611812"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>快速入門：使用 Resource Manager 範本部署認知搜尋

本文會逐步引導您完成使用 Resource Manager 範本在 Azure 入口網站中部署 Azure 認知搜尋資源的程序。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-cognitive-search-service"></a>建立認知搜尋服務

### <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 範本](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices)。

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

此範本中定義的 Azure 資源：

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices)：建立 Azure 認知搜尋服務

### <a name="deploy-the-template"></a>部署範本

選取以下影像來登入 Azure 並開啟範本。 此範本會建立 Azure 認知搜尋資源。

[![部署至 Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

入口網站會顯示一個表單，讓您輕鬆地提供參數值。 有些參數會預先填入範本中的預設值。 您必須提供您的訂用帳戶、資源群組、位置和服務名稱。 如果您想要在 [AI 擴充](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)管線中使用認知服務，例如用於分析二進位影像檔案中的文字，請選擇同時提供認知搜尋和認知服務的位置。 這兩項服務必須位於相同的區域中，才能處理 AI 擴充工作負載。 完成表單後，您必須同意條款與條件，然後選取購買按鈕來完成部署。

> [!div class="mx-imgBorder"]
> ![Azure 入口網站顯示的範本](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>檢閱已部署的資源

部署完成時，您可以在入口網站中存取新的資源群組和新的搜尋服務。

## <a name="clean-up-resources"></a>清除資源

其他認知搜尋快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓此資源留在原處。 如果不再需要，您可刪除資源群組，這會刪除認知搜尋服務和相關資源。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure Resource Manager 範本建立了認知搜尋服務，並已驗證部署。 若要深入了解認知搜尋和 Azure Resource Manager，請繼續閱讀下列文章。

 - 閱讀 [Azure 認知搜尋的概觀](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - 為您的搜尋服務[建立索引](https://docs.microsoft.com/azure/search/search-get-started-portal)
 - 使用入口網站精靈[建立搜尋應用程式](https://docs.microsoft.com/azure/search/search-create-app-portal)
 - [建立技能集](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)以從您的資料擷取資訊


