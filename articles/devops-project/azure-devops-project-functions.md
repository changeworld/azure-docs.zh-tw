---
title: 教學課程：使用 Azure DevOps 入門版將 ASP.NET 應用程式部署到 Azure Functions
description: Azure DevOps 入門版可供輕鬆地開始使用 Azure。 透過 Azure DevOps 入門版，您可以藉由幾個快速步驟將 ASP.NET 應用程式部署至 Azure Functions。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355469"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>使用 DevOps 入門版部署到 Azure Functions

Azure DevOps 入門版提供簡化的體驗，其可供自備現有程式碼和 Git 存放庫，或選擇一個範例應用程式，以在 Azure 中建立持續整合 (CI) 和持續傳遞 (CD) 管線。

DevOps 入門版也能夠：

* 自動建立 Azure 資源，例如 Azure Functions

* 在 Azure DevOps 中建立並設定發行管線以實現 CI/CD

在本教學課程中，您將：

> [!div class="checklist"]
>* 使用 DevOps 入門版將 ASP.NET 應用程式部署到 Azure Function
>* 設定 Azure DevOps 與 Azure 訂用帳戶
>* 檢查 Azure Function
>* 檢查 CI 管線
>* 檢查 CD 管線
>* 將變更認可至 Git 並自動部署至 Azure
>* 清除資源

目前支援的函式執行階段是 **.NET** 和 **Node.js**。 我們使用 . NET 執行階段在此教學課程中部署至 Azure Functions。 

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 您可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得免費的訂用帳戶

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>使用 DevOps 入門版將 ASP.NET 應用程式部署到 Azure Functions

DevOps 入門版會在 Azure Pipelines 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 DevOps Projects 也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源，例如 IoT 中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com)

1. 在搜尋方塊中，鍵入並選取 **DevOps 入門版**。 按一下 [新增] 以建立新項目。

    ![DevOps 入門版儀表板](_img/azure-devops-starter-aks/search-devops-starter.png)

1. 選取 [.NET]，然後選取 [下一步]。 在 [選擇應用程式架構] 之下，選取 [ASP.NET]，然後按 [下一步]。

1. 選取 [函式應用程式]，然後選取 [下一步]。

## <a name="configure-azure-devops-and-azure-subscription"></a>設定 Azure DevOps 與 Azure 訂用帳戶

1. 為您的 Azure DevOps 專案輸入名稱。

1. 建立新的 Azure DevOps 組織或選取現有組織。

1. 選取 Azure 訂用帳戶。

1. 若要查看其他 Azure 組態設定及識別定價層和位置，請按一下 [其他設定]。 此窗格會顯示各種選項，可用來設定 Azure 服務的定價層和位置。

1. 退出 Azure 組態區域，然後選取 [完成]。

1. 程序完成需要幾分鐘的時間。 系統會在 Azure DevOps 組織的 Git 存放庫中設定 ASP.NET 應用程式範例、建立函式應用程式和 Application Insights、執行 CI/CD 管線，然後將應用程式部署至 Azure。

   當這些動作都完成後，Azure 入口網站中隨即會顯示 Azure DevOps 入門版儀表板。 您可直接從 Azure 入口網站中的 [所有資源] 移至 DevOps 入門版儀表板。

   此儀表板會顯示您的 Azure DevOps 程式碼存放庫、CI/CD 管線和 Azure Function。 您可以在 Azure DevOps 管線中設定其他 CI/CD 選項。 從右側選取要檢視的 [函式應用程式]。

## <a name="examine-the-function-app"></a>檢查函式應用程式

DevOps 入門版會自動設定函式應用程式，且您可加以瀏覽和自訂。 若要了解函式應用程式，請執行下列動作：

1. 前往 DevOps 入門版儀表板。

    ![DevOps Projects 儀表板](_img/azure-devops-project-functions/fapp-dashboard.png)

