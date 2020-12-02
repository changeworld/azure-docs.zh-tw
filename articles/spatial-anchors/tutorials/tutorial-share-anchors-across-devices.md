---
title: 教學課程：跨工作階段和裝置共用錨點
description: 在本教學課程中，您將了解如何在 Unity 中透過後端服務於 Adroid/iOS 裝置之間共用 Azure Spatial Anchors 識別碼。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3048eb1ca7f9312a43bc1ab5885bf19d4c2e7931
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185284"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>教學課程：跨工作階段和裝置共用空間錨點

Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 

在本教學課程中，您將使用 [Azure Spatial Anchors](../overview.md) 在一個工作階段中建立錨點，然後在相同或不同裝置上找到這些錨點。 相同的錨點也可由多個裝置在相同的位置同時找到。

![此動畫顯示使用行動裝置建立，並在過去幾天內與不同裝置搭配使用的空間錨點。](./media/persistence.gif)


在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 中部署可用來共用錨點，並將錨點儲存至記憶體達指定期間的 ASP.NET Core Web 應用程式。
> * 在快速入門中的 Unity 範例內設定 AzureSpatialAnchorsLocalSharedDemo 場景，以使用「共用錨點」Web 應用程式。
> * 將錨點部署至一或多個裝置並執行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> 您在本教學課程中將使用 Unity 和 ASP.NET Core Web 應用程式，但此方法只為了提供範例來說明如何與其他裝置共用 Azure Spatial Anchor 識別碼。 您可以使用其他語言和後端技術達到相同的目標。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>下載範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>部署共用錨點服務

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

開啟 Visual Studio，然後開啟 *Sharing\SharingServiceSample* 資料夾中的專案。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

在 Visual Studio Code 中部署服務之前，必須先建立資源群組和 App Service 方案。

### <a name="sign-in-to-azure"></a>登入 Azure

移至 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站</a>，然後登入您的 Azure 訂用帳戶。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

選取 [資源群組] 旁邊的 [新增]。

將資源群組命名為 **myResourceGroup**，然後選取 [確定]。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

選取 [主控方案]旁的 [新增]。

在 [設定主控方案] 窗格中，使用以下設定：

| 設定 | 建議的值 | 描述 |
|-|-|-|
|App Service 方案| MySharingServicePlan | App Service 方案的名稱 |
| Location | 美國西部 | 裝載 Web 應用程式的資料中心 |
| 大小 | 免費 | 決定裝載功能的[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |

選取 [確定]  。

開啟 Visual Studio Code，然後開啟 *Sharing\SharingServiceSample* 資料夾中的專案。 

若要透過 Visual Studio Code 部署共用服務，請依照<a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">使用 Visual Studio Code 將 ASP.NET Core 應用程式發佈至 Azure</a> 中的指示操作。 請從「使用 Visual Studio Code 加以開啟」一節開始。 請勿建立另一個 ASP.NET 專案 (如上述步驟所述)，因為您已有要部署和發佈的專案：SharingServiceSample。

---

## <a name="deploy-the-sample-app"></a>部署範例應用程式

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Azure 中部署 ASP.NET Core Web 應用程式，且已設定並部署 Unity 應用程式。 您使用應用程式建立了空間錨點，並使用 ASP.NET Core Web 應用程式與其他裝置共用這些錨點。

您可以改善 ASP.NET Core Web 應用程式，使其能夠使用 Azure Cosmos DB 持續儲存共用的空間錨點識別碼。 藉由新增 Azure Cosmos DB 支援，您可以讓 ASP.NET Core Web 應用程式立即建立錨點。 然後，藉由使用儲存在 Web 應用程式中的錨點識別碼，您可以讓應用程式在數天後返回以重新尋找錨點。

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 儲存錨點](./tutorial-use-cosmos-db-to-store-anchors.md)