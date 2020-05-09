---
title: 教學課程 - 設定 Azure Linux 虛擬機器的 Canary 部署
description: 在本教學課程中，您將了解如何使用藍綠部署策略來設定持續部署 (CD) 管線，進而更新 Azure 虛擬機器群組
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120546"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>教學課程 - 設定 Azure Linux 虛擬機器的藍綠部署策略


## <a name="iaas---configure-cicd"></a>IaaS - 設定 CI/CD 
Azure Pipelines 提供了一組完整且功能齊全的 CI/CD 自動化工具，可用來部署至虛擬機器。 您可以直接從 Azure 入口網站設定 Azure VM 的持續傳遞管線。 本文件包含了相關步驟，可供您設定 CI/CD 管線以使用藍綠策略進行多機器部署。 您也可以查看其他策略，像是[輪流](https://aka.ms/AA7jlh8)和 [canary](https://aka.ms/AA7jdrz) 部署，這些都是可透過 Azure 入口網站直接支援的。 

 
 **請在虛擬機器上設定 CI/CD**

虛擬機器可以新增為[部署群組](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)的目標，並可作為多機器更新的目標。 部署之後，檢視部署群組內的**部署記錄**可提供從 VM 到管線、再到認可的追蹤。 
 
  
**Blue-Green 部署**：Blue-Green 部署會準備好一個完全相同的待命環境來減少停機時間。 隨時會有一個環境保持運作。 在為新版本做準備時，您會在綠色環境中完成測試的最後階段。 一旦軟體在綠色環境中正常運作後，請切換流量，讓所有傳入的要求進入綠色環境，此時藍色環境便會成為閒置狀態。
您可以從 Azure 入口網站使用持續傳遞選項來設定**虛擬機器**的藍色-綠色部署。 

以下是逐步解說。

1. 登入 Azure 入口網站並瀏覽至虛擬機器 
2. 在 VM 左窗格中，移至 [持續傳遞]  。 然後，按一下 **[設定]** 。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在 [設定] 面板中，按一下 [Azure DevOps 組織]  以選取現有帳戶，或建立一個新帳戶。 然後選取要在其下設定管線的專案。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署群組是根據邏輯而形成、會代表實體環境的一組部署目標機器；例如，「開發」、「測試」、「使用者驗收測試」和「生產」。 您可以建立新的部署群組，也可以選取現有的部署群組。 
5. 選取組建管線，以發佈要部署到虛擬機器的套件。 請注意，已發佈的套件在套件根目錄的 `deployscripts` 資料夾中應該要有部署指令碼 deploy.ps1  或 deploy.sh  。 這個部署指令碼將會在執行時間由 Azure DevOps 管線執行。
6. 選取您選擇的部署策略。 選取 [藍綠]  。
7. 將「藍色」或「綠色」標籤新增至要成為 Blue-Green 部署一部分的 VM。 如果 VM 用於待命角色，則應該將其標記為「綠色」，否則請將其標記為「藍色」。
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. 按一下 [確定]  以設定持續傳遞管線。 您現在會擁有設定為要部署至虛擬機器的持續傳遞管線。
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. 按一下 Azure DevOps 中的 [編輯]  發行管線，以查看管線設定。 管線包含三個階段。 第一個階段是部署群組階段，並且會部署至標記為「綠色」  (待命 VM) 的 VM。 第二個階段會暫停管線，並等候手動介入以繼續執行。 一旦部署穩定且使用者滿意時，就可以將流量重新導向至「綠色」  VM，然後繼續執行管線，將 VM 中的「藍色」  和「綠色」  標籤交換。 這可確保具有較舊應用程式版本的 VM 會標記為」綠色」  ，並部署到下一個管線執行。
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. 執行部署指令碼工作預設會在已發佈套件根目錄的 `deployscripts` 資料夾中，執行部署指令碼 deploy.ps1  或 deploy.sh  。 請確定選取的建置管線會將此項目發佈到套件的根資料夾中。
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>其他部署策略
- [設定滾動部署策略](https://aka.ms/AA7jlh8)
- [設定 Canary 部署策略](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Azure DevOps 專案 
比以往更為輕鬆地開始使用 Azure。
 
有了 DevOps Projects，只要三個步驟就能開始在任何 Azure 服務上執行應用程式：選取應用程式語言、執行階段和 Azure 服務。
 
[深入了解](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他資源 
- [使用 DevOps 專案部署至 Azure 虛擬機器](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [為應用程式實作目標為虛擬機器擴展集的持續部署](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
