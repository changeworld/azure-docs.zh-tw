---
title: 從中的 Azure DevOps 執行映射 factory Azure DevTest Labs
description: 本文涵蓋從 Azure DevOps （先前稱為 Visual Studio Team Services）執行映射 factory 所需的所有準備工作。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85476133"
---
# <a name="run-an-image-factory-from-azure-devops"></a>從 Azure DevOps 執行映像處理站
本文涵蓋從 Azure DevOps （先前稱為 Visual Studio Team Services）執行映射 factory 所需的所有準備工作。

> [!NOTE]
> 任何協調流程引擎都可以使用！ Azure DevOps 不是必要的。 映射處理站是使用 Azure PowerShell 腳本來執行，因此可以使用 Windows 工作排程器、其他 CI/CD 系統等來手動執行。

## <a name="create-a-lab-for-the-image-factory"></a>建立映射 factory 的實驗室
設定映射 factory 的第一個步驟是在 Azure DevTest Labs 中建立實驗室。 此實驗室是「映射工廠」實驗室，我們會在其中建立虛擬機器並儲存自訂映射。 此實驗室會被視為整體映射處理站程式的一部分。 建立實驗室之後，請務必儲存名稱，因為您稍後將會用到它。

## <a name="scripts-and-templates"></a>腳本和範本
針對您的小組採用「映射處理站」的下一個步驟是瞭解可用的專案。 您可以在[DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)存放庫中公開取得映射 factory 腳本和範本。 以下是各部分的大綱：

- 映射 Factory。 它是根資料夾。
    - 設定。 影像 factory 的輸入
        - GoldenImages. 此資料夾包含 JSON 檔案，代表自訂映射的定義。
        - Labs.js。 檔案，小組會在其中註冊以接收特定的自訂映射。
- 文字. 映射 factory 的引擎。

本節中的文章提供有關這些腳本和範本的更多詳細資料。

## <a name="create-an-azure-devops-team-project"></a>建立 Azure DevOps team 專案
Azure DevOps 可讓您儲存原始程式碼，請在一處執行 Azure PowerShell。 您可以排程週期性執行，讓影像保持在最新狀態。 有很好的工具可以記錄結果，以診斷任何問題。  不過，不需要使用 Azure DevOps，您可以使用任何可連線到 Azure 並可執行 Azure PowerShell 的控管/引擎。

如果您有想要改為使用的現有 DevOps 帳戶或專案，請略過此步驟。

若要開始使用，請在 Azure DevOps 中建立免費帳戶。 流覽 https://www.visualstudio.com/ 並選取 [**免費開始**使用] **AZURE DEVOPS** （先前為 VSTS）。 您必須選擇唯一的帳戶名稱，並務必選擇使用 Git 來管理程式碼。 一旦建立此專案，請將 URL 儲存至您的 team 專案。 以下是範例 URL： `https://<accountname>.visualstudio.com/MyFirstProject` 。

## <a name="check-in-the-image-factory-to-git"></a>將映射 factory 簽入 Git
映射 factory 的所有 PowerShell、範本和設定都位於[公用 DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)存放庫中。 將程式碼放入新的 team 專案的最快方式是匯入存放庫。 這會提取整個 DevTest Labs 存放庫（因此您會取得額外的檔和範例）。

1. 流覽您在上一個步驟中建立的 Azure DevOps 專案（URL 看起來像**HTTPs： \/ / \<accountname> . visualstudio.com/MyFirstProject**）。
2. 選取 [匯**入存放庫**]。
3. 輸入 DevTest Labs 存放庫的**複製 URL** ： `https://github.com/Azure/azure-devtestlab` 。
4. 選取 [匯入]。

    ![匯入 Git 存放庫](./media/set-up-devops-lab/import-git-repo.png)

如果您決定只檢查所需的內容（映射 factory 檔案），請遵循[這裡](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)的步驟來複製 Git 存放庫，並只推送位於**scripts/ImageFactory**目錄中的檔案。

## <a name="create-a-build-and-connect-to-azure"></a>建立組建並連接至 Azure
此時，您的來源檔案儲存在 Azure DevOps 的 Git 存放庫中。 現在，您必須設定管線來執行 Azure PowerShell。 有許多選項可執行這些步驟。 在本文中，您會使用組建定義來簡化，但它適用于 DevOps 組建、DevOps 發行（單一或多個環境）、其他執行引擎（例如 Windows 工作排程器），或任何可執行 Azure PowerShell 的其他工具。

> [!NOTE]
> 有一點要牢記在心，當有很多（10 +）個自訂映射要建立時，某些 PowerShell 檔案會花很長的時間來執行。 免費裝載的 DevOps 組建/發行代理程式的超時為30分鐘，因此在您開始建立許多映射後，就無法使用免費的託管代理程式。 這個超時挑戰適用于您決定要使用的任何控管，最好先進行驗證，以延長長時間執行 Azure PowerShell 腳本的一般超時。 在 Azure DevOps 的情況下，您可以使用付費裝載的代理程式，或使用您自己的組建代理程式。

