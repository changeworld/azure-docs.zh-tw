---
title: 教學課程 - 設定 Azure Linux 虛擬機器的 Canary 部署
description: 在本教學課程中，您將了解如何使用 Canary 部署策略來設定持續部署 (CD) 管線，進而更新 Azure Linux 虛擬機器群組
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120526"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>教學課程 - 設定 Azure Linux 虛擬機器的 Canary 部署策略


## <a name="iaas---configure-cicd"></a>IaaS - 設定 CI/CD 
Azure Pipelines 提供了一組完整且功能齊全的 CI/CD 自動化工具，可用來部署至虛擬機器。 您可以直接從 Azure 入口網站設定 Azure VM 的持續傳遞管線。 本文件包含了相關步驟，可供您設定 CI/CD 管線以使用 Canary 策略進行多機器部署。 您也可以查看其他策略，像是[輪流](https://aka.ms/AA7jlh8)和[藍綠](https://aka.ms/AA83fwu)部署，這些都是可透過 Azure 入口網站直接支援的。 


**請在虛擬機器上設定 CI/CD**

虛擬機器可以新增為[部署群組](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)的目標，並可作為多機器更新的目標。 部署之後，部署群組內的**部署記錄**可提供從 VM 到管線、再到認可的追蹤。 
 
  
**Canary 部署**：Canary 部署會慢慢地向一小部分使用者推出變更，以降低風險。 隨著您對新版本越來越有信心，您可以開始將其發行到基礎結構中的更多伺服器，然後將更多使用者路由傳送至該版本。 您可以在 Azure 入口網站中使用持續傳遞選項來設定虛擬機器的 Canary 部署。 以下是逐步解說。 
1. 登入 Azure 入口網站並瀏覽至虛擬機器 
2. 在 VM 的左窗格中，瀏覽至 [持續傳遞] ****  功能表。 按一下 [設定]  。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在 [設定] 面板中，按一下 [Azure DevOps 組織]  以選取現有帳戶，或建立一個新帳戶。 然後選取要在其下設定管線的專案。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署群組是根據邏輯而形成、會代表實體環境的一組部署目標機器；例如，「開發」、「測試」、「使用者驗收測試」和「生產」。 您可以建立新的部署群組，也可以選取現有的部署群組。 
5. 選取組建管線，以發佈要部署到虛擬機器的套件。 請注意，已發佈的套件在套件根目錄的 `deployscripts` 資料夾中應該要有部署指令碼 deploy.ps1  或 deploy.sh  。 這個部署指令碼將會在執行時間由 Azure DevOps 管線執行。
6. 選取您選擇的部署策略。 選取 [Canary]  。
7. Canary 部署成功後，將「Canary」標籤新增至成為 Canary 部署一部分的 VM，並將「Prod」標籤新增至成為部署一部分的 VM。 標籤可協助您以僅具有特定角色的 VM 作為目標。
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. 按一下對話方塊上的 [確定]  以設定持續傳遞管線。 您現在會擁有設定為要部署至虛擬機器的持續傳遞管線。
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. 按一下 Azure DevOps 中的 [編輯]  發行管線，以查看管線設定。 管線包含三個階段。 第一個階段是部署群組階段，並且會部署至標記為「canary」  的 VM。 第二個階段會暫停管線，並等候手動介入以繼續執行。 Canary 部署穩定且使用者也滿意後，就可以繼續執行管線執行，然後再執行第三個階段，也就是部署到標記為「生產」  的 VM。![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. 繼續管線執行之前，請確定至少有一個 VM 標記為「生產」  。在管線的第三個階段中，應用程式只會部署到有「生產」  標籤的 VM。

11. 執行部署指令碼工作預設會在已發佈套件根目錄的 'deployscripts' 資料夾中，執行部署指令碼 deploy.ps1  或 deploy.sh  。 請確定選取的建置管線會將此項目發佈到套件的根資料夾中。 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>其他部署策略
- [設定滾動部署策略](https://aka.ms/AA7jlh8)
- [設定藍綠部署策略](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Azure DevOps 專案 
比以往更為輕鬆地開始使用 Azure。
 
有了 DevOps Projects，只要三個步驟就能開始在任何 Azure 服務上執行應用程式：選取應用程式語言、執行階段和 Azure 服務。
 
[深入了解](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他資源 
- [使用 DevOps 專案部署至 Azure 虛擬機器](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [為應用程式實作目標為虛擬機器擴展集的持續部署](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
