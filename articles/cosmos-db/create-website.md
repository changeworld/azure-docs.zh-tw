---
title: 使用範本部署 Web 應用程式 - Azure Cosmos DB
description: 瞭解如何使用 Azure Resource Manager 範本來部署 Azure Cosmos 帳戶、Azure App Service Web Apps 和範例 Web 應用程式。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 5038d9968e37b956774d1c5f8abdb14865422e8b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027742"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本，從 GitHub 透過 web 應用程式部署 Azure Cosmos DB 和 Azure App Service

本教學課程會示範如何在第一次執行時，執行連接到 Azure Cosmos DB 的 web 應用程式「不需要觸控」部署，而不需要將任何連接資訊從 Azure Cosmos DB 剪下並貼 `appsettings.json` 到 Azure 入口網站中的 Azure App Services 應用程式設定。 所有這些動作都是在單一作業中使用 Azure Resource Manager 範本來完成。 在這裡的範例中，我們將從[Web 應用程式教學](sql-api-dotnet-application.md)課程部署[Azure Cosmos DB ToDo 範例](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)。

Resource Manager 範本都相當有彈性，可讓您在 Azure 中的任何服務上撰寫複雜的部署。 這包括像是從 GitHub 部署應用程式，以及將連接資訊插入 Azure 入口網站中 Azure App Service 的應用程式設定等的先進工作。 本教學課程將示範如何使用單一 Resource Manager 範本來執行下列動作。

* 部署 Azure Cosmos 帳戶。
* 部署 Azure App Service 的主控方案。
* 部署 Azure App Service。
* 將 Azure Cosmos 帳戶中的端點和金鑰插入 Azure 入口網站中的 App Service 應用程式設定。
* 將 web 應用程式從 GitHub 存放庫部署到 App Service。

產生的部署具有功能完整的 web 應用程式，可以連接到 Azure Cosmos DB，而不需要從 Azure 入口網站剪下並貼上 Azure Cosmos DB 的端點 URL 或驗證金鑰。

## <a name="prerequisites"></a>必要條件

> [!TIP]
> 雖然本教學課程沒有假設您先前已有使用 Azure Resource Manager 範本或 JSON 的經驗，但是，如果您需要修改參考的範本或部署選項，還是需要每個領域的知識。

## <a name="step-1-deploy-the-template"></a>步驟1：部署範本

首先，請選取下方的 [**部署至 Azure** ] 按鈕，以開啟 Azure 入口網站來建立自訂部署。 您也可以從[Azure 快速入門範本資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)中查看 Azure 資源管理範本

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="部署至 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

在 [Azure 入口網站] 中，選取要部署到的訂用帳戶，然後選取或建立新的資源群組。 然後填入下列值。

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="範本部署 UI 的螢幕擷取畫面":::

* **區域**-這是 Resource Manager 所需的。 輸入您的資源所在位置參數所使用的相同區域。
* **應用程式名稱**-這個部署的所有資源都會使用此名稱。 請務必選擇唯一的名稱，以避免與現有的 Azure Cosmos DB 和 App Service 帳戶發生衝突。
* **位置**-您的資源部署所在的區域。
* **App Service 計畫層**級-App Service 方案的定價層。
* **App Service 方案實例**-應用程式服務方案的背景工作數目。
* 存放**庫 URL** -GitHub 上 web 應用程式的存放庫。
* **分支**-GitHub 存放庫的分支。
* **資料庫名稱**-Azure Cosmos 資料庫名稱。
* **容器名稱**-Azure Cosmos 容器名稱。

填入值之後，請選取 [**建立**] 按鈕以開始部署。 此步驟應需要5到10分鐘的時間才能完成。

> [!TIP]
> 範本不會驗證在範本中輸入的 Azure App Service 名稱和 Azure Cosmos 帳戶名稱是否有效且可供使用。 強烈建議您先確認打算提供之名稱的可用性，再提交部署。


## <a name="step-2-explore-the-resources"></a>步驟2：探索資源

### <a name="view-the-deployed-resources"></a>查看已部署的資源

