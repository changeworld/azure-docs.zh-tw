---
title: 在 Amazon Web Services 中自動部署 VM
description: 本文示範如何使用 Azure 自動化來自動化 Amazon Web 服務 VM 的建立
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604836"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure 自動化案例 - 佈建 AWS 虛擬機器
在本文中，您將了解如何利用「Azure 自動化」在 Amazon Web Service (AWS) 訂用帳戶中佈建虛擬機器，並為該 VM 提供一個特定名稱 (AWS 稱之為為 VM 「加上標籤」)。

## <a name="prerequisites"></a>Prerequisites
您需要具有 Azure 自動化帳戶和 Amazon Web 服務 (AWS) 訂閱。 如需設定 Azure 自動化帳戶以及使用 AWS 訂用帳戶認證進行設定的詳細資訊，請檢閱[使用 Amazon Web Services 設定驗證](automation-config-aws-account.md)。 在繼續操作之前,應使用 AWS 訂閱憑證創建或更新此帳戶,因為您在以下各節中引用此帳戶。

## <a name="deploy-amazon-web-services-powershell-module"></a>部署 Amazon Web Services PowerShell 模組
您的 VM 預先執行簿使用 AWS PowerShell 模組來執行其工作。 使用以下步驟將模組添加到配置了 AWS 訂閱認證的自動化帳戶。  

1. 開啟網頁瀏覽器，並瀏覽至 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AWSPowerShell/)，然後按一下 [部署至 Azure 自動化]**** 按鈕。<br><br> ![AWS PS 模組匯入](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. 系統會帶您前往 Azure 登入頁面，而在驗證之後，則會帶您到 Azure 入口網站並顯示下列頁面：<br><br> ![匯入模組頁面](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 選擇要使用的自動化帳戶,然後單擊"**確定**"以開始部署。

   > [!NOTE]
   > 當 Azure 自動化導入 PowerShell 模組時,它會提取 cmdlet。 在自動化完全導入模組並提取 cmdlet 之前,不會顯示這些活動。 此程序需要數分鐘的時間。  
   > <br>

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 按下「**資產」** 磁貼,在「資產」窗格中選擇 **「模組**」。。
3. 在 [模組] 頁面上，您會在清單中看到 **AWSPowerShell** 模組。

## <a name="create-aws-deploy-vm-runbook"></a>建立 AWS 部署 VM Runbook
部署 AWS PowerShell 模組之後，您現在即可撰寫 Runbook，以使用 PowerShell 指令碼自動在 AWS 中佈建虛擬機器。 以下步驟演示如何在 Azure 自動化中使用本機 PowerShell 腳本。  

> [!NOTE]
> 如需進一步選項以及此指令碼的相關資訊，請瀏覽 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-AwsVM/)。
> 

1. 以開啟 PowerShell 工作階段並鍵入以下指令,從 PowerShell 函式庫下載 PowerShell 文稿 New-AwsVM:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. 從 Azure 門戶中,打開自動化帳戶,並在 **「流程自動化**」下選擇**Runbook。**  
3. 從 [Runbook] 頁面中，選取 [新增 Runbook]****。
4. 在"添加 Runbook"窗格中,選擇 **"快速建立**"以建立新的 Runbook。
5. 在 Runbook 屬性窗格中,鍵入 Runbook 的名稱。
6. 從**Runbook 類型**下拉清單中,選擇**PowerShell,** 然後單擊"**創建**" 。<br><br> ![建立 Runbook 窗格](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. [編輯 PowerShell Runbook] 頁面出現時，將 PowerShell 指令碼複製並貼入 Runbook 撰寫畫布。<br><br> ![Runbook PowerShell 指令碼](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > 使用範例 PowerShell 指令碼時，請注意下列事項：
    > 
    > * Runbook 包含一些預設參數值。 請評估所有預設值，並視需要更新。
    > * 如果您將 AWS 認證儲存為`AWScred`與 名稱不同的認證資產,則需要更新第 57 行的腳本以相應地匹配。  
    > * 在 PowerShell 中使用 AWS CLI 命令 (特別是使用此範例 Runbook) 時，您必須指定 AWS 區域。 否則，Cmdlet 會失敗。 如需進一步詳細資訊，請檢視 AWS Tools for PowerShell 文件中的 AWS 主題： [指定 AWS 區域](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 。  
    >

7. 若要從 AWS 訂用帳戶擷取映像名稱清單，請啟動 PowerShell ISE 並匯入 AWS PowerShell 模組。 通過在 ISE`Get-AutomationPSCredential`環境中`AWScred = Get-Credential`替換為 AWS 進行身份驗證。 此語句提示您的認證,您可以提供使用者名的存取金鑰 ID 和密碼的機密存取金鑰。 

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
8. 複製並貼上在 Runbook 中引用的自動化變數中的影像名稱之一`$InstanceType`為 。 由於在此範例中,您使用的是免費 AWS 分層訂閱,因此使用**t2.micro**作為 runbook 範例。  
9. 儲存 Runbook，然後按一下 [發佈]**** 來發佈 Runbook，並在出現提示時按一下 [是]****。

### <a name="testing-the-aws-vm-runbook"></a>測試 AWS VM Runbook
在繼續測試 Runbook 之前,您需要驗證以下幾點:

* 已創建針對`AWScred`AWS 進行身份驗證的資產,或者腳本已更新以引用您的憑據資產名稱。    
* AWS PowerShell 模組已在 Azure 自動化中導入。  
* 已創建新的 Runbook,並在必要時驗證和更新參數值。  
* 在 Runbook**操作下****記錄詳情**紀錄與選擇**的紀錄紀錄進度紀錄**已設定為**On**。<br><br> ![Runbook 紀錄記錄與](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)追蹤 。  

1. 按下 **「開始」** 以啟動 Runbook,然後在「開始執行簿」窗格打開時按一下 **「 確定**」。
2. 在「開始運行簿」窗格中,提供 VM 名稱。 接受在腳本中預配置的其他參數的預設值。 按一下 [確定]**** 啟動 Runbook 工作。<br><br> ![啟動 New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 為您建立的 Runbook 作業打開工作窗格。 關閉此窗格。
4. 您可以通過從 Runbook 作業窗格中選擇 **「所有紀錄**」來查看作業的進度並查看輸出流。<br><br> ![串流輸出](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. 要確認正在預配 VM,請登入 AWS 管理主控台(如果您當前未登錄)。<br><br> ![AWS 主控台部署 VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>後續步驟
* 要開始使用圖形執行簿,請參閱[我的第一個圖形執行簿](automation-first-runbook-graphical.md)。
* 要開始使用 PowerShell 工作流執行簿,請參閱[我的第一個 PowerShell 工作流執行簿](automation-first-runbook-textual.md)。
* 要瞭解有關 Runbook 類型及其優點和限制的更多資訊,請參閱[Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 有關 PowerShell 文稿支援功能的詳細資訊,請參閱[Azure 自動化 中的本機 PowerShell 文稿支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。