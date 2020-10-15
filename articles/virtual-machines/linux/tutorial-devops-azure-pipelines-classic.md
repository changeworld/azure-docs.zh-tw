---
title: 教學課程 - 設定 Azure Linux 虛擬機器的輪流部署
description: 在本教學課程中，您將了解如何設定持續部署 (CD) 管線。 此管線會使用輪流部署策略以累加方式更新 Azure Linux 虛擬機器的群組。
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
ms.openlocfilehash: f77cc85bd62deb2cb2cb74c42cf245a409904b3a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978300"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>教學課程 - 設定 Azure Linux 虛擬機器的輪流部署策略

Azure DevOps 是內建的 Azure 服務，可為所有 Azure 資源自動執行 DevOps 程序的每個部分。 無論應用程式使用的是虛擬機器、Web 應用程式、Kubernetes 或任何其他資源，您都可以使用 Azure 和 Azure DevOps 來實作基礎結構即程式碼 (IaC)、持續整合、持續測試、持續傳遞和持續監視。

![在 [服務] 底下選取 Azure DevOps 的 Azure 入口網站](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>基礎結構即服務 (IaaS) - 設定 CI/CD

Azure Pipelines 提供了一組功能齊全的 CI/CD 自動化工具，可用來部署至虛擬機器。 您可以從 Azure 入口網站設定 Azure VM 的持續傳遞管線。

本文說明如何從 Azure 入口網站設定用於輪流多機器部署的 CI/CD 管線。 Azure 入口網站也支援其他策略，例如 [Canary](./tutorial-azure-devops-canary-strategy.md) 和[藍綠](./tutorial-azure-devops-blue-green-strategy.md)。

### <a name="configure-cicd-on-virtual-machines"></a>在虛擬機器上設定 CI/CD

您可以將虛擬機器新增為[部署群組](/azure/devops/pipelines/release/deployment-groups)的目標。 然後，您可以將其設為多機器更新的目標。 部署至機器後，請檢視部署群組內的 [部署歷程記錄]。 此檢視可讓您從 VM 追蹤至管線，然後再追蹤至認可。

### <a name="rolling-deployments"></a>輪流部署

在每次反覆執行時，輪流部署都會取代應用程式舊版的執行個體。 舊版的執行個體會在一組固定的機器 (輪流集合) 上取代為新版的執行個體。 以下將逐步解說如何設定虛擬機器的輪流更新。

您可以在 Azure 入口網站中使用持續傳遞選項來設定虛擬機器的輪流更新。 以下是逐步解說：

1. 登入 Azure 入口網站並瀏覽至虛擬機器。
1. 在 VM 設定最左側的窗格中，選取 [持續傳遞]。 然後選取 [設定]。

   ![具有 [設定] 按鈕的 [持續傳遞] 窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 在 [設定] 面板中，選取 [Azure DevOps 組織] 以選擇現有的帳戶，或建立新的帳戶。 然後，選取要在其下設定管線的專案。  

   ![持續傳遞面板](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 部署群組是根據邏輯而形成、會代表實體環境的一組部署目標機器。 開發、測試、UAT 和生產皆為其範例。 您可以建立新的部署群組，或選取現有的群組。
1. 選取組建管線，以發佈要部署到虛擬機器的套件。 已發佈的套件在套件根資料夾的 deployscripts 資料夾中應有名為 deploy.ps1 或 deploy.sh 的部署指令碼。 管線會執行此部署指令碼。
1. 在 [部署策略] 中，選取 [輪流]。
1. 您也可以選擇性地以角色標記每個機器。 例如，"web" 和 "db" 標籤。 這些標籤可協助您僅以具有特定角色的 VM 作為目標。
1. 選取 [確定] 以設定持續傳遞管線。
1. 設定完成後，您會擁有設定為要部署至虛擬機器的持續傳遞管線。  

   ![顯示部署歷程記錄的持續傳遞面板](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. 此時會顯示虛擬機器的部署詳細資料。 您可以選取連結以移至管線 **Release-1** 檢視部署，或選取 [編輯] 以修改發行管線定義。

1. 如果您要設定多個 VM，請重複步驟 2 到 4，讓其他 VM 新增至部署群組。 如果您選取已執行管線的部署群組，VM 就會直接新增至部署群組。 不會建立新的管線。
1. 設定完成後，請選取管線定義，瀏覽至 Azure DevOps 組織，然後針對發行管線選取 [編輯]。

   ![編輯輪流管線](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. 在**開發**階段中，選取 [1 個作業、1 個工作]。 選取 [部署] 階段。

   ![已選取部署工作的輪流管線工作](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. 在最右側的設定窗格中，您可以指定要在每次反覆執行時平行部署的機器數目。 如果您想要一次部署到多個機器，您可以使用滑桿，以百分比指定機器數目。  

1. 「執行部署指令碼」工作依預設會執行部署指令碼 deploy.ps1 或 deploy.sh。此指令碼位於已發佈套件之根資料夾的 deployscripts 資料夾中。

   ![顯示 deployscripts 資料夾中的 deploy.sh 的 [成品] 窗格](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>其他部署策略

- [設定 Canary 部署策略](./tutorial-azure-devops-canary-strategy.md)
- [設定藍綠部署策略](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

您可以輕鬆地開始使用 Azure。 透過 DevOps Projects，只要在三個步驟中選取下列項目，就可開始在任何 Azure 服務上執行應用程式：

- 應用程式語言
- 執行階段
- Azure 服務
 
[深入了解](https://azure.microsoft.com/features/devops-projects/)。
 
## <a name="additional-resources"></a>其他資源

- [使用 Azure DevOps Projects 部署 Azure 虛擬機器](../../devops-project/azure-devops-project-vms.md)
- [為應用程式實作目標為虛擬機器擴展集的持續部署](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)