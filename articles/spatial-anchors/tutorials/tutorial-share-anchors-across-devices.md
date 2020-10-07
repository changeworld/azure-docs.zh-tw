---
title: 教學課程：跨工作階段和裝置共用錨點
description: 在本教學課程中，您將了解如何在 Unity 中透過後端服務於 Adroid/iOS 裝置之間共用 Azure Spatial Anchors 識別碼。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8b6c3608165ed592cc2f0daf475226c9d35de012
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358750"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>教學課程：跨工作階段和裝置來共用 Azure Spatial Anchors

在本教學課程中，您將了解如何使用 [Azure Spatial Anchors](../overview.md) 在一個工作階段中建立錨點，然後在相同或不同裝置上找到這些錨點。 這些相同的錨點也可由多個裝置在相同的位置同時找到。

![動畫顯示使用行動裝置建立的 Azure 空間錨點，並在過去幾天內與不同的裝置搭配使用。](./media/persistence.gif)

Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有可部署至兩個或更多裝置的應用程式。 一個執行個體所建立的 Azure Spatial Anchors，將可與其他執行個體共用。

您將學習如何：

> [!div class="checklist"]
> * 在 Azure 中部署可用來共用錨點並將其儲存至記憶體一段時間的 ASP.NET Core Web 應用程式。
> * 在快速入門中的 Unity 範例內設定 AzureSpatialAnchorsLocalSharedDemo 場景，以使用「共用錨點」Web 應用程式。
> * 部署到一或多個裝置並執行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

應注意的是，雖然您在本教學課程中將使用 Unity 和 ASP.NET Core Web 應用程式，但這只為了提供範例來說明如何與其他裝置共用 Azure Spatial Anchor 識別碼。 您可以使用其他語言和後端技術達到相同的目標。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>下載範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>部署共用錨點服務

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

開啟 Visual Studio，並開啟 `Sharing\SharingServiceSample` 資料夾上的專案。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

在 VS Code 中部署服務之前，必須先建立資源群組和 App Service 方案。

### <a name="sign-in-to-azure"></a>登入 Azure

瀏覽至 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站</a>，然後登入您的 Azure 訂用帳戶。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

選取 [資源群組]**** 旁邊的 [新增]****。

將資源群組命名為 **myResourceGroup**，然後選取 [確定]****。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

選取 [主控方案]**** 旁的 [新增]****。

在 [設定主控方案]**** 對話方塊中，使用以下設定：

| 設定 | 建議的值 | 說明 |
|-|-|-|
|App Service 方案| MySharingServicePlan | App Service 方案的名稱。 |
| Location | 美國西部 | 裝載 Web 應用程式的資料中心。 |
| 大小 | 免費 | 決定裝載功能的[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 |

選取 [確定]。

開啟 Visual Studio Code，並開啟 `Sharing\SharingServiceSample` 資料夾上的專案。 請依照<a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">本教學課程</a>的說明，透過 Visual Studio Code 部署共用服務。 您可以遵循〈使用 Visual Studio Code 開啟〉一節開頭的步驟。 請勿建立另一個 ASP.NET 專案 (如上述步驟所述)，因為您已經有需要部署和發佈的專案 - SharingServiceSample。

---

## <a name="deploy-the-sample-app"></a>部署範例應用程式

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Azure 中部署 ASP.NET Core Web 應用程式，然後設定並部署 Unity 應用程式。 您使用應用程式建立了 Spatial Anchors，並使用 ASP.NET Core Web 應用程式與其他裝置共用這些錨點。

您可以改善 ASP.NET Core Web 應用程式，使它能夠使用 Azure Cosmos DB 持續儲存共用的 Spatial Anchors 識別碼。 新增 Azure Cosmos DB 支援可以讓 ASP.NET Core Web 應用程式在今天建立一個錨點，幾天後回來時還能使用儲存在 Web 應用程式中的錨點識別碼再次找到該錨點。

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 儲存錨點](./tutorial-use-cosmos-db-to-store-anchors.md)

