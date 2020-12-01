---
title: 適用于 Azure AD Connect 雲端布建的 PowerShell 模組 AADCloudSyncTools
description: 本文說明如何安裝 Azure AD Connect 雲端佈建代理程式。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba71d3eedbb75780c20499daa890556574494a35
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348132"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-provisioning"></a>適用于 Azure AD Connect 雲端布建的 PowerShell 模組 AADCloudSyncTools

隨著公開預覽重新整理2的發行，Microsoft 引進了 AADCloudSyncTools PowerShell 模組。  此課程模組提供一組實用的工具，可讓您用來協助管理 Azure AD Connect 雲端同步部署。

## <a name="pre-requisites"></a>必要條件
需要下列先決條件：
- 此課程模組使用 MSAL authentication，因此需要安裝 MSAL.PS 模組。 它不再依賴 Azure AD 或 Azure AD 預覽。   若要確認，請在系統管理員 PowerShell 視窗中執行 `Get-module MSAL.PS` 。 如果模組已正確安裝，您會收到回應。  您可以使用 `Install-AADCloudSyncToolsPrerequisites` 安裝最新版本的 MSAL.PS
- AzureAD PowerShell 模組。  某些 Cmdlet 會依賴 AzureAD PowerShell 模組的部分來完成其工作。  若要確認，請在系統管理員 PowerShell 視窗中執行 `Get-module AzureAD` 。 您應該會收到回應。  您可以使用 `Install-AADCloudSyncToolsPrerequisites` 安裝最新版的 AzureAD PowerShell 模組。
- 從 PowerShell 安裝模組可能會使用 TLS 1.2 來強制執行。  若要確定您可以安裝模組，請設定下列各項： \
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>安裝 AADCloudSyncTools PowerShell 模組
若要安裝和使用 AADCloudSyncTools 模組，請使用下列步驟：

1.  以系統管理許可權開啟 Windows PowerShell
2.  `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`輸入，然後按 enter 鍵。
3.  輸入或複製並貼上下列內容： 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  按 enter 鍵。
4.  若要確認已安裝模組，請輸入或複製並貼上下列 "
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  您現在應該會看到模組的相關資訊。
6.  下次執行
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  這會安裝 PowerShell Get 模組。  關閉 PowerShell 視窗。
8.  以系統管理許可權開啟 Windows PowerShell
9.  使用步驟3再次匯入模組。
10. 執行 `Install-AADCloudSyncToolsPrerequisites` 以安裝 MSAL 和 AzureAD 模組
11. 所有預先先決條件都應該已成功安裝 ![ 安裝模組](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools Cmdlet
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
使用 AzureAD 模組連接到 Azure AD 和 MSAL.PS 模組，以要求 Microsoft Graph 的權杖


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
匯出並封裝壓縮檔案中的所有疑難排解資料，如下所示：
 1. 使用 AADCloudSyncToolsVerboseLogs 啟動詳細資訊追蹤。  您可以在 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 資料夾中找到這些追蹤記錄。
 2. 收集3分鐘的追蹤記錄。
   您可以使用-TracingDurationMins 指定不同的時間，或略過詳細追蹤-SkipVerboseTrace
 3. 使用 Stop-AADCloudSyncToolsVerboseLogs 停止詳細資訊追蹤
 4. 收集過去24小時內事件檢視器記錄
 5. 將所有代理程式記錄檔、詳細資訊記錄檔和事件檢視器記錄，壓縮成使用者的 [檔] 資料夾下的壓縮 zip 檔案。 
 </br>您可以使用-OutputPath 指定不同的輸出檔案夾 \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
顯示 Azure AD 租使用者詳細資料和內部變數狀態

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
使用 Graph 取得 AD2AAD 服務主體，並傳回同步處理工作資訊。
也可以使用特定的同步作業識別碼作為參數來呼叫。

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
使用 Graph 取得 AD2AAD 服務主體，並傳回同步處理工作的排程。
也可以使用特定的同步作業識別碼作為參數來呼叫。

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
使用 Graph 取得 AD2AAD 服務主體，並傳回同步處理工作的架構。

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
使用 Graph 取得所提供同步處理工作識別碼的同步處理工作架構，並輸出所有篩選群組的範圍。

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
使用 Graph 取得 AD2AAD 服務主體，並傳回同步處理作業的設定。
也可以使用特定的同步作業識別碼作為參數來呼叫。

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
使用 Graph 取得 AD2AAD 服務主體，並傳回同步處理工作的狀態。
也可以使用特定的同步作業識別碼作為參數來呼叫。

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
使用 Graph 取得 AD2AAD 和/或 SyncFabric 的服務主體 (s) 。
如果沒有參數，將只會傳回 AD2AAD 服務主體 (s) 。

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
檢查 PowerShellGet v 2.2.4.1 或更新版本是否存在，並 Azure AD 和 MSAL.PS 模組，並安裝這些模組（如果遺失的話）。

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
針對指定為參數的 URI、方法和主體叫用 Web 要求

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
使用 Azure AD PowerShell 來刪除目前的帳戶 (如果有) ，並使用 Azure AD 中的新同步處理帳戶重設同步帳戶驗證。

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
重新開機完整同步處理。

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
繼續從先前的浮水印進行同步處理。

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
修改 ' AADConnectProvisioningAgent.exe.config ' 以啟用詳細資訊追蹤並重新啟動 AADConnectProvisioningAgent 服務您可以使用-SkipServiceRestart 以防止重新開機服務，但任何設定變更將不會生效。  您可以在 C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace 資料夾中找到這些追蹤記錄。

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
修改 ' AADConnectProvisioningAgent.exe.config ' 以停用詳細資訊追蹤並重新啟動 AADConnectProvisioningAgent 服務。 您可以使用-SkipServiceRestart 來防止服務重新開機，但是任何設定變更將不會生效。

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
暫停同步處理。

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)

