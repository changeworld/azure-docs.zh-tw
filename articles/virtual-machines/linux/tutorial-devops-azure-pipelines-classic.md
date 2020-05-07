---
title: 教學課程 - 設定 Azure Linux 虛擬機器的輪流部署
description: 在本教學課程中，您將了解如何使用輪流部署策略來設定持續部署 (CD) 管線，進而以累加方式更新 Azure Linux 虛擬機器群組
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
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113473"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>教學課程 - 設定 Azure Linux 虛擬機器的輪流部署策略

Azure DevOps 是內建的 Azure 服務，可透過持續整合和持續傳遞來為所有 Azure 資源自動執行 DevOps 程序的每個部分。
無論應用程式使用的是虛擬機器、Web 應用程式、Kubernetes 或任何其他資源，您都可以使用 Azure 和 Azure DevOps 來實作基礎結構即程式碼、持續整合、持續測試、持續傳遞和持續監視。  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS - 設定 CI/CD 
Azure Pipelines 提供了一組完整且功能齊全的 CI/CD 自動化工具，可用來部署至虛擬機器。 您可以直接從 Azure 入口網站設定 Azure VM 的持續傳遞管線。 本文件包含了相關步驟，可供您從 Azure 入口網站設定 CI/CD 管線以進行輪流的多機器部署。 您也可以查看其他策略，像是 [Canary](https://aka.ms/AA7jdrz) 和[藍綠](https://aka.ms/AA83fwu)部署，這些都是可透過 Azure 入口網站直接支援的。 


**請在虛擬機器上設定 CI/CD**

虛擬機器可以新增為[部署群組](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)的目標，並可作為多機器更新的目標。 部署之後，部署群組內的**部署記錄**可提供從 VM 到管線、再到認可的追蹤。 
 

**輪流部署**：輪流部署會在每一次的反覆執行中，於固定一組機器上將應用程式上一版本的執行個體取代為應用程式新版本的執行個體。 讓我們逐步解說如何設定虛擬機器的輪流更新。  
您可以在 Azure 入口網站中使用持續傳遞選項來設定**虛擬機器**的輪流更新。 

以下是逐步解說。 
1. 登入 Azure 入口網站並瀏覽至虛擬機器。 
2. 在 VM 的左窗格中，瀏覽至 [ **持續傳遞**]  功能表。 然後按一下 [ **設定**]。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在 [設定] 面板中，按一下 [Azure DevOps 組織] 以選取現有帳戶，或建立一個新帳戶。 然後選取要在其下設定管線的專案。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署群組是根據邏輯而形成、會代表實體環境的一組部署目標機器；例如，「開發」、「測試」、「使用者驗收測試」和「生產」。 您可以建立新的部署群組，也可以選取現有的部署群組。 
5. 選取組建管線，以發佈要部署到虛擬機器的套件。 請注意，已發佈的套件在套件根目錄的 `deployscripts` 資料夾中應該要有部署指令碼 deploy.ps1  或 deploy.sh  。 這個部署指令碼將會在執行時間由 Azure DevOps 管線執行。
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

15. 執行部署指令碼工作預設會在已發佈套件根目錄的 'deployscripts' 資料夾中，執行部署指令碼 deploy.ps1  或 deploy.sh  。  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>其他部署策略

- [設定 Canary 部署策略](https://aka.ms/AA7jdrz)
- [設定藍綠部署策略](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps 專案 
比以往更為輕鬆地開始使用 Azure。
 
有了 DevOps Projects，只要三個步驟就能開始在任何 Azure 服務上執行應用程式：選取應用程式語言、執行階段和 Azure 服務。
 
[深入了解](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他資源 
- [使用 DevOps 專案部署至 Azure 虛擬機器](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [為應用程式實作目標為虛擬機器擴展集的持續部署](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
