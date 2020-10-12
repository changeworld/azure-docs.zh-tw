---
title: 具有 Azure DevOps 入門 Azure IoT Edge 的 CI/CD 管線 |Microsoft Docs
description: Azure DevOps 入門版可供輕鬆地開始使用 Azure。 它可協助您透過幾個簡單的步驟啟動您所選擇的 Azure IoT Edge 應用程式。
author: shizn
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f0f0452854ef7456d1bdf7caf0674fab5bd14557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297125"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>使用 Azure DevOps Starter 建立 IoT Edge 的 CI/CD 管線

使用 DevOps Projects 為您的 IoT Edge 應用程式設定持續整合 (CI) 與持續傳遞 (CD)。 DevOps Starter 可簡化 Azure Pipelines 中建置與發行管線的初始設定。

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

DevOps 入門版會在 Azure DevOps 中建立 CI/CD 管線。 您可以建立新的 Azure DevOps 組織或使用現有組織。 DevOps 入門版也會在您選擇的 Azure 訂用帳戶中建立 Azure 資源。

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。

1. 在左窗格中，選取 [ **建立資源**]，然後搜尋 **DevOps Starter**。  

1. 選取 [建立]。

## <a name="create-a-new-application-pipeline"></a>建立新的應用程式管道

1. 您的 Azure IoT Edge 模組可使用 [C#](tutorial-csharp-module.md)、[Node.js](tutorial-node-module.md)、[Python](tutorial-python-module.md)、[C](tutorial-c-module.md) 和 [Java](tutorial-java-module.md) 來撰寫。 選取您的慣用語言來啟動新的應用程式：[.NET]、[Node.js]、[Python]、[C]，或 [Java]。 選取 [下一步] 以繼續操作。

   ![選取用來新建應用程式的語言](./media/how-to-devops-starter/select-language.png)

2. 選取 [Simple IoT] 作為應用程式架構，然後選取 [下一步]。

   ![選取 Simple IoT 架構](media/how-to-devops-starter/select-iot.png)

3. 選取 [IoT Edge] 作為部署您應用程式的 Azure 服務，然後選取 [下一步]。

   ![選取 IoT Edge 服務](media/how-to-devops-starter/select-iot-edge.png)

4. 免費建立新的 Azure DevOps 組織或選擇現有組織。

   1. 為您的專案提供名稱。

   2. 選取您的 Azure DevOps 組織。 如果您沒有現有組織，請選取 [其他設定] 來建立一個新的。

   3. 選取 Azure 訂用帳戶。

   4. 使用由您的專案名稱所產生的 IoT 中樞名稱，或是自行提供一個名稱。

   5. 接受預設位置，或選擇靠近位置。

   6. 選取 [ **其他設定** ]，以設定 DevOps Starter 會代表您建立的 Azure 資源。

   7. 選取 [完成] 來完成建立專案。

   ![命名和建立專案](media/how-to-devops-starter/create-project.png)

在幾分鐘後，Azure 入口網站中便會顯示 DevOps Starter 儀表板。 選取您的專案名稱來查看進度。 您可能需要重新整理該頁面。 系統會在您 Azure DevOps 組織的存放庫中設定範例 IoT Edge 應用程式、執行建置，然後將您的應用程式部署到 IoT Edge 裝置。 此儀表板可顯示您的程式碼存放庫、CI/CD 管線，和您在 Azure 中的應用程式。

   ![Azure 入口網站中的 View 專案](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>認可程式碼變更並執行 CI/CD

DevOps 入門會在 Azure Repos 中為您的專案建立 Git 存放庫。 在這一節中，您會檢視存放庫並變更您應用程式的程式碼。

1. 若要瀏覽到針對您的專案所建立的存放庫，請選取您專案儀表板功能表中的 [存放庫]。 此連結會開啟 [瀏覽器] 索引標籤和新專案的 Azure DevOps 存放庫。

   ![檢視在 Azure Repos 中產生的存放庫](./media/how-to-devops-starter/view-repositories.png)

> [!NOTE]
> 下列步驟會逐步引導您使用網頁瀏覽器來變更應用程式的程式碼。 如果您想要改為於本機複製您的存放庫，請選取視窗右上角的 [複製]。 使用所提供的 URL 來在 Visual Studio Code 或您偏好的開發工具中複製您的 Git 存放庫。

2. 根據在建立程序中所選的應用程式語言，存放庫已經包含適用於稱為 **FilterModule** 模組的程式碼。 開啟 **modules/FilterModule/module.json** 檔案。

   ![在 Azure Repos 中開啟 module.json 檔案](./media/how-to-devops-starter/open-module-json.png)

3. 請注意，此檔案會使用 **version** 參數中的 [Azure DevOps 組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) (英文)。 此設定可確保每次執行新組建時，都會建立新版本的模組。

## <a name="examine-the-cicd-pipeline"></a>檢查 CI/CD 管線

在上一節中，Azure DevOps Starter 會自動為您的 IoT Edge 應用程式設定完整的 CI/CD 管線。 現在請探索管線，並視需要進行自訂。 請使用下列步驟來熟悉 Azure DevOps 組建和發行管線。

1. 若要檢視 DevOps 專案中的建置管線，請選取專案儀表板功能表中的 [建置管線]。 此連結會開啟瀏覽器索引標籤和新專案的 Azure DevOps 建置管線。

   ![在 Azure Pipelines 中檢視建置管線](./media/how-to-devops-starter/view-build-pipelines.png)

2. 開啟自動產生的組建管線，然後選取右上方的 [ **編輯** ]。

    ![編輯組建管線](media/how-to-devops-starter/click-edit-button.png)

3. 在開啟的面板中，您可以檢視會在建置管線執行時發生的工作。 建置管線會執行各種工作，例如從 Git 存放庫提取來源、建置 IoT Edge 模組映像、將 IoT Edge 模組推送到容器登錄，以及發佈用於部署的輸出。 若要深入了解 Azure DevOps 中的 Azure IoT Edge 工作，請參閱[針對持續整合設定 Azure Pipelines](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration)。

4. 選取建置管線頂端的 [管線] 標頭來開啟管線詳細資料。 將建置管線的名稱變更成較具描述性的名稱。

   ![編輯管線詳細資料](./media/how-to-devops-starter/edit-build-pipeline.png)

5. 選取 [儲存並排入佇列]，然後選取 [儲存]。 這是批註的選擇性選項。

6. 在 [組建管線] 功能表中選取 [觸發程序]。 DevOps Starter 已自動建立 CI 觸發程序，且每次對存放庫的認可都會啟動新的組建。  您可以選擇性地選擇要在 CI 程序中包含還是排除分支。

7. 選取 [保留期]。 遵循連結，將您重新導向至保留原則所在的專案設定。 根據案例，您可以指定原則來保留或移除特定數目的組建。

8. 選取 [記錄]。 [記錄] 窗格會包含組建近期變更的稽核線索。 Azure Pipelines 會追蹤對建置管線進行的任何變更，且可讓您比較版本。

9. 當您探索完建置管線之後，請瀏覽到相對應的發行管線。 選取 [管線] 下方的 [版本]，然後選取 [編輯] 以檢視管線詳細資料。

    ![檢視發行管線](media/how-to-devops-starter/release-pipeline.png)

10. 在 [成品] 下，選取 [置放]。 此成品所監看的來源，是您在先前步驟所檢查之建置管線的輸出。

11. 在 [置放] 圖示旁邊，選取看起來像閃電的 [持續部署觸發程序]。 這個發行管線已啟用觸發程序，每次有新的組建成品可用時，它就會執行部署。 您可以選擇性地停用觸發程序，因此需要手動執行部署。  

12. 在您發行管線的功能表中，選取 [工作] 然後從下拉式清單中選擇 [開發] 階段。 DevOps Projects 已為您建立發行階段，其會建立 IoT 中樞，於該中樞中建立 IoT Edge 裝置，從建置管線部署範例模組，然後佈建虛擬機器以作為您的 IoT Edge 裝置執行。 若要深入了解適用於 CD 的 Azure IoT Edge 工作，請參閱[針對持續部署設定 Azure Pipelines](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment)。

    ![檢視持續部署工作](media/how-to-devops-starter/choose-release.png)

13. 從右側選取 [檢視版本]。 此檢視會顯示版本的歷程記錄。

14. 選取某個版本的名稱以檢視其詳細資訊。

## <a name="clean-up-resources"></a>清除資源

當您不再需要所建立的 Azure App Service 和其他相關資源時，可將其刪除。 請使用 DevOps 入門版儀表板的 [刪除] 功能。

## <a name="next-steps"></a>後續步驟

* 在 [Azure IoT Edge 的持續整合與持續部署](how-to-continuous-integration-continuous-deployment.md)中了解 Azure DevOps 上的 Azure IoT Edge 工作
* 在[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)中，了解 IoT Edge 部署。
* 逐步解說[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)中，用來建立、更新或刪除部署的步驟。
