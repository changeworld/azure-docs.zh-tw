---
title: 快速入門 - 建立異地複寫登錄 - Azure Resource Manager 範本
description: 了解如何使用 Azure Resource Manager 範本建立異地複寫的 Azure 容器登錄。
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: 2cfce37ff63a8321f40843ced2a7b786bcfc013e
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649596"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>快速入門：使用 ARM 範本建立異地複寫的容器登錄

本快速入門示範如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure 容器登錄執行個體。 此範本會設定[異地複寫](container-registry-geo-replication.md)登錄，自動同步處理多個 Azure 區域的登錄內容。 異地複寫可讓您從區域部署對映像進行網路封閉存取，同時提供單一的管理體驗。 這是[進階](container-registry-skus.md)登錄服務層級的一項功能。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/)。 此範本會設定一項登錄和一個額外的區域複本。

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

範本中會定義下列資源：

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** ：建立 Azure 容器登錄
* **[Microsoft.ContainerRegistry/registries/replications](/azure/templates/microsoft.containerregistry/registries/replications)** ：建立容器登錄複本

如需更多 Azure 容器登錄範本範例，請參閱[快速入門範本資源庫](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署範本

 1. 選取以下影像來登入 Azure 並開啟範本。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. 選取或輸入下列值。

    * **訂用帳戶**：選取 Azure 訂用帳戶。
    * **資源群組**：選取 [新建]，輸入資源群組的唯一名稱，然後選取 [確認]。
    * **位置**：選取資源群組的位置。 範例：**美國中部**。
    * **Acr 名稱**：接受為登錄所產生的名稱，或輸入名稱。 此名稱必須是全域唯一的。
    * **位置**：接受為登錄首頁複本所產生的位置，或輸入位置，例如**美國中部**。 
    * **Acr 複本位置**：使用區域的簡短名稱輸入登錄複本的位置。 必須和首頁的登錄位置不同。 範例：**westeurope**。
    * **我同意上方所述的條款及條件**：選取。

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="範本屬性":::

 3. 如果接受這些條款與條件，請選取 [購買]。 成功建立登錄之後，您會收到一則通知：

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="入口網站通知":::

 Azure 入口網站用於部署範本。 除了 Azure 入口網站，您可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-cli.md)。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

使用 Azure 入口網站或 Azure CLI 之類的工具來檢閱容器登錄的屬性。

1. 在入口網站中，搜尋容器登錄，然後選取您建立的容器登錄。

1. 記下 [概觀] 頁面中，登錄的**登入伺服器**。 當您使用 Docker 標記映像並將其推送至您的登錄時，請使用此 URI。 如需相關資訊，請參閱[使用 Docker CLI 推送您的第一個映像](container-registry-get-started-docker-cli.md)。

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="登錄概觀":::

1. 在 [複寫] 頁面上，確認主要複本和透過範本所新增複本的位置。 如有需要，請在此頁面中新增更多複本。

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="登錄複寫":::

## <a name="clean-up-resources"></a>清除資源

您可以刪除不再需要的資源群組、登錄和登錄複本。 若要這樣做，請移至 Azure 入口網站，選取包含登錄的資源群組，然後選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 ARM 範本建立了 Azure 容器登錄，並在另一個位置設定了登錄複本。 請繼續進行 Azure 容器登錄教學課程，以深入了解 ACR。

> [!div class="nextstepaction"]
> [Azure Container Registry 教學課程](container-registry-tutorial-prepare-registry.md)

如需逐步教學課程，以引導您完成建立範本的流程，請參閱：

> [!div class="nextstepaction"]
> [教學課程：建立及部署您的第一個 ARM 範本](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