1. 從右側選取函式應用程式。 隨即會開啟函式應用程式的窗格。 在此檢視中，您可以執行各種動作，例如作業監視和搜尋記錄等等。

    ![函式應用程式](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>檢查 CI 管線

DevOps 入門版會在 Azure DevOps 組織中自動設定 CI/CD 管線。 您可以瀏覽及自訂管線。 若要讓您自己熟悉管線，請執行下列作業：

1. 前往 DevOps 入門版儀表板。

1. 按一下 [建置] 底下的超連結。 瀏覽器索引標籤會顯示新專案的建置管線。

    ![此螢幕擷取畫面顯示 Azure 入口網站 DevOps 入門版儀表板，其中有一個箭號指向 C I/C D 管線中 [組建] 底下的連結。](_img/azure-devops-project-functions/build.png)

1. 選取 [編輯]。 在此窗格中，您可以檢查建置管線的各種工作。 建置會執行各種工作，例如從 Git 存放庫擷取來源程式碼、建置應用程式、執行單元測試，以及發佈用來進行部署的輸出。

1. 選取 [觸發程序]。 DevOps 入門版已自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新組建。 您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

1. 選取 [保留期]。 根據案例，您可以指定原則來保留或移除特定數目的組建。

1. 在建置管線的頂端，選取建置管線名稱。

1. 將建置管線的名稱變更成較具描述性的名稱，並從 [儲存並排入佇列] 下拉式清單中選取 [儲存]。

1. 在建置管線名稱下，選取 [記錄]。 此窗格會顯示近期建置變更的稽核線索。 Azure DevOps 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

## <a name="examine-the-cd-release-pipeline"></a>檢查 CD 發行管線

DevOps 入門版會自動建立並設定必要的步驟，以從 Azure DevOps 組織部署至 Azure 訂用帳戶。 這些步驟包括設定 Azure 服務連線，以對 Azure 訂用帳戶驗證 Azure DevOps。 自動化也會建立發行管線，以將 CD 提供給 Azure。 若要深入了解發行管線，請執行下列動作：

1. 流覽至 [管線] | [發行]。

1. 按一下 [編輯]。

1. 在 [成品] 下，選取 [置放]。 您在先前步驟中檢查的建置管線會產生用於成品的輸出。

1. 在 [置放] 圖示的右側，選取 [持續部署觸發程序]。 這個發行管線已啟用 CD 觸發程序，每次有新的建置成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。

1. 從右側選取 [檢視版本]，以顯示版本的歷程記錄。

1. 按一下 [發行] 以顯示管線。 按一下任何環境，以檢查與**工作項目**相關聯的 [摘要] 和 [認可]。

1. 選取 [認可]。 此檢視會顯示與此部署相關聯的程式碼認可。 請比較版本，以查看部署的認可差異。

1. 選取 [檢視記錄]。 記錄包含關於部署程序的實用資訊。 您可以在部署期間和部署之後加以檢視。

## <a name="commit-code-changes-and-execute-cicd"></a>認可程式碼變更並執行 CI/CD

> [!NOTE]
> 下列程序會進行簡單的文字變更來測試 CI/CD 管線。

您現在已準備好要使用 CI/CD 程序來與小組共同對應用程式執行作業，並將您最新的工作自動部署至 Azure Function。 對 Git 存放庫進行的每個變更都會在 Azure DevOps 中起始一個建置，且 CD 管線會對 Azure 執行部署。 請遵循本節中的程序，或使用另一個方法來認可存放庫的變更。 例如，您可以將 Git 存放庫複製到您最常用的工具或 IDE 中，然後推送此存放庫的變更。

1. 在 [Azure DevOps] 功能表中，選取 [存放庫] | [檔案]，然後移至您的存放庫。

1. 根據您在建立程序中所選擇的應用程式語言，存放庫已經包含名為 **SampleFunctionApp** 的程式碼。 開啟 **Application/SampleFunctionApp/Function1.cs** 檔案。

1. 選取 [編輯]，並對**行號 31** 進行變更。 例如，您可以將其更新為**您好！歡迎搭配 DevOps 入門版使用 Azure Functions**

1. 在右上方選取 [認可]，然後再次選取 [認可]來推送您的變更。

1. 開啟 **Application/SampleFunctionApp.Test/Function1TestRunner.cs** 檔案。 

1. 選取 [編輯]，並對**行號 21** 進行變更。 例如，您可以將其更新為**您好！歡迎搭配 Azure DevOps 入門版使用 Azure Functions**。

     不久之後，會在 Azure DevOps 中開始建置並執行發行以部署變更。 在 DevOps 入門版儀表板上或在瀏覽器中使用 Azure DevOps 組織來監視組建狀態。

## <a name="clean-up-resources"></a>清除資源

當您不再需要所建立的相關資源時，可將其刪除。 請使用 DevOps 入門版儀表板的 [刪除] 功能。

## <a name="next-steps"></a>後續步驟

您可以選擇性地修改這些組建和發行管線，以符合小組的需求。 您也可以使用此 CI/CD 模式作為其他管線的範本。 在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 DevOps 入門版將 ASP.NET Core 應用程式部署到 Azure Function
> * 設定 Azure DevOps 與 Azure 訂用帳戶 
> * 檢查 Azure Function
> * 檢查 CI 管線
> * 檢查 CD 管線
> * 將變更認可至 Git 並自動部署至 Azure
> * 清除資源

