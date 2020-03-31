---
title: Azure 資料資源管理器的 Azure DevOps 任務
description: 在本主題中，您將學習創建發佈管道並部署
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472038"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure 資料資源管理器的 Azure DevOps 任務

[Azure DevOps 服務](https://azure.microsoft.com/services/devops/)提供開發協作工具，如高性能管道、免費專用 Git 存儲庫、可配置看板以及廣泛的自動和連續測試功能。 [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是一種 Azure DevOps 功能，使您能夠管理 CI/CD，以便使用適用于任何語言、平臺和雲的高性能管道部署代碼。
[Azure 資料資源管理器 - 管理命令](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX)是 Azure 管道任務，它使您能夠創建發佈管道並將資料庫更改部署到 Azure 資料資源管理器資料庫。 它在[視覺工作室市場](https://marketplace.visualstudio.com/)免費提供。

本文檔介紹了有關使用**Azure 資料資源管理器和管理命令**任務將架構更改部署到資料庫的簡單示例。 有關完整的 CI/CD 管道，請參閱[Azure DevOps 文檔](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* Azure 資料資源管理器群集設置：
    * [Azure 資料資源管理器群集和資料庫](/azure/data-explorer/create-cluster-database-portal)
    * 通過[預配 Azure AD 應用程式創建](/azure/kusto/management/access-control/how-to-provision-aad-app)Azure 活動目錄 （Azure AD） 應用。
    * 通過[管理 Azure 資料資源管理器資料庫許可權](/azure/data-explorer/manage-database-permissions)，在 Azure 資料資源管理器資料庫上授予對 Azure AD 應用的存取權限。
* Azure DevOps 設置：
    * [註冊免費組織](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [建立組織](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [在 Azure 開發人員計畫中創建專案](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [使用 Git 的代碼](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>建立資料夾

在 Git 存儲庫中創建以下示例資料夾（*函數*、*策略*、*表*）。 [在此處](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline)將檔案複製到相應的資料夾中，如下所示並提交更改。 提供示例檔以執行以下工作流。

![建立資料夾](media/devops/create-folders.png)

> [!TIP]
> 在創建自己的工作流時，我們建議您使代碼具有冪等性。 例如，使用[.create-merge 表](/azure/kusto/management/create-table-command#create-merge-table)而不是[.create 表](/azure/kusto/management/create-table-command)，並使用[.create 或更改](/azure/kusto/management/create-alter-function)函數而不是[.create](/azure/kusto/management/create-function)函數。

## <a name="create-a-release-pipeline"></a>建立發行管線

1. 登錄到 Azure [DevOps 組織](https://dev.azure.com/)。
1. 選擇 **"管道** > **從左側功能表中釋放"** 並選擇 **"新建管道**"。

    ![新增管線](media/devops/new-pipeline.png)

1. 將打開 **"新建"管道**視窗。 在"**管道"** 選項卡中，在"**選擇範本**"窗格中，選擇 **"空作業**"。

     ![選取範本](media/devops/select-template.png)

1. 選擇 **"階段"** 按鈕。 在 **"階段"** 窗格中，添加**舞臺名稱**。 選擇 **"保存"** 以保存管道。

    ![命名舞臺](media/devops/stage-name.png)

1. 選擇 **"添加專案**"按鈕。 在"**添加專案"** 窗格中，選擇代碼所在的存儲庫，填寫相關資訊，然後按一下"**添加**"。 選擇 **"保存"** 以保存管道。

    ![新增構件](media/devops/add-artifact.png)

1. 在 **"變數"** 選項卡中，選擇 **"添加"** 以創建將在任務中使用的**終結點 URL**的變數。 編寫終結點**的名稱**和**值**。 選擇 **"保存"** 以保存管道。 

    ![創建變數](media/devops/create-variable.png)

    要查找**Endpoint_URL，Azure 資料資源管理器群集**在 Azure 門戶中的概述頁包含 Azure 資料資源管理器群集 URI。 以以下格式`https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`構造 URI。  例如，HTTPs：\//kustodocs.westus.kusto.windows.net？資料庫名稱_示例DB

    ![Azure 資料資源管理器群集 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>創建要部署的任務

1. 在 **"管道"** 選項卡中，按一下**1 個作業，0 個任務**以添加任務。 

    ![新增工作](media/devops/add-task.png)

1. 按此順序創建三個任務以部署**表**、**函數**和**策略**。 

1. 在"**任務"** 選項卡中**+**，按 **"代理作業**"選擇 。 搜尋 **Azure 資料總管**。 在**應用商店**中，安裝**Azure 資料資源管理器和管理員命令**擴展。 然後，選擇 **"在****運行 Azure 資料資源管理器"命令**中添加。

     ![添加管理員命令](media/devops/add-admin-commands.png)

1. 按一下左側的**Kusto 命令**，然後使用以下資訊更新任務：
    * **顯示名稱**：任務的名稱
    * **檔路徑**：在**表**任務中，指定 */table/*.csl，因為表創建檔位於*表*資料夾中。
    * **終結點 URL**：`EndPoint URL`輸入在上一步中創建的變數。
    * 選擇 **"使用服務終結點**"並選擇 **" 新建**"。

    ![更新庫斯塔命令任務](media/devops/kusto-command-task.png)

1. 在 **"添加 Azure 資料資源管理器"服務連接**視窗中完成以下資訊：

    |設定  |建議的值  |
    |---------|---------|
    |**連接名稱**     |    輸入名稱以標識此服務終結點     |
    |**群集 URL**    |    值可在 Azure 門戶中的 Azure 資料資源管理器群集的概述部分中找到 | 
    |**服務主體 ID**    |    輸入 AAD 應用 ID（作為先決條件創建）     |
    |**服務主體應用金鑰**     |    輸入 AAD 應用金鑰（作為先決條件創建）    |
    |**AAD 租戶 ID**    |      輸入您的 AAD 租戶（如microsoft.com、contoso.com...    |

    選擇"**允許所有管道使用此連接**"核取方塊。 選取 [確定]****。

    ![添加服務連接](media/devops/add-service-connection.png)

1. 重複步驟 1-5 兩次以從*函數*和*策略*資料夾中部署檔。 選取 [儲存]****。 在"**任務"** 選項卡中，請參閱創建的三個任務：**部署表**、**部署函數**和**部署策略**。

    ![部署所有資料夾](media/devops/deploy-all-folders.png)

1. 選擇 **+ 發佈** > **創建版本**以創建版本。

    ![建立發行](media/devops/create-release.png)

1. 在 **"日誌"** 選項卡中，檢查部署狀態是否成功。

    ![部署成功](media/devops/deployment-successful.png)

您現已完成發佈管道的創建，用於部署三個任務進行預生產。