在範本部署資源之後，您現在可以在資源群組中看到它們。

:::image type="content" source="./media/create-website/resource-group.png" alt-text="資源群組":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>查看 Cosmos DB 端點和金鑰

接下來，在入口網站中開啟 Azure Cosmos 帳戶。 下列螢幕擷取畫面顯示 Azure Cosmos 帳戶的端點和金鑰。

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos 索引鍵":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>在應用程式設定中查看 Azure Cosmos DB 金鑰

接下來，流覽至資源群組中的 [Azure App Service]。 按一下 [設定] 索引標籤，以查看 App Service 的應用程式設定。 應用程式設定包含連接到 Cosmos DB 所需的 Cosmos DB 帳戶和主要金鑰值，以及從範本部署傳入的資料庫和容器名稱。

:::image type="content" source="./media/create-website/application-settings.png" alt-text="應用程式設定":::

### <a name="view-web-app-in-deployment-center"></a>在部署中心內查看 web 應用程式

接下來，移至 App Service 的 [部署中心]。 您會在這裡看到存放庫指向範本的 GitHub 儲存機制。 下列狀態也會指出 [成功] （作用中），表示應用程式已成功部署並啟動。

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="部署中心":::

### <a name="run-the-web-application"></a>執行 Web 應用程式

按一下 [部署中心] 頂端的 **[流覽]** 以開啟 web 應用程式。 Web 應用程式將會開啟到主畫面。 按一下 [**新建]，並**在欄位中輸入一些資料，然後按一下 [儲存]。 產生的畫面會顯示儲存到 Cosmos DB 的資料。

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="主畫面":::

## <a name="step-3-how-does-it-work"></a>步驟3：運作方式

此作業需要三個元素。

### <a name="reading-app-settings-at-runtime"></a>在執行時間讀取應用程式設定

首先，應用程式必須在 `Startup` ASP.NET MVC web 應用程式的類別中要求 Cosmos DB 端點和金鑰。 [Cosmos DB To Do 範例](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)可以在本機執行，您可以在此輸入連接資訊到 appsettings.js。 不過，部署時，此檔案會與應用程式一起部署。 如果這些紅色行無法從上的 appsettings.js存取設定，它會嘗試從 Azure App Service 中的應用程式設定。

:::image type="content" source="./media/create-website/startup.png" alt-text="啟動":::

### <a name="using-special-azure-resource-management-functions"></a>使用特殊的 Azure 資源管理功能

若要讓這些值可供應用程式在部署時使用，Azure Resource Manager 範本可以使用特殊的 Azure 資源管理功能（包括[參考](../azure-resource-manager/templates/template-functions-resource.md#reference)和[listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys) ）從 Cosmos DB 帳戶要求這些值，以從 Cosmos DB 帳戶抓取值，並將其插入至應用程式設定值，其索引鍵名稱與上述在 ' {section： key} ' 格式中使用的內容相符。 例如： `CosmosDb:Account` 。

:::image type="content" source="./media/create-website/template-keys.png" alt-text="範本金鑰":::

### <a name="deploying-web-apps-from-github"></a>從 GitHub 部署 web 應用程式

最後，我們需要從 GitHub 將 web 應用程式部署到 App Service。 這會使用下列 JSON 來完成。 有兩個要注意的事項是此資源的類型和名稱。 `"type": "sourcecontrols"`和 `"name": "web"` 屬性值都是硬式編碼，不應變更。

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="從 GitHub 部署":::

## <a name="next-steps"></a>後續步驟

恭喜！ 您已部署 Azure Cosmos DB、Azure App Service 和範例 web 應用程式，它會自動具有連接到 Cosmos DB 所需的連線資訊，全都在單一作業中，而且不需要剪下和貼上機密資訊。 使用此範本做為起點，您可以修改它以相同方式部署您自己的 web 應用程式。

* 如需此範例的 Azure Resource Manager 範本，請移至[Azure 快速入門範本資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* 如需範例應用程式的原始程式碼，請移至[GitHub 上的 Cosmos DB 至執行應用程式](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)。
