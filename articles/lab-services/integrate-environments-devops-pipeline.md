---
title: 在 Azure 開發人員測試實驗室中將環境集成到 Azure 管道中
description: 瞭解如何將 Azure 開發人員測試實驗室環境集成到 Azure DevOps 持續集成 （CI） 和連續交付 （CD） 管道中。
services: devtest-lab,virtual-machines,lab-services
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
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169415"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>將環境集成到 Azure DevOps CI/CD 管道中
您可以使用 Azure DevOps 服務（以前稱為視覺化工作室團隊服務）中安裝的 Azure DevTest Labs 任務擴展，輕鬆將連續集成 （CI）/連續交付 （CD） 生成和發佈管道與 Azure DevTest 實驗室集成。 通過這些擴展，可以更輕鬆地為特定的測試工作快速部署[環境](devtest-lab-test-env.md)，然後在測試完成後將其刪除。 

本文演示如何創建和部署環境，然後刪除環境，所有這些都位於一個完整的管道中。 通常，您將在您自己的自訂生成測試部署管道中單獨執行這些任務。 本文中使用的擴展是這些[創建/刪除 DTL VM 任務](devtest-lab-integrate-ci-cd-vsts.md)的補充：

- 建立環境
- 刪除環境

## <a name="before-you-begin"></a>開始之前
在將 CI/CD 管道與 Azure 開發人員測試實驗室集成之前，請從視覺化工作室應用商店安裝[Azure 開發人員測試實驗室任務](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)擴展。 

## <a name="create-and-configure-the-lab-for-environments"></a>為環境創建和配置實驗室
本節介紹如何創建和配置將部署 Azure 環境的實驗室。

1. 如果還沒有[實驗室，請創建一個實驗室](devtest-lab-create-lab.md)。 
2. 配置實驗室並按照本文中的說明創建環境範本：[使用 Azure 資源管理器範本創建多 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。
3. 在此示例中，使用現有的 Azure 快速啟動範本[https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)。
4. 將**201 網路應用-redis-cache-sql 資料庫**資料夾複製到步驟 2 中配置的存儲庫中的**ArmTemplate**資料夾中。

## <a name="create-a-release-definition"></a>建立發行定義
若要建立發行定義，請執行下列動作：

1.  在 **"生成&發佈中心**"的 **"發佈**"選項卡上，選擇**加號 （+）** 按鈕。
2.  在 [建立發行定義]**** 視窗中，選取 [空白]**** 範本，然後選取 [下一步]****。
3.  選取 [稍後選擇]****，然後選取 [建立]****，以使用一個預設的環境、但不使用任何連結的構件來建立新的發行定義。
4.  要打開快顯功能表，請在新版本定義中，選擇環境名稱旁邊的**省略號 （...），** 然後選擇 **"組態變數**"。
5.  在 [設定 - 環境]**** 視窗中，為您在發行定義工作中使用的變數輸入下列值：
1.  對於**管理員登錄**，輸入 SQL 管理員登錄名。
2.  對於**管理員登錄密碼**，輸入 SQL 管理員登錄所使用的密碼。 使用「掛鎖」圖示可隱藏及保護密碼。
3.  對於**資料庫名稱**，請輸入 SQL 資料庫名稱。
4.  這些變數特定于示例環境，不同的環境可能具有不同的變數。

## <a name="create-an-environment"></a>建立環境
部署的下一階段是創建用於開發或測試目的的環境。

1. 在發行定義中，選取 [新增工作]****。
2. 在"**任務"** 選項卡上，添加 Azure 開發人員測試實驗室創建環境任務。 請依照下列方式設定工作：
    1. 針對 [Azure RM 訂用帳戶]****，選取 [可用的 Azure 服務連線]**** 清單中的連線，或對您的 Azure 訂用帳戶建立權限更具限制性的連線。 如需詳細資訊，請參閱 [Azure Resource Manager 服務端點](/azure/devops/pipelines/library/service-endpoints)。
2. 對於**實驗室名稱**，請選擇您之前創建的實例的名稱*。
3. 對於**存儲庫名稱**，選擇將資源管理器範本 （201） 推送到*的存儲庫。
4. 對於**範本名稱**，請選擇保存到原始程式碼存儲庫的環境的名稱*。 
5. **實驗室名稱**、**存儲庫名稱**和**範本名稱**是 Azure 資源識別碼 的友好表示形式。 手動輸入易記名稱將導致失敗，請使用下拉清單選擇資訊。
6. 對於**環境名稱**，請輸入一個名稱以唯一標識實驗室中的環境實例。  它在實驗室內必須是唯一的。
7. **參數檔和****參數**允許將自訂參數傳遞給環境。 或者兩者都可用於設置參數值。 在此示例中，將使用參數部分。 使用環境中定義的變數的名稱，例如：`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 環境範本中的資訊可以在範本的輸出部分傳遞。 檢查**基於環境範本輸出創建輸出變數，** 以便其他任務可以使用資料。 `$(Reference name.Output Name)`是要遵循的模式。 例如，如果引用名稱為 DTL，並且範本中的輸出名稱位於該變數的位置，則變數將為`$(DTL.location)`。

## <a name="delete-the-environment"></a>刪除環境
最後一個階段是刪除在 Azure 開發人員測試實驗室實例中部署的環境。 通常在執行開發工作或運行已部署資源上所需的測試後刪除環境。

在發佈定義中，選擇 **"添加任務**"，然後在 **"部署"** 選項卡上添加**Azure 開發人員測試實驗室刪除環境**任務。 進行下列設定：

1. 要刪除 VM，請參閱[Azure 開發人員測試實驗室任務](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)：
    1. 針對 [Azure RM 訂用帳戶]****，選取 [可用的 Azure 服務連線]**** 清單中的連線，或對您的 Azure 訂用帳戶建立權限更具限制性的連線。 如需詳細資訊，請參閱 [Azure Resource Manager 服務端點](/azure/devops/pipelines/library/service-endpoints)。
    2. 對於**實驗室名稱**，選擇存在環境的實驗室。
    3. 對於**環境名稱**，請輸入要刪除的環境的名稱。
2. 輸入發行定義的名稱，並加以儲存。

## <a name="next-steps"></a>後續步驟
查看下列文章： 
- [使用資源管理器範本創建多 VM 環境](devtest-lab-create-environment-from-arm.md)。
- 從[開發人員測試實驗室 GitHub 存儲庫](https://github.com/Azure/azure-quickstart-templates)快速啟動開發人員測試實驗室自動化的資源管理器範本。
- [VSTS 故障排除頁面](/azure/devops/pipelines/troubleshooting)

