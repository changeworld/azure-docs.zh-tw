---
title: 在 Amazon Web Services 中自動部署 VM
description: 本文示範如何使用 Azure 自動化來自動化 Amazon Web 服務 VM 的建立
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604836"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure 自動化案例 - 佈建 AWS 虛擬機器
在本文中，您將了解如何利用「Azure 自動化」在 Amazon Web Service (AWS) 訂用帳戶中佈建虛擬機器，並為該 VM 提供一個特定名稱 (AWS 稱之為為 VM 「加上標籤」)。

## <a name="prerequisites"></a>先決條件
您需要有 Azure 自動化帳戶和 Amazon Web Services （AWS）訂閱。 如需設定 Azure 自動化帳戶以及使用 AWS 訂用帳戶認證進行設定的詳細資訊，請檢閱[使用 Amazon Web Services 設定驗證](automation-config-aws-account.md)。 在繼續之前，您應該先使用您的 AWS 訂用帳號憑證來建立或更新此帳戶，因為您會在下列各節中參考此帳戶。

## <a name="deploy-amazon-web-services-powershell-module"></a>部署 Amazon Web Services PowerShell 模組
您的 VM 布建 runbook 會使用 AWS PowerShell 模組來執行其工作。 使用下列步驟，將模組新增至您的自動化帳戶，並使用您的 AWS 訂閱認證進行設定。  

1. 開啟網頁瀏覽器，並瀏覽至 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AWSPowerShell/)，然後按一下 [部署至 Azure 自動化]**** 按鈕。<br><br> ![AWS PS 模組匯入](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. 系統會帶您前往 Azure 登入頁面，而在驗證之後，則會帶您到 Azure 入口網站並顯示下列頁面：<br><br> ![匯入模組頁面](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 選取要使用的自動化帳戶，然後按一下 **[確定]** 以開始部署。

   > [!NOTE]
   > 當 Azure 自動化匯入 PowerShell 模組時，它會解壓縮 Cmdlet。 在自動化完成匯入模組並解壓縮 Cmdlet 之前，不會出現這些活動。 此程序需要數分鐘的時間。  
   > <br>

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 按一下 [**資產**] 圖格，然後在 [資產] 窗格中選取 [**模組**]。
3. 在 [模組] 頁面上，您會在清單中看到 **AWSPowerShell** 模組。

## <a name="create-aws-deploy-vm-runbook"></a>建立 AWS 部署 VM Runbook
部署 AWS PowerShell 模組之後，您現在即可撰寫 Runbook，以使用 PowerShell 指令碼自動在 AWS 中佈建虛擬機器。 下列步驟示範如何在 Azure 自動化中使用原生 PowerShell 腳本。  

> [!NOTE]
> 如需進一步選項以及此指令碼的相關資訊，請瀏覽 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-AwsVM/)。
> 

1. 開啟 PowerShell 會話，然後輸入下列命令，從 PowerShell 資源庫下載 PowerShell 腳本 New-awsvm：<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. 在 Azure 入口網站中，開啟您的自動化帳戶，然後選取 [程式**自動化**] 底下的 [ **runbook** ]。  
3. 從 [Runbook] 頁面中，選取 [新增 Runbook]****。
4. 在 [新增 runbook] 窗格上，選取 [**快速建立**] 以建立新的 runbook。
5. 在 [Runbook 屬性] 窗格中，輸入您的 Runbook 名稱。
6. 從 [ **Runbook 類型**] 下拉式清單中，選取 [ **PowerShell**]，然後按一下 [**建立**]。<br><br> ![建立 Runbook 窗格](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. [編輯 PowerShell Runbook] 頁面出現時，將 PowerShell 指令碼複製並貼入 Runbook 撰寫畫布。<br><br> ![Runbook PowerShell 指令碼](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > 使用範例 PowerShell 指令碼時，請注意下列事項：
    > 
    > * Runbook 包含一些預設參數值。 請評估所有預設值，並視需要更新。
    > * 如果您已將 AWS 認證儲存為認證資產`AWScred`，其名稱與不同，則您必須更新第57行上的腳本，以符合。  
    > * 在 PowerShell 中使用 AWS CLI 命令 (特別是使用此範例 Runbook) 時，您必須指定 AWS 區域。 否則，Cmdlet 會失敗。 如需進一步詳細資訊，請檢視 AWS Tools for PowerShell 文件中的 AWS 主題： [指定 AWS 區域](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 。  
    >

7. 若要從 AWS 訂用帳戶擷取映像名稱清單，請啟動 PowerShell ISE 並匯入 AWS PowerShell 模組。 使用`AWScred = Get-Credential`在您的 ISE `Get-AutomationPSCredential`環境中取代來對 AWS 進行驗證。 此語句會提示您輸入認證，您可以為使用者名稱和密碼存取金鑰提供您的存取金鑰識別碼。 

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    會傳回下列輸出：<br><br>
   ![取得 AWS 映像](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. 複製並貼上自動化變數中的其中一個映射名稱，如同 runbook 中所參考的`$InstanceType`一樣。 因為在此範例中，您使用的是免費的 AWS 階層式訂用帳戶，所以您可以使用**t2**作為 runbook 範例。  
9. 儲存 Runbook，然後按一下 [發佈]**** 來發佈 Runbook，並在出現提示時按一下 [是]****。

### <a name="testing-the-aws-vm-runbook"></a>測試 AWS VM Runbook
在您繼續測試 runbook 之前，您必須先確認一些事項：

* 已建立名`AWScred`為對 AWS 進行驗證的資產，或已更新腳本來參考您的認證資產名稱。    
* AWS PowerShell 模組已匯入 Azure 自動化。  
* 已建立新的 runbook，並在必要時驗證並更新參數值。  
* 記錄**詳細資訊記錄**，並選擇性地在 Runbook 作業記錄下記錄**進度記錄** **，並將追蹤**設定為 [**開啟**]。<br><br> ![Runbook 記錄和追蹤](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)。  

1. 按一下 [**啟動**] 以啟動 runbook，然後在 [啟動 runbook] 窗格開啟時按一下 **[確定]** 。
2. 在 [啟動 Runbook] 窗格上，提供 VM 名稱。 接受您在腳本中預先設定之其他參數的預設值。 按一下 [確定]**** 啟動 Runbook 工作。<br><br> ![啟動 New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 作業窗格會針對您所建立的 Runbook 作業開啟。 關閉此窗格。
4. 您可以從 [runbook 工作] 窗格中選取 [**所有記錄**]，以查看作業的進度並查看輸出資料流程。<br><br> ![串流輸出](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. 若要確認已布建 VM，請登入 AWS 管理主控台（如果您目前未登入）。<br><br> ![AWS 主控台部署 VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>後續步驟
* 若要開始使用圖形化 runbook，請參閱[我的第一個圖形化 runbook](automation-first-runbook-graphical.md)。
* 若要開始使用 PowerShell 工作流程 runbook，請參閱[我的第一個 powershell 工作流程 runbook](automation-first-runbook-textual.md)。
* 若要深入了解 Runbook 類型及其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 如需 PowerShell 腳本支援功能的詳細資訊，請參閱[Azure 自動化中的原生 PowerShell 腳本支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。