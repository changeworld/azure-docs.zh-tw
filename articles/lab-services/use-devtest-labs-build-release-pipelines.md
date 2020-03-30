---
title: 使用 Azure Pipelines 建置和發行管線中的 DevTest 實驗室
description: 瞭解如何在 Azure 管道生成和發佈管道中使用 Azure 開發人員測試實驗室。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169229"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>使用 Azure Pipelines 建置和發行管線中的 DevTest 實驗室
本文提供有關如何在 Azure 管道生成和發佈管道中使用 DevTest Labs 的資訊。 

## <a name="overall-flow"></a>總體流程
基本流程是具有執行以下任務的**生成管道**：

1. 生成應用程式代碼。
1. 在 DevTest 實驗室中創建基本環境。
1. 使用自訂資訊更新環境。
1. 將應用程式部署到開發人員測試實驗室環境
1. 測試代碼。 

成功完成生成後，**發佈管道**將使用生成專案來部署暫存或生產。 

必要的前提之一是，在生成工件中提供重新創建測試生態系統所需的所有資訊，包括 Azure 資源的配置。 由於 Azure 資源在使用時會產生成本，因此公司希望控制或跟蹤這些資源的使用。 在某些情況下，用於創建和配置資源的 Azure 資源管理器範本可能由 IT 等其他部門管理。 而且，這些範本可能存儲在不同的存儲庫中。 它會導致一個有趣的情況，即將創建和測試生成，並且代碼和配置都需要存儲在生成工件中，以便在生產中正確重新創建系統。 

在生成/測試階段使用 DevTest Labs，可以將 Azure 資源管理器範本和支援檔添加到生成源，以便在發佈階段將用於測試的確切配置部署到生產階段。 使用正確配置**創建 Azure DevTest 實驗室環境**任務將資源管理器範本保存在生成工件中。 在此示例中，您將使用教程中的代碼[：在 Azure 應用服務中構建 .NET Core 和 SQL 資料庫 Web 應用](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)，以在 Azure 中部署和測試 Web 應用。

![總體流程](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>設定 Azure 資源
需要事先創建幾個項：

- 兩個存儲庫。 第一個包含教程中的代碼和資源管理器範本，其中有兩個額外的 VM。 第二個將包含基本 Azure 資源管理器範本（現有配置）。
- 用於部署生產代碼和配置的資源組。
- 需要使用與生成管道[的配置存儲庫的連接](devtest-lab-create-environment-from-arm.md)來設置實驗室。 需要將資源管理器範本作為 azuredeploy.json 與中繼資料.json 一起簽入配置存儲庫，以允許 DevTest Labs 識別和部署該範本。

生成管道將創建一個 DevTest Labs 環境，並部署用於測試的代碼。

## <a name="set-up-a-build-pipeline"></a>設置生成管道
在 Azure 管道中，使用教程中的代碼創建生成管道[：在 Azure 應用服務中生成 .NET 核心和 SQL 資料庫 Web 應用](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)。 使用**ASP.NET核心**範本，該範本將填充生成、測試和發佈代碼所需的任務。

![選擇ASP.NET範本](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

您需要添加三個附加任務，以在 DevTest 實驗室中創建環境並部署到環境中。

![具有三個任務的管道](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>創建環境任務
在創建環境任務 **（Azure DevTest 實驗室創建環境**任務）中使用下拉清單選擇以下值：

- Azure 訂用帳戶
- 實驗室名稱
- 存儲庫的名稱
- 範本的名稱（顯示存儲環境的資料夾）。 

我們建議您在頁面上使用下拉清單，而不是手動輸入資訊。 如果手動輸入資訊，請輸入完全合格的 Azure 資源識別碼。 任務顯示易記名稱而不是資源 Id。 

環境名稱是開發人員測試實驗室中顯示的顯示名稱。 它應該是每個生成的唯一名稱。 例如 **：TestEnv$（生成.BuildId）**。 

您可以指定參數檔或參數以將資訊傳遞到資源管理器範本。 

**根據環境範本輸出選項選擇"創建輸出變數**"並輸入引用名稱。 在此示例中，輸入參考名稱的**BaseEnv。** 配置下一個任務時，您將使用此 BaseEnv。 

![創建 Azure 開發人員測試實驗室環境任務](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>填充環境任務
第二個任務 **（Azure 開發人員測試實驗室填充環境**任務）是更新現有的開發人員測試實驗室環境。 創建環境任務輸出用於為此任務配置環境名稱的**BaseEnv.環境ResourceId。** 此示例的資源管理器範本有兩個參數 -**管理員使用者名**和**管理員密碼**。 

![填充 Azure 開發人員測試實驗室環境任務](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>應用服務部署任務
第三個任務是**Azure 應用服務部署**任務。 應用類型設置為 Web**應用**，應用服務名稱設置為 **$（WebSite）。**

![應用服務部署任務](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>設置發佈管道
創建具有兩個任務的發佈管道 **：Azure 部署：創建或更新資源組**和**部署 Azure 應用服務**。 

對於第一個任務，指定資源組的名稱和位置。 範本位置是連結專案。 如果資源管理器範本包含連結範本，則需要實施自訂資源組部署。 範本位於已發佈的刪除工件中。 覆蓋資源管理器範本的範本參數。 您可以將其餘設置保留為預設值。 

對於第二個任務 **"部署 Azure 應用服務**"，指定 Azure 訂閱，為**應用類型**選擇**Web 應用**，為**應用服務名稱**選擇 **$（WebSite）。** 您可以將其餘設置保留為預設值。 

## <a name="test-run"></a>測試執行
現在，兩個管道都已設置，手動排隊生成，並看到它工作。 下一步是為生成設置適當的觸發器，並將生成連接到發佈管道。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [將 Azure 開發人員測試實驗室集成到 Azure 管道中，持續集成和交付管道](devtest-lab-integrate-ci-cd-vsts.md)
- [將環境集成到 Azure 管道 CI/CD 管道中](integrate-environments-devops-pipeline.md)