1. 若要開始，請在 DevOps 專案的首頁上，選取 [**設定組建**]：

    ![設定組建按鈕](./media/set-up-devops-lab/setup-build-button.png)
2. 指定組建的**名稱**（例如：組建並將映射傳遞至 DevTest Labs）。
3. 選取**空白**的組建定義，然後選取 [套用 **] 以建立您的組建**。
4. 在這個階段，您可以選擇 [裝載于組建**代理程式]** 。
5. **儲存**組建定義。

    ![組建定義](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>設定組建變數
若要簡化命令列參數，請將驅動影像處理站的索引鍵值封裝成一組組建變數。 選取 [**變數**] 索引標籤，您會看到數個預設變數的清單。 以下是要在 Azure DevOps 中輸入的變數清單：


| 變數名稱 | 值 | 注意 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | 這是存放庫中**設定資料夾的**完整路徑。 如果您已匯入上述整個存放庫，左邊的值是正確的。 否則，更新以指向設定位置。 |
| DevTestLabName | MyImageFactory | Azure DevTest Labs 中的實驗室名稱，用來做為產生影像的 factory。 如果您沒有帳戶，請建立一個。 請確定實驗室位於服務端點可存取的相同訂用帳戶中。 |
| ImageRetention | 1 | 您想要儲存每個類型的影像數目。 將 [預設值] 設定為1。 |
| MachinePassword | ******* | 虛擬機器的內建系統管理員帳戶密碼。 這是暫時性帳戶，因此請確定它是安全的。 選取右邊的小鎖定圖示，以確保其為安全字串。 |
| MachineUserName | ImageFactoryUser | 虛擬機器的內建系統管理員帳戶使用者名稱。 這是暫時性帳戶。 |
| StandardTimeoutMinutes | 30 | 我們應該等待定期 Azure 作業的時間。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 實驗室所在的訂用帳戶識別碼，以及服務端點可存取的。 |
| VMSize | Standard_A3 | 要用於**建立**步驟的虛擬機器大小。 建立的 Vm 是暫時性的。 大小必須是[為實驗室啟用](devtest-lab-set-lab-policy.md)的大小。 確認有足夠的訂用帳戶[核心配額](../azure-resource-manager/management/azure-subscription-service-limits.md)。

![組建變數](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>連線到 Azure
下一個步驟是設定服務主體。 這是 Azure Active Directory 中的身分識別，可讓 DevOps 組建代理程式代表使用者在 Azure 中操作。 若要進行設定，請從新增您的第一個 Azure PowerShell 組建步驟開始。

1. 選取 [**新增**工作]。
2. 搜尋**Azure PowerShell**。
3. 找到之後，請選取 [**新增**] 將工作新增至組建。 當您這麼做時，您會看到工作在左側顯示為 [已新增]。

![設定 PowerShell 步驟](./media/set-up-devops-lab/set-up-powershell-step.png)

設定服務主體的最快方式是讓 Azure DevOps 為我們執行此動作。

1. 選取您**剛才**新增的工作。
2. 針對 [ **Azure 連線類型**]，選擇 [ **Azure Resource Manager**]。
3. 選取 [**管理**] 連結以設定服務主體。

如需詳細資訊，請參閱這篇[部落格文章](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)。 當您選取 [**管理**] 連結時，您會在 DevOps 中放置正確的位置（在 blog 文章中的第二個螢幕擷取畫面）來設定與 Azure 的連線。 設定此設定時，請務必選擇 [ **Azure Resource Manager 服務端點**]。

## <a name="complete-the-build-task"></a>完成組建工作
如果您選取 [組建] 工作，您會在右窗格中看到應填入的所有詳細資料。

1. 首先，將組建工作命名為：**建立虛擬機器**。
2. 選擇您所建立的**服務主體**，方法是選擇 [ **Azure Resource Manager**
3. 選擇**服務端點**。
4. 在 [**腳本路徑**] 中，選取 [ **...]（省略號）** 右側。
5. 流覽至**MakeGoldenImageVMs.ps1**腳本。
6. 腳本參數看起來應該像這樣：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![完成組建定義](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>將組建排到佇列
讓我們藉由將新的組建排入佇列，確認您已正確設定所有專案。 在組建執行時，切換至[Azure 入口網站](https://portal.azure.com)並選取映射工廠實驗室中的**所有虛擬機器**，以確認一切都正常運作。 您應該會看到在實驗室中建立三部虛擬機器。

![實驗室中的 Vm](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>後續步驟
根據 Azure DevTest Labs 設定映射 factory 的第一個步驟是完成。 在系列的下一篇文章中，您會取得一般化並儲存至自訂映射的 Vm。 然後，將它們散發到所有其他實驗室。 請參閱系列中的下一篇文章：[儲存自訂映射並散發至多個實驗室](image-factory-save-distribute-custom-images.md)。
