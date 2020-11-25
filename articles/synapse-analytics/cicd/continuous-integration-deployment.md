---
title: Synapse 工作區的持續整合和傳遞
description: 瞭解如何使用持續整合和傳遞，將工作區中的變更部署到一個環境 (開發、測試、生產) 至另一個環境。
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 2f2221ad10a2e07a3443cab9f957c8ec26969a3b
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96031202"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse 工作區的持續整合和傳遞

## <a name="overview"></a>概觀

持續整合 (CI) 是在每次小組成員將變更認可到版本控制時，自動化組建和測試程式碼的流程。 持續部署 (CD) 是在生產環境中建立、測試、設定和部署多個測試或預備環境的流程。

針對 Azure Synapse 工作區，持續整合和傳遞 (CI/CD) 將 (開發、測試、生產) 等環境中的所有實體移至另一個環境。 若要將您的工作區升級為另一個工作區，有兩個部分：使用 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) 來建立或更新工作區資源 (集區和工作區) ;使用 Azure DevOps 中的 Synapse CI/CD 工具，將成品 (SQL 腳本、筆記本、Spark 作業定義、管線、資料集、資料流程等) 。 

本文將概述如何使用 Azure 發行管線，將 Synapse 工作區的部署自動化至多個環境。

## <a name="pre-requirements"></a>預先需求

-   用於開發的工作區已使用 Studio 中的 Git 儲存機制進行設定，請參閱 [Synapse Studio 中的原始檔控制](source-control.md)。
-   已準備好執行發行管線的 Azure DevOps 專案。

## <a name="set-up-a-release-pipelines"></a>設定發行管線

1.  在 [Azure DevOps](https://dev.azure.com/)中，開啟針對發行建立的專案。

1.  在頁面的左側，選取 [管線]，然後選取 [發行]。

    ![選取管線、發行](media/create-release-1.png)

1.  選取 [新增管線]，或者，如有現有的管線，請選取 [新增]，然後選取 [新增發行管線]。

1.  選取 [空白作業] 範本。

    ![選取空白作業](media/create-release-select-empty.png)

1.  在 [階段名稱] 中，輸入您的環境名稱。

1.  選取 [ **新增** 成品]，然後選取您的開發 Synapse Studio 所設定的 git 存放庫。 選取您用來管理集區和工作區 ARM 範本的 git 存放庫。 如果您使用 GitHub 作為來源，您需要為您的 GitHub 帳戶和提取存放庫建立服務連線。 有關[服務連接](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints)的詳細資訊 

    ![新增發佈分支](media/release-creation-github.png)

1.  選取 ARM 範本的分支以進行資源更新。 針對 [預設版本]，選取 [來自預設分支的最新版本]。

    ![新增 ARM 範本](media/release-creation-arm-branch.png)

1.  針對 [預設分支] 選取存放庫的[發佈分支](source-control.md#configure-publishing-settings)。 根據預設，此發行分支為 `workspace_publish`。 針對 [預設版本]，選取 [來自預設分支的最新版本]。

    ![新增構件](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>設定用於 ARM 資源建立和更新的階段工作 

新增 Azure Resource Manager 部署工作來建立或更新資源，包括工作區和集區：

1. 在階段檢視中，選取 [檢視階段工作]。

    ![階段檢視](media/release-creation-stage-view.png)

1. 建立新的工作。 搜尋 **ARM 範本部署**，然後選取 [ **新增**]。

1. 在 [部署] 工作中，選取目標工作區的訂用帳戶、資源群組和位置。 視需要提供認證。

1. 在 [動作] 清單中，選取 [建立或更新資源群組]。

1. 選取 [範本] 方塊旁的省略符號按鈕 ( **...** )。 流覽目標工作區的 Azure Resource Manager 範本

1. 選取 [範本參數] 方塊旁的 **…** ，以選擇 parameters 檔案。

1. 選取 [覆寫範本參數] 方塊旁的 在 [覆 **寫範本參數** ] 方塊旁，輸入目標工作區所需的參數值。 

1. 針對 [部署模式] 選取 [增量]。
    
    ![工作區和集區部署](media/pools-resource-deploy.png)

1.  (選擇性) 新增授與和更新工作區角色指派 **Azure PowerShell** 。 如果您使用發行管線來建立 Synapse 工作區，則必須將管線的服務主體新增為預設工作區系統管理員。您可以執行 PowerShell，將工作區的存取權授與其他帳戶。 
    
    ![授與許可權](media/release-creation-grant-permission.png)

 > [!WARNING]
> 在完整部署模式中，存在於資源群組中但未在新的 Resource Manager 範本中指定的資源將會被 **刪除**。 如需詳細資訊，請參閱 [Azure Resource Manager 部署模式](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>設定構件部署的階段工作 

使用 [Synapse Workspace Build & Release](https://marketplace.visualstudio.com/items?itemName=PraveenMathamsetty.synapsecicd-deploy) 工作，在 Synapse 工作區中部署其他專案，例如資料集、SQL 腳本、筆記本、spark 作業定義、資料流程、管線、連結服務、認證和 IR (Integration Runtime) 。  

1. 確定已將訂用帳戶的許可權授與 Azure DevOps 管線的服務主體，而且也指派為目標工作區的工作區系統管理員。 

1. 建立新的工作。 搜尋 **Synapse Workspace Build & Release**，然後選取 [ **新增**]。

1.  在工作中，提供 **workspace_publish** 的相關 git 存放庫資訊，然後選取目標工作區的資源群組、區域、名稱和雲端環境。 如有需要，請提供參數和值。

    ![synapse 工作區部署](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> 在 CI/CD 案例中，不同環境中的整合執行階段 (IR) 類型必須相同。 例如，如果您在開發環境中有自我裝載 IR，則相同的 IR 在其他環境 (例如測試和生產環境) 中也必須屬於自我裝載類型。 同樣地，如果您要跨多個階段共用整合執行階段，則必須將所有環境中的整合執行階段設定為連結自我裝載，例如開發、測試和生產環境。

## <a name="create-release-for-deployment"></a>建立部署的發行 

儲存所有變更之後，您可以選取 [ **建立發行** ] 以手動建立發行。 若要自動建立發行，請參閱 [Azure DevOps 發行觸發程序](https://docs.microsoft.com/azure/devops/pipelines/release/triggers)

   ![建立建立發行](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>CI/CD 的最佳做法

如果您使用 Git 與 Synapse 工作區的整合，並擁有 CI/CD 管線，將您的變更從開發移至測試，然後再移至生產環境，我們建議使用下列最佳作法：

-   **Git 整合**。 使用 Git 整合來設定您的開發 Synapse 工作區。 測試和生產工作區的變更會透過 CI/CD 部署，而不需要 Git 整合。
-   **在構件遷移之前準備** 集區。 如果您在開發工作區中將集區附加至 SQL 腳本或筆記本，則預期會有不同環境中相同的集區名稱。 
-   **其他**。 查看 [其他最佳作法](/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)

## <a name="unsupported-features"></a>不支援的功能

- Synapse Studio 不允許對認可或選擇性的資源發佈進行揀選選擇。 
- Synapse Studio 不支援自訂認可訊息。
- 根據設計，刪除動作將直接認可至 git

