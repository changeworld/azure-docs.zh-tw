---
title: 使用 Azure 自動化 Runbook 來部署 Amazon Web Services VM
description: 本文說明如何自動建立 Amazon Web Services VM。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 456a7e4f07b2416e1d2037205574f2e7149e70e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185937"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>使用 Runbook 來部署 Amazon Web Services VM

在本文中，您將了解如何利用「Azure 自動化」在 Amazon Web Service (AWS) 訂用帳戶中佈建虛擬機器，並為該 VM 提供一個特定名稱 (AWS 稱之為為 VM 「加上標籤」)。

## <a name="prerequisites"></a>Prerequisites

您需要有 Azure 自動化帳戶和 Amazon Web Services (AWS) 訂用帳戶。 如需設定 Azure 自動化帳戶以及使用 AWS 訂用帳戶認證進行設定的詳細資訊，請檢閱[使用 Amazon Web Services 設定驗證](automation-config-aws-account.md)。 繼續之前，請先使用 AWS 訂用帳戶認證來建立或更新此帳戶，因為下列各節中會參考此帳戶。

## <a name="deploy-amazon-web-services-powershell-module"></a>部署 Amazon Web Services PowerShell 模組

VM 佈建 Runbook 使用 AWS PowerShell 模組來執行工作。 使用下列步驟，將模組新增至以 AWS 訂用帳戶認證所設定的自動化帳戶。  

1. 開啟網頁瀏覽器，並瀏覽至 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AWSPowerShell/)，然後按一下 [部署至 Azure 自動化] 按鈕。<br><br> ![AWS PS 模組匯入](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. 系統會帶您前往 Azure 登入頁面，而在驗證之後，則會帶您到 Azure 入口網站並顯示下列頁面：<br><br> ![匯入模組頁面](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 選取要使用的自動化帳戶，然後按一下 [確定] 以開始部署。

   > [!NOTE]
   > Azure 自動化匯入 PowerShell 模組時會擷取 Cmdlet。 在自動化完成匯入模組和擷取 Cmdlet 之前，不會出現這些活動。 此程序需要數分鐘的時間。  
   > <br>

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 按一下 [資產] 圖格，然後在 [資產] 窗格上選取 [模組]。
3. 在 [模組] 頁面上，您會在清單中看到 **AWSPowerShell** 模組。

## <a name="create-aws-deploy-vm-runbook"></a>建立 AWS 部署 VM Runbook

部署 AWS PowerShell 模組之後，您現在即可撰寫 Runbook，以使用 PowerShell 指令碼自動在 AWS 中佈建虛擬機器。 下列步驟示範如何在 Azure 自動化中使用原生 PowerShell 指令碼。  

> [!NOTE]
> 如需進一步選項以及此指令碼的相關資訊，請瀏覽 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-AwsVM/)。
> 

1. 開啟 PowerShell 工作階段並輸入下列命令，從 PowerShell 資源庫下載 PowerShell 指令碼 New-AwsVM：<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. 從 Azure 入口網站中，開啟自動化帳戶，然後在 [程序自動化] 下選取 [Runbook]。  
3. 從 [Runbook] 頁面中，選取 [新增 Runbook]。
4. 在 [新增 Runbook] 窗格上，選取 [快速建立] 以建立新的 Runbook。
5. 在 [Runbook 屬性] 窗格中，輸入 Runbook 的名稱。
6. 從 [Runbook 類型] 下拉式清單中，選取 [PowerShell]，然後按一下 [建立]。<br><br> ![建立 Runbook 窗格](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. [編輯 PowerShell Runbook] 頁面出現時，將 PowerShell 指令碼複製並貼入 Runbook 撰寫畫布。<br><br> ![Runbook PowerShell 指令碼](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    使用範例 PowerShell 指令碼時，請注意下列事項：

    * Runbook 包含一些預設參數值。 請評估所有預設值，並視需要更新。
    * 如果您已將 AWS 認證儲存為認證資產，而且名稱不是 `AWScred`，則需要更新指令碼第 57 行，使之相符。  
    * 在 PowerShell 中使用 AWS CLI 命令 (特別是使用此範例 Runbook) 時，您必須指定 AWS 區域。 否則，Cmdlet 會失敗。 如需進一步詳細資訊，請檢視 AWS Tools for PowerShell 文件中的 AWS 主題： [指定 AWS 區域](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 。  

8. 若要從 AWS 訂用帳戶擷取映像名稱清單，請啟動 PowerShell ISE 並匯入 AWS PowerShell 模組。 在 ISE 環境中將 `Get-AutomationPSCredential` 換成 `AWScred = Get-Credential`，以對 AWS 進行驗證。 此陳述式會提示您輸入認證，請提供存取金鑰識別碼作為使用者名稱，並提供祕密存取金鑰作為密碼。 

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
9. 針對 Runbook 中以 `$InstanceType` 參考的自動化變數，複製並貼上其中一個映像名稱。 在此範例中，因為您使用免費 AWS 層訂用帳戶，所以請在您的 Runbook 範例中使用 **t2.micro**。  
10. 儲存 Runbook，然後按一下 [發佈] 來發佈 Runbook，並在出現提示時按一下 [是]。

### <a name="test-the-aws-vm-runbook"></a>測試 AWS VM Runbook

1. 確認 Runbook 建立名為 `AWScred` 的資產以用於對 AWS 進行驗證，或更新指令碼以參考您的認證資產名稱。    
2. 驗證新的 Runbook，並確定已視需要更新所有參數值。
確定 AWS PowerShell 模組已匯入 Azure 自動化中。  
3. 在 Azure 自動化中，請在 Runbook 作業 [記錄和追蹤] 下，將 [記錄詳細記錄] 和 (選擇性) [記錄進度記錄] 設為 [開啟]。<br><br> ![Runbook 記錄和追蹤](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)。  
4. 按一下 [啟動] 以啟動 Runbook，然後在 [啟動 Runbook] 窗格開啟時，按一下 [確定]。
5. 在 [啟動 Runbook] 窗格上，提供 VM 名稱。 對於您在指令碼中預先設定的其他參數，請接受預設值。 按一下 [確定] 啟動 Runbook 工作。<br><br> ![啟動 New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. 作業窗格會針對您所建立的 Runbook 作業開啟。 關閉此窗格。
7. 您可以從 [Runbook 作業] 窗格中選取 [所有記錄]，以檢視作業的進度和輸出資料流。<br><br> ![串流輸出](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. 若要確認正在佈建 VM，請登入 AWS 管理主控台 (如果目前尚未登入)。<br><br> ![AWS 主控台部署 VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>後續步驟
 
* 若要了解支援哪些 Runbook，請參閱[Azure 自動化 Runbook 類型](automation-runbook-types.md)。
* 若要使用 Runbook，請參閱[在 Azure 自動化中管理 Runbook](manage-runbooks.md)。
* 如需 PowerShell 的詳細資料，請參閱 [PowerShell 文件](/powershell/scripting/overview)。
* 關於指令碼支援，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
