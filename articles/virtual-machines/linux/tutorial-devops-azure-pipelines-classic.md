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
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885837"
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
Azure Pipelines 提供了一組完整且功能齊全的 CI/CD 自動化工具，可用來部署至虛擬機器。 您可以直接從 Azure 入口網站設定 Azure VM 的持續傳遞管線。 本文件包含了相關步驟，可供您從 Azure 入口網站設定 CI/CD 管線以進行多機器部署。 


**請在虛擬機器上設定 CI/CD**

虛擬機器可以新增為[部署群組](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)的目標，並可作為多機器更新的目標。 您可以根據需求選擇任何一種現成的部署策略，像是_輪流_、_Canary_、_Blue-Green_ 或進一步進行自訂。 部署之後，部署群組內的部署記錄檢視可提供從 VM 到管線、再到認可的追蹤。 
 
**輪流部署**：輪流部署會在每一次的反覆執行中，於固定一組機器上將應用程式上一版本的執行個體取代為應用程式新版本的執行個體。 讓我們逐步解說如何設定虛擬機器的輪流更新。  
您可以在 Azure 入口網站中使用持續傳遞選項來設定**虛擬機器**的輪流更新。 

以下是逐步解說。 
1. 登入 Azure 入口網站並瀏覽至虛擬機器。 
2. 在 VM 的左窗格中，瀏覽至 [ **持續傳遞**]  功能表。 然後按一下 [ **設定**]。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在 [設定] 面板中，按一下 [Azure DevOps 組織] 以選取現有帳戶，或建立一個新帳戶。 然後選取要在其下設定管線的專案。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署群組是根據邏輯而形成、會代表實體環境的一組部署目標機器；例如，「開發」、「測試」、「使用者驗收測試」和「生產」。 您可以建立新的部署群組，也可以選取現有的部署群組。 
5. 選取組建管線，以發佈要部署到虛擬機器的套件。 請注意，已發佈的套件在套件根目錄的 deployscripts  資料夾中應該要有部署指令碼  deploy.ps1 或  deploy.sh。 這個部署指令碼將會在執行時間由 Azure DevOps 管線執行。
6. 選取您選擇的部署策略。 在此情況下，請選取「輪流」。
7. 您也可以選擇性地以角色標記機器。 例如，「web」、「db」等等。這可協助您以僅具有特定角色的 VM 為目標。
8. 按一下對話方塊上的 [確定]  以設定持續傳遞管線。 
9. 完成之後，您將會擁有設定為要部署至虛擬機器的持續傳遞管線。  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. 您會看到虛擬機器的部署正在進行中。 您可以按一下連結以瀏覽至管線。 按一下 [Release-1]  可檢視部署。 或者，您也可以按一下 [編輯]  來修改發行管線的定義。 
11. 如果您有多個要設定的 VM，請重複步驟 2 至 4，以將其他 VM 新增至部署群組。 請注意，如果您選取已有管線執行的部署群組，系統會將 VM 直接新增至部署群組，而不會建立任何新的管線。 
12. 完成後，按一下管線定義、瀏覽至 Azure DevOps 組織，然後按一下 [編輯]  發行管線。 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. 在 [開發]  階段中，按一下 [1 項作業、1 個工作]  連結。 按一下 [部署]  階段。
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. 在右側的組態窗格中，您可以指定要在每個反覆運算中平行部署的機器數目。 如果想一次部署到多部機器，可以使用滑桿以百分比來指定。  

15. 執行部署指令碼工作預設會在已發佈套件之根目錄的 deployscripts  資料夾中，執行部署指令碼 deploy.ps1  或 deploy.sh  。
  
**Canary 部署**：Canary 部署會慢慢地向一小部分使用者推出變更，以降低風險。 隨著您對新版本越來越有信心，您可以開始將其發行到基礎結構中的更多伺服器，然後將更多使用者路由傳送至該版本。 您可以在 Azure 入口網站中使用持續傳遞選項來設定**虛擬機器**的 Canary 部署。 以下是逐步解說。 
1. 登入 Azure 入口網站並瀏覽至虛擬機器 
2. 遵循**輪流部署**一節下的步驟 2 至 7，將多個 VM 新增至部署群組。 在部署策略下拉式清單中，選取「Canary」。
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Canary 部署成功後，將「Canary」標籤新增至成為 Canary 部署一部分的 VM，並將「Prod」標籤新增至成為部署一部分的 VM。
4. 完成之後，您將會擁有設定為要部署至虛擬機器的持續傳遞管線。
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. 就像在**輪流部署** 一節中，您可以按一下 Azure DevOps 中的 [編輯]  發行管線，以查看管線組態。 管線包含 3 個階段 - 第一個階段是 DG 階段，並且會部署至標記為「Canery」  的 VM。 第二個階段會暫停管線，並等候手動介入以繼續執行。 Canary 部署穩定且使用者也滿意後，就可以繼續執行管線執行，然後再執行第三個階段，也就是部署到標記為「生產」  的 VM。![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Blue-Green 部署**：Blue-Green 部署會準備好一個完全相同的待命環境來減少停機時間。 隨時會有一個環境保持運作。 在為新版本做準備時，您會在綠色環境中執行測試的最後階段。 一旦軟體在綠色環境中正常運作後，請切換流量，讓所有傳入的要求進入綠色環境，此時藍色環境便會成為閒置狀態。
您可以從 Azure 入口網站使用持續傳遞選項來設定**虛擬機器**的藍色-綠色部署。 

以下是逐步解說。

1. 登入 Azure 入口網站並瀏覽至虛擬機器 
2. 遵循**輪流部署**一節下的步驟 2 至 7，將多個 VM 新增至部署群組。 將「藍色」或「綠色」標籤新增至要成為 Blue-Green 部署一部分的 VM。 如果 VM 是待命角色，則應該標記為「綠色」。
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. 完成之後，您將會擁有設定為要部署至虛擬機器的持續傳遞管線。
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. 就像進行**輪流部署**一樣，您可以按一下 Azure DevOps 中的 [編輯]  發行管線，以查看管線組態。 管線包含 3 個階段 - 第一個階段是 DG 階段，並且會部署至標記為「綠色」  (待命 VM) 的 VM。 第二個階段會暫停管線，並等候手動介入以繼續執行。 一旦部署穩定且使用者滿意時，就可以將流量重新導向至「綠色」  VM，然後繼續執行管線，將 VM 中的「藍色」  和「綠色」  標籤交換。 這可確保具有較舊應用程式版本的 VM 會標記為」綠色」  ，並部署到下一個管線執行。
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. 請注意，此部署策略必須至少有一個 VM 各標記為藍色和綠色。 請確定在手動介入步驟中繼續執行管線之前，至少有一個 VM 標記為「藍色」  。





 
## <a name="azure-devops-project"></a>Azure DevOps 專案 
比以往更為輕鬆地開始使用 Azure。
 
有了 DevOps Projects，只要三個步驟就能開始在任何 Azure 服務上執行應用程式：選取應用程式語言、執行階段和 Azure 服務。
 
[深入了解](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他資源 
- [使用 DevOps 專案部署至 Azure 虛擬機器](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [為應用程式實作目標為虛擬機器擴展集的持續部署](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
