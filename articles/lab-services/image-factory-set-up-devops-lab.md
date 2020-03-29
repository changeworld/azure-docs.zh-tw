---
title: 在 Azure 開發人員測試實驗室中從 Azure DevOps 運行映射工廠
description: 本文介紹從 Azure DevOps（以前的視覺化工作室團隊服務）運行映射工廠所需的所有準備工作。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758677"
---
# <a name="run-an-image-factory-from-azure-devops"></a>從 Azure DevOps 執行映像處理站
本文介紹從 Azure DevOps（以前的視覺化工作室團隊服務）運行映射工廠所需的所有準備工作。

> [!NOTE]
> 任何業務流程引擎都將工作！ Azure DevOps 不是強制性的。 映射工廠使用 Azure PowerShell 腳本運行，因此可以使用 Windows 任務計畫程式、其他 CI/CD 系統等手動運行映射。

## <a name="create-a-lab-for-the-image-factory"></a>為映射工廠創建實驗室
設置映射工廠的第一步是在 Azure 開發人員測試實驗室中創建實驗室。 本實驗室是映射工廠實驗室，我們在此創建虛擬機器並保存自訂映射。 本實驗被視為整個圖像工廠過程的一部分。 創建實驗室後，請確保保存名稱，因為稍後需要它。

## <a name="scripts-and-templates"></a>腳本和範本
為您的團隊採用映射工廠的下一步是瞭解可用的功能。 映射工廠腳本和範本在[開發人員測試實驗室 GitHub 存儲庫中](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)公開提供。 以下是部分的概述：

- 圖像工廠。 它是根資料夾。
    - 設定。 映射工廠的輸入
        - 黃金圖像。 此資料夾包含表示自訂圖像定義的 JSON 檔。
        - 實驗室.json. 檔團隊註冊以接收特定的自訂映射。
- 腳本。 映射工廠的發動機。

本節中的文章提供了有關這些腳本和範本的更多詳細資訊。

## <a name="create-an-azure-devops-team-project"></a>創建 Azure DevOps 團隊專案
Azure DevOps 允許您存儲原始程式碼，在一個位置運行 Azure PowerShell。 您可以安排定期運行，使映射保持最新。 記錄結果以診斷任何問題有良好的功能。  但是，使用 Azure DevOps 不是一項要求，您可以使用任何可以連接到 Azure 並可以運行 Azure PowerShell 的線束/引擎。

如果您有要使用的現有 DevOps 帳戶或專案，請跳過此步驟。

要開始，請在 Azure DevOps 中創建一個免費帳戶。 訪問https://www.visualstudio.com/並選擇**Azure DevOps（** 以前的 VSTS）下的**免費入門**。 您需要選擇唯一的帳戶名稱，並確保選擇使用 Git 管理代碼。 創建此選項後，將 URL 保存到團隊專案中。 下面是一個示例 URL： `https://<accountname>.visualstudio.com/MyFirstProject`。

