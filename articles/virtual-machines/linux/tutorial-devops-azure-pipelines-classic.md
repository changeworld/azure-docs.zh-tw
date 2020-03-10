---
title: 教學課程 - Azure 上適用於 IaaS 和 PaaS 的整合式 DevOps
description: 在本教學課程中，您將了解如何使用 Azure Pipelines 將應用程式的持續整合 (CI) 和持續部署 (CD) 安裝至 Azure VM。
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912523"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>教學課程：Azure 上適用於 IaaS 和 PaaS 的整合式 DevOps

透過 Azure 上的端對端解決方案，小組可以在應用程式生命週期的各個階段實作 DevOps 做法：規劃、開發、交付和操作。 

以下是一些 Azure 服務，這些服務可簡化雲端工作負載，並且可在結合後實現強大無比的案例。
這些技術加上人員與程序，就能讓小組持續為客戶提供價值。 

- Azure： https://portal.azure.com – 用來建置雲端工作負載的入口網站。 管理和監視各種項目，小至簡單的 Web 應用程式，大到複雜的雲端應用程式 
- Azure DevOps： https://dev.azure.com – 透過一組現代化的開發服務，更聰明地進行規劃、更有效率地共同作業，以及更快速地送達 
- Azure Machine Learning Studio： https://ml.azure.com - 準備資料、定型及部署機器學習模型 
 

Azure DevOps 是內建的 Azure 服務，可透過持續整合和持續傳遞來為所有 Azure 資源自動執行 DevOps 程序的每個部分。
無論應用程式使用的是虛擬機器、Web 應用程式、Kubernetes 或任何其他資源，您都可以使用 Azure 和 Azure DevOps 來實作基礎結構即程式碼、持續整合、持續測試、持續傳遞和持續監視。  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - 設定 CI/CD 
Azure Pipelines 提供了一組完整且功能齊全的 CI/CD 自動化工具，可用來部署至虛擬機器。 您可以直接從 Azure 入口網站設定 Azure VM 的持續傳遞管線。 本文件包含了相關步驟，可供您從 Azure 入口網站設定 CI/CD 管線以進行多機器部署。 請在虛擬機器上設定 CI/CD。

虛擬機器可以新增為[部署群組](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)的目標，並可作為多機器輪流更新的目標。 部署群組內的部署歷程記錄檢視可提供從 VM 到管線、再到認可的追蹤。 
 
**輪流更新**：輪流部署會在每一次的反覆執行中，於固定一組機器上將應用程式上一版本的執行個體取代為應用程式新版本的執行個體。 讓我們逐步解說如何設定虛擬機器的輪流更新。  
您可以在 Azure 入口網站中使用持續傳遞選項來設定**虛擬機器**的輪流更新。 

以下是逐步解說。 
1. 登入 Azure 入口網站並瀏覽至虛擬機器。 
2. 在 VM 的左窗格中，瀏覽至 [ **持續傳遞**]  功能表。 然後按一下 [ **設定**]。 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. 在 [設定] 面板中，按一下 [Azure DevOps 組織] 以選取現有帳戶，或建立一個新帳戶。 然後選取要在其下設定管線的專案。  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. 部署群組是根據邏輯而形成、會代表實體環境的一組部署目標機器；例如，「開發」、「測試」、「使用者驗收測試」和「生產」。 您可以建立新的部署群組，也可以選取現有的部署群組。 您可以選擇性地以角色標記機器。 例如，「web」、「db」等等。  
5. 按一下對話方塊上的 [確定]  以設定持續傳遞管線。 
6. 完成之後，您將會擁有設定為要部署至虛擬機器的持續傳遞管線。  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. 您會看到虛擬機器的部署正在進行中。 您可以按一下連結以瀏覽至管線。 按一下 [Release-1]  可檢視部署。 或者，您也可以按一下 [編輯]  來修改發行管線的定義。 
8. 如果您有多個要設定的 VM，請重複步驟 2 至 5，以將其他 VM 新增至部署群組。 
9. 完成後，按一下管線定義、瀏覽至 Azure DevOps 組織，然後按一下 [編輯]  發行管線。 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. 在 [開發]  階段中，按一下 [1 項作業、1 個工作]  連結。 按一下 [部署]  階段。  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. 從右邊的 [設定] 窗格中，您可以看到管線預設會設定為以平行方式執行所有目標的輪流更新。 您可以使用滑桿，將部署設定為一次一個或依百分比進行。  
  
  
**Canary** 會藉由緩慢地向一小部分使用者推出變更，以降低風險。 隨著您對新版本越來越有信心，您可以開始將其發行到基礎結構中的更多伺服器，然後將更多使用者路由傳送至該版本。 您可以在 Azure 入口網站中使用持續傳遞選項來設定**虛擬機器**的 Canary 部署。 以下是逐步解說。 
1. 登入 Azure 入口網站並瀏覽至虛擬機器 
2. 遵循上一節中的步驟 2 至 5，將多個 VM 新增至部署群組。 
3. 將自訂標籤新增至要成為 Canary 部署一部分的 VM。 例如，「Canary」。
4. 為 VM 設定管線之後，請按一下管線、啟動 Azure DevOps 組織、[編輯]  管線，然後瀏覽至 [開發]  階段。 將標籤新增至篩選條件「Canary」。 
5. 新增另一個部署群組階段，使用標籤來設定階段以將其餘 VM 的目標設為該部署群組。  
6. (選擇性) 設定手動驗證步驟以升級/拒絕 Canary 部署。 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**藍色-綠色**會藉由準備好一個完全相同的待命環境來減少部署停機時間。 隨時會有一個環境保持運作。 在為新版本做準備時，您會在綠色環境中執行測試的最後階段。 一旦軟體在綠色環境中正常運作後，請切換流量，讓所有傳入的要求進入綠色環境，此時藍色環境便會成為閒置狀態。
您可以從 Azure 入口網站使用持續傳遞選項來設定**虛擬機器**的藍色-綠色部署。 

以下是逐步解說。 

1. 登入 Azure 入口網站並瀏覽至虛擬機器 
2. 遵循**輪流更新**一節底下的步驟 2 至 5，將多個 VM 新增至部署群組。 將自訂標籤新增至要成為藍色-綠色部署一部分的 VM。 例如，對於用於待命角色的 VM，可新增「藍色」或「綠色」標籤。 
3. 為 VM 設定管線之後，請按一下管線、啟動 Azure DevOps 組織、[編輯]  管線，然後瀏覽至 [開發]  階段。 將標籤新增至篩選條件「綠色」。 
4. 新增無代理程式階段、設定具有手動驗證步驟和 invoke-REST API 步驟的階段來交換標籤。 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps 專案 
比以往更為輕鬆地開始使用 Azure。
 
有了 DevOps Projects，只要三個步驟就能開始在任何 Azure 服務上執行應用程式：選取應用程式語言、執行階段和 Azure 服務。
 
[深入了解](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他資源 
- [使用 DevOps 專案部署至 Azure 虛擬機器](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [為應用程式實作目標為虛擬機器擴展集的持續部署](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
