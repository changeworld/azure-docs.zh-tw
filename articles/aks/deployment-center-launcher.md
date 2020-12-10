---
title: 適用於 Azure Kubernetes 的部署中心
description: Azure DevOps 中的部署中心可簡化對您應用程式設定健全 Azure DevOps 管線的程序
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932212"
---
# <a name="deployment-center-for-azure-kubernetes"></a>適用於 Azure Kubernetes 的部署中心

Azure DevOps 中的部署中心可簡化對您應用程式設定健全 Azure DevOps 管線的程序。 根據預設，部署中心會設定 Azure DevOps 管線來將您的應用程式更新部署至 Kubernetes 叢集。 您可以擴充預設的 Azure DevOps 管線，並新增更豐富的功能：先核准再部署、佈建其他 Azure 資源、執行腳本、升級應用程式，甚至是執行額外驗證測試的能力。

在本教學課程中，您將：

> [!div class="checklist"]
> * 設定 Azure DevOps 管線來將您的應用程式更新部署至 Kubernetes 叢集。
> * 檢查持續整合 (CI) 管線。
> * 檢查持續傳遞 (CI) 管線。
> * 清除資源。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 您可以透過 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 取得免費的訂用帳戶。

* Azure Kubernetes Service (AKS) 叢集。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 Azure 入口網站中功能表列右上角的 [[Cloud Shell](../cloud-shell/overview.md)] 選項。

1. 若要建立 AKS 叢集，請執行下列命令：

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>將應用程式更新部署至 Kubernetes 叢集

1. 移至您在上一節中建立的資源群組。

1. 選取 AKS 叢集，然後在左側刀鋒視窗上選取 [部署中心 (預覽)]  。 選取 [馬上開始]。 

   ![此螢幕擷取畫面顯示 Azure 入口網站，其中有一個箭號指向部署中心。](media/deployment-center-launcher/settings.png)

1. 選擇程式碼的位置，然後選取 [下一步]  。 然後，選取其中一個目前支援的存放庫： **[Azure Repos](/azure/devops/repos/index)** 或 **GitHub**。

    Azure Repos 是一組版本控制工具，可協助您管理程式碼。 無論您的軟體專案大小為何，盡早使用版本控制都是個不錯的主意。

    - **Azure Repos**：從現有的專案和組織選擇存放庫。

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**：為您的 GitHub 帳戶授權並選取存放庫。

        ![動畫顯示 GitHub 中選取 GitHub 做為來源，然後選取存放庫的過程。](media/deployment-center-launcher/github.gif)


1. 部署中心會分析存放庫，並偵測您的 Dockerfile。 如果您想要更新 Dockerfile，可以編輯已找到的連接埠號碼。

    ![應用程式設定](media/deployment-center-launcher/application-settings.png)

    如果存放庫不包含 Dockerfile，系統就會顯示訊息來認可一個 Dockerfile。

    ![此螢幕擷取畫面顯示部署中心，其中有一則訊息指出「在存放庫中找不到 Dockerfile」。](media/deployment-center-launcher/dockerfile.png)

1. 選取現有的容器登錄或建立一個容器登錄，然後選取 [完成]  。 管線會自動建立，並將建置排入 [Azure Pipelines](/azure/devops/pipelines/index) 的佇列中。

    Azure Pipelines 是一種雲端服務，可讓您用來自動建置和測試程式碼專案，並將其提供給其他使用者使用。 Azure Pipelines 結合了持續整合與持續傳遞，可持續且一致地測試及建置程式碼，並將其傳送至任何目標。

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. 選取連結以查看進行中的管線。

1. 部署完成後，您會看到成功的記錄。

    ![此螢幕擷取畫面顯示部署中心，其中以綠色的核取記號圖示標記 Release-1。](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>檢查 CI 管線

部署中心會自動設定您 Azure DevOps 組織的 CI/CD 管線。 您可以探索及自訂管線。

1. 移至部署中心儀表板。  

1. 選取成功記錄清單中的組建編號，即可查看您專案的建置管線。

1. 選取右上角的省略符號 (...)。 功能表會顯示數個選項，例如將新建置排入佇列、保留建置和編輯建置管線。 選取 [編輯管線]  。 

1. 您可以在此窗格中檢查建置管線的不同工作。 該建置會執行各種工作，例如從 Git 存放庫收集來源、建立映像、將映射推送至容器登錄，以及發佈用於部署的輸出。

1. 在管線的頂端，選取建置管線名稱。

1. 將建置管線名稱變更成較具描述性的名稱，並選取 [儲存並排入佇列]  ，然後選取 [儲存]  。

1. 在建置管線下，選取 [記錄]  。 此窗格會顯示最近建置變更的稽核記錄。 Azure DevOps 會監視對建置管線進行的任何變更，且可讓您比較版本。

1. 選取 [觸發程序]  。 您可以在 CI 程序中包含還是排除分支。

1. 選取 [保留期]  。 根據案例，您可以指定原則來保留或移除數個建置。

## <a name="examine-the-cd-pipeline"></a>檢查 CD 管線

部署中心會自動建立及設定 Azure DevOps 組織和 Azure 訂用帳戶之間的關聯性。 這些步驟包括設定 Azure 服務連線，以向 Azure DevOps 驗證 Azure 訂用帳戶。 自動化程序也會建立發行管線，將持續傳遞提供給 Azure。

1. 選取 [管線]  ，然後選取 [發行]  。

1. 若要編輯發行管線，請選取 [編輯]  。

1. 從 [成品]  清單中選取 [置放]  。 在先前步驟中，您所檢查的建構管線已產生用於成品的輸出。 

1. 在 [置放]  選項的右側，選取 [持續部署]  觸發程序。 此發行管線已啟用 CD 觸發程序，每當有新的建置成品可用時，觸發程序就會執行部署。 您也可以停用觸發程序，以要求手動執行您的部署。

1. 若要檢查管線的所有工作，請選取 [工作]  。 發行會設定 Tiller 環境、設定 `imagePullSecrets` 參數、安裝 Helm 工具，並將 Helm 圖表部署至 Kubernetes 叢集。

1. 若要查看發行記錄，請選取 [檢視發行]  。

1. 若要查看摘要，請選取 [發行]  。 選取任何階段即可瀏覽數個功能表，例如發行摘要、相關聯的工作項目及測試。 

1. 選取 [認可]  。 此視圖會顯示與此部署相關的程式碼認可。 請比較發行版本，以查看部署的認可差異。

1. 選取 [記錄]  。 記錄包含實用的部署資訊，您可以在部署期間和之後加以查看。

## <a name="clean-up-resources"></a>清除資源

當您不再需要所建立的相關資源時，可將其刪除。 請使用 DevOps Projects 儀表板上的刪除功能。

## <a name="next-steps"></a>後續步驟

您可以修改這些建置和發行管線，以符合小組的需求。 或是，您可以使用此 CI/CD 模型作為其他管線的範本。
