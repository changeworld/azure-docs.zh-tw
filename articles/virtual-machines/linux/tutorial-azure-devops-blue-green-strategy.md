---
title: 教學課程 - 設定 Azure Linux 虛擬機器的 Canary 部署
description: 在本教學課程中，您將了解如何設定持續部署 (CD) 管線。 此管線會使用藍綠部署策略更新 Azure Linux 虛擬機器的群組。
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
ms.openlocfilehash: a98989ed48e515cafeca27ae492c83efca6002c4
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871606"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>教學課程 - 設定 Azure Linux 虛擬機器的藍綠部署策略

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>基礎結構即服務 (IaaS) - 設定 CI/CD

Azure Pipelines 提供了一組功能齊全的 CI/CD 自動化工具，可用來部署至虛擬機器。 您可以從 Azure 入口網站設定 Azure VM 的持續傳遞管線。

本文說明如何設定使用藍綠策略進行多機器部署的 CI/CD 管線。 Azure 入口網站也支援其他策略，例如[輪流](https://aka.ms/AA7jlh8)和 [Canary](https://aka.ms/AA7jdrz)。

### <a name="configure-cicd-on-virtual-machines"></a>在虛擬機器上設定 CI/CD

您可以將虛擬機器新增為[部署群組](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)的目標。 然後，您可以將其設為多機器更新的目標。 部署至機器後，請檢視部署群組內的 [部署歷程記錄]。 此檢視可讓您從 VM 追蹤至管線，然後再追蹤至認可。

### <a name="blue-green-deployments"></a>藍綠部署

藍綠部署會準備好一個完全相同的待命環境以縮短停機時間。 無論何時，都只會有一個有效的環境。

在為新版本做準備時，請在綠色環境中完成測試的最後階段。 當軟體在綠色環境中正常運作後，請切換流量，讓所有傳入的要求進入綠色環境。 藍色環境會處於閒置狀態。

使用持續傳遞選項時，您可以從 Azure 入口網站設定虛擬機器的藍綠部署。 以下是逐步解說：

1. 登入 Azure 入口網站並瀏覽至虛擬機器。
1. 在 VM 設定最左側的窗格中，選取 [持續傳遞]。 然後選取 [設定]。

   ![具有 [設定] 按鈕的 [持續傳遞] 窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 在 [設定] 面板中，選取 [Azure DevOps 組織] 以選擇現有的帳戶，或建立新的帳戶。 然後，選取要在其下設定管線的專案。  

   ![持續傳遞面板](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 部署群組是根據邏輯而形成、會代表實體環境的一組部署目標機器。 開發、測試、UAT 和生產皆為其範例。 您可以建立新的部署群組，或選取現有的群組。
1. 選取組建管線，以發佈要部署到虛擬機器的套件。 已發佈的套件在套件根資料夾的 deployscripts 資料夾中應有名為 deploy.ps1 或 deploy.sh 的部署指令碼。 管線會執行此部署指令碼。
1. 在 [部署策略] 中，選取 [藍綠]。
1. 將 "blue" 或 "green" 標籤新增至要成為藍綠部署一部分的 VM。 如果 VM 是待命角色，請將其標記為 "green"。 否則，請將其標記為 "blue"。

   ![已選擇「藍綠」作為部署策略值的持續傳遞面板](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. 選取 [確定]，以設定要部署至虛擬機器的持續傳遞管線。

   ![藍綠管線](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. 此時會顯示虛擬機器的部署詳細資料。 您可以選取連結以移至 Azure DevOps 中的發行管線。 在發行管線中，選取 [編輯] 以檢視管線設定。 此管線具有下列三個階段：

   1. 此階段是部署群組階段。 應用程式會部署至標記為 "green" 的待命 VM。
   1. 在此階段中會暫停管線，並等候手動介入以繼續執行。 使用者在手動確定已穩定部署至標記為 "green" 的 VM 之後，即可繼續執行管線。
   1. 此階段會交換 VM 中的 "blue" 和 "green" 標籤。 這可確保具有舊版應用程式的 VM 此時會標記為 "green"。 在下一個管線執行期間，應用程式將會部署至這些 VM。

      ![「部署藍綠」工作的 [部署群組] 窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. 「執行部署指令碼」工作依預設會執行部署指令碼 deploy.ps1 或 deploy.sh。此指令碼位於已發佈套件之根資料夾的 deployscripts 資料夾中。 請確定選取的建置管線會將部署發佈到套件的根資料夾中。

   ![顯示 deployscripts 資料夾中的 deploy.sh 的 [成品] 窗格](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>其他部署策略

- [設定輪流部署策略](https://aka.ms/AA7jlh8)
- [設定 Canary 部署策略](https://aka.ms/AA7jdrz)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

您可以輕鬆地開始使用 Azure。 透過 DevOps Projects，只要在三個步驟中選取下列項目，就可開始在任何 Azure 服務上執行應用程式：

- 應用程式語言
- 執行階段
- Azure 服務

[深入了解](https://azure.microsoft.com/features/devops-projects/)。

## <a name="additional-resources"></a>其他資源

- [使用 Azure DevOps Projects 部署 Azure 虛擬機器](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [為應用程式實作目標為虛擬機器擴展集的持續部署](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