## <a name="check-in-the-image-factory-to-git"></a>簽入映射工廠到 Git
映射工廠的所有 PowerShell、範本和配置都位於[公共 DevTest Labs GitHub 存儲庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中。 將代碼導入新團隊專案的最快方法是導入存儲庫。 這將提取整個 DevTest Labs 存儲庫（因此您將獲得額外的文檔和示例）。

1. 訪問您在上一步中創建的 Azure DevOps 專案（URL 看起來像**HTTPs：\//\<帳戶名稱>.visualstudio.com/MyFirstProject）。**
2. 選擇 **"導入存儲庫**"。
3. 輸入 DevTest 實驗室回購的**克隆 URL：** `https://github.com/Azure/azure-devtestlab`。
4. 選取 [匯入]****。

    ![導入 Git 存儲庫](./media/set-up-devops-lab/import-git-repo.png)

如果您決定只簽入所需的內容（映射工廠檔），請按照[此處](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)的步驟克隆 Git 存儲庫，並僅推送**腳本/ImageFactory**目錄中的檔。

## <a name="create-a-build-and-connect-to-azure"></a>創建生成並連接到 Azure
此時，在 Azure DevOps 中的 Git 存儲庫中存儲原始檔案。 現在，您需要設置管道以運行 Azure PowerShell。 執行這些步驟有很多選項。 在本文中，使用組建定義是為了簡單起見，但它適用于 DevOps 生成、DevOps 發佈（單個或多個環境）、其他執行引擎（如 Windows 任務計畫程式）或任何其他可以執行 Azure PowerShell 的工具。

> [!NOTE]
> 要記住的一個要點是，當要創建大量 （10+） 自訂映射時，某些 PowerShell 檔需要很長時間才能運行。 免費託管 DevOps 生成/發佈代理的超時為 30 分鐘，因此，一旦開始構建許多映射，您就無法使用免費託管代理。 此超時挑戰適用于您決定使用的任何工具，最好預先驗證是否可以延長長時間運行 Azure PowerShell 腳本的典型超時。 在 Azure DevOps 中，可以使用付費託管代理或使用自己的生成代理。

1. 要啟動，請選擇在 DevOps 專案的主頁上**設置"生成**"：

    ![設置製作按鈕](./media/set-up-devops-lab/setup-build-button.png)
2. 指定生成**的名稱**（例如：生成映射並將圖像傳遞到 DevTest 實驗室）。
3. 選擇**空**組建定義，然後選擇 **"應用"** 以創建生成。
4. 在此階段，您可以選擇生成代理的 **"託管**"。
5. **保存**組建定義。

    ![組建定義](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>配置生成變數
要簡化命令列參數，封裝將映射工廠驅動到一組生成變數的鍵值。 選擇 **"變數"** 選項卡，您將看到多個預設變數的清單。 下面是要輸入到 Azure DevOps 的變數清單：


| 變數名稱 | 值 | 注意 |
| ------------- | ----- | ----- |
| 配置位置 | /腳本/映射工廠/配置 | 這是存儲庫中到 **"配置"** 資料夾的完整路徑。 如果上述導入了整個回購，則左側的值是正確的。 否則更新以指向"配置"位置。 |
| 開發測試實驗室名稱 | 我的圖像工廠 | Azure 開發人員測試實驗室中實驗室的名稱用作生成映射的工廠。 如果沒有，請創建一個。 確保實驗室與服務終結點有權訪問的訂閱相同。 |
| 圖像保留 | 1 | 要保存每種類型的圖像數。 將預設值設置為 1。 |
| MachinePassword | ******* | 虛擬機器的內置管理員帳戶密碼。 這是一個瞬態帳戶，因此請確保它是安全的。 選擇右側的小鎖圖示，以確保它是一個安全的字串。 |
| 機器使用者名 | 圖像工廠使用者 | 虛擬機器的內置管理員帳戶使用者名。 這是一個瞬態帳戶。 |
| 標準超時分鐘數 | 30 | 超時，我們應該等待常規的 Azure 操作。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 實驗室存在且服務終結點有權訪問的訂閱的 ID。 |
| VMSize | Standard_A3 | 要用於**創建**步驟的虛擬機器的大小。 創建的 VM 是瞬態的。 大小必須是[為實驗室啟用的](devtest-lab-set-lab-policy.md)。 確認有足夠的[訂閱核心配額](../azure-resource-manager/management/azure-subscription-service-limits.md)。

![生成變數](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>連線到 Azure
下一步是設置服務主體。 這是 Azure 活動目錄中的標識，使 DevOps 生成代理能夠代表使用者在 Azure 中操作。 要設置它，首先添加第一個 Azure PowerShell 生成步驟。

1. 選擇 **"添加任務**"。
2. 搜索**Azure 電源外殼**。
3. 找到它後，選擇 **"添加"** 以將任務添加到生成。 執行此操作時，您將看到任務在左側顯示為添加。

![設置 PowerShell 步驟](./media/set-up-devops-lab/set-up-powershell-step.png)

設置服務主體的最快方法是讓 Azure DevOps 為我們執行此操作。

1. 選擇您剛剛添加**的任務**。
2. 對於**Azure 連線類型**，請選擇**Azure 資源管理器**。
3. 選擇 **"管理**"連結以設置服務主體。

有關詳細資訊，請參閱此[博客文章](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)。 選擇 **"管理"** 連結時，您將在 DevOps（博客文章中的第二個螢幕截圖）中的位置放置，以設置與 Azure 的連接。 設置 Azure**資源管理器服務終結點**時，請確保選擇該終結點。

## <a name="complete-the-build-task"></a>完成生成任務
如果選擇生成任務，您將看到應填寫的右側窗格中的所有詳細資訊。

1. 首先，命名生成任務：**創建虛擬機器**。
2. 選擇通過選擇**Azure 資源管理器**創建的**服務主體**
3. 選擇**服務終結點**。
4. 對於**腳本路徑**，請選擇 **...（省略）** 在右邊。
5. 導航到**製作 GoldenImageVM.ps1**腳本。
6. 腳本參數應如下所示：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![完成組建定義](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>對生成進行排隊
讓我們通過排隊新生成來驗證所有內容都已正確設置。 在生成運行時，切換到[Azure 門戶](https://portal.azure.com)，並在映射工廠實驗室**中的所有虛擬機器**上選擇，以確認一切工作正常。 您應該會看到在實驗室中創建三個虛擬機器。

![實驗室中的 VM](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>後續步驟
基於 Azure 開發人員測試實驗室設置映射工廠的第一步已完成。 在本系列的下一篇文章中，您可以對這些 VM 進行泛化並保存到自訂映射。 然後，將它們分發到所有其他實驗室。 請參閱本系列的下一篇文章：[保存自訂映射並分發到多個實驗室](image-factory-save-distribute-custom-images.md)。
