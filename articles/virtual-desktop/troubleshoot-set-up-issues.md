---
title: Windows 虛擬桌面租戶主機池創建 - Azure
description: 在設置 Windows 虛擬桌面租戶環境期間如何排除和解決租戶和主機池問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371591"
---
# <a name="tenant-and-host-pool-creation"></a>建立租用戶和主機集區

本文介紹 Windows 虛擬桌面租戶和相關工作階段主機池基礎結構的初始設置期間的問題。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>獲取 Windows 10 企業多會話映射

要使用 Windows 10 企業多會話映射，請轉到 Azure 應用商店，選擇"**啟動** > **Microsoft Windows 10** >和[適用于虛擬桌面的 Windows 10 企業版，版本 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

![選擇適用于虛擬桌面的 Windows 10 企業版的螢幕截圖，版本 1809。](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>創建 Windows 虛擬桌面租戶

本節介紹創建 Windows 虛擬桌面租戶時的潛在問題。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>錯誤：使用者無權查詢管理服務

![無權查詢管理服務的 PowerShell 視窗的螢幕截圖。](media/UserNotAuthorizedNewTenant.png)

原始錯誤示例：

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**原因：** 已登錄的使用者尚未在其 Azure 活動目錄中分配租戶建立者角色。

**修復：** 按照將[租戶建立者應用程式角色分配給 Azure 活動目錄租戶中的使用者](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)的說明進行操作。 按照說明操作後，您將將使用者分配給租戶建立者角色。

![已分配租戶建立者角色的螢幕截圖。](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>創建 Windows 虛擬桌面工作階段主機 VM

可以通過多種方式創建工作階段主機 VM，但 Windows 虛擬桌面團隊僅支援與[Azure 應用商店](https://azuremarketplace.microsoft.com/)產品相關的 VM 預配問題。 有關詳細資訊，請參閱使用[Windows 虛擬桌面的問題 - 預配主機池 Azure 應用商店產品](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)。

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Windows 虛擬桌面的問題 – 預配主機池 Azure 應用商店產品

Windows 虛擬桌面 – 預配主機池範本可從 Azure 應用商店獲得。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>錯誤：使用 GitHub 中的連結時，將顯示消息"創建免費帳戶"

![創建免費帳戶的螢幕截圖。](media/be615904ace9832754f0669de28abd94.png)

**原因 1：** 用於登錄到 Azure 的帳戶中沒有活動訂閱，或者所使用的帳戶沒有查看訂閱的許可權。

**修復 1：** 使用具有參與者存取權限（至少）的帳戶登錄，該訂閱將部署工作階段主機 VM。

**原因 2：** 正在使用的訂閱是 Microsoft 雲服務提供者 （CSP） 租戶的一部分。

**修復 2：** 轉到 GitHub 位置**以創建和預配新的 Windows 虛擬桌面主機池**，然後按照以下說明操作：

1. 按右鍵 **"部署到 Azure"** 並選擇 **"複製連結位址**"。
2. 打開**記事本**並粘貼連結。
3. 在 # 字元之前，插入 CSP 最終客戶租戶名稱。
4. 在瀏覽器中打開新連結，Azure 門戶將載入範本。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>錯誤：您收到"範本部署無效"錯誤

!["範本部署的螢幕截圖...無效"錯誤](media/troubleshooting-marketplace-validation-error-generic.png)

在採取特定操作之前，您需要檢查活動日誌以查看部署驗證失敗的詳細錯誤。

要查看活動日誌中的錯誤，請進行以下工作：

1. 退出當前 Azure 應用商店部署產品。
2. 在頂部搜索欄中，搜索並選擇**活動日誌**。
3. 查找狀態為 **"失敗"** 的名為 **"驗證部署"** 的活動並選擇該活動。
   ![具有 [失敗] 狀態的單個 [驗證部署] 活動的螢幕截圖](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. 選擇 JSON，然後向下滾動到螢幕底部，直到看到"狀態訊息"欄位。
   ![失敗活動的螢幕截圖，JSON 文本的狀態Message屬性周圍有一個紅色框。](media/troubleshooting-marketplace-validation-error-json-boxed.png)

如果操作範本超出配額限制，則可以執行以下操作之一來修復它：

 - 使用首次使用的參數運行 Azure 應用商店，但這次使用的 VM 和 VM 內核較少。
 - 打開您在瀏覽器中**的狀態Message**欄位中看到的連結，以提交請求，以增加指定 VM SKU 的 Azure 訂閱配額。

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure 資源管理器範本和 PowerShell 所需狀態配置 （DSC） 錯誤

請按照這些說明對 Azure 資源管理器範本和 PowerShell DSC 的不成功部署進行故障排除。

1. 使用 Azure 資源管理器使用[視圖部署操作](../azure-resource-manager/resource-manager-deployment-operations.md)查看部署中的錯誤。
2. 如果部署中沒有錯誤，請使用"查看活動日誌"審核活動日誌中的錯誤[以審核對資源的操作](../azure-resource-manager/resource-group-audit.md)。
3. 識別錯誤後，請使用與[Azure 資源管理器一起排除常見 Azure 部署錯誤](../azure-resource-manager/resource-manager-common-deployment-errors.md)中的錯誤訊息和資源來解決此問題。
4. 刪除上次部署期間創建的任何資源，然後重試再次部署範本。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>錯誤：您的部署失敗>...\<

![您的部署失敗螢幕截圖。](media/e72df4d5c05d390620e07f0d7328d50f.png)

原始錯誤示例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1：** 為將 VM 加入域提供的憑據不正確。

**修復 1：** 請參閱會話[主機 VM 配置](troubleshoot-vm-configuration.md)中 VM 中未加入域的 VM 的"憑據不正確"錯誤。

**原因 2：** 功能變數名稱無法解析。

**修復 2：** 請參閱錯誤：在[工作階段主機 VM 配置](troubleshoot-vm-configuration.md)中[，功能變數名稱未解析](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)。

**原因 3：** 您的虛擬網路 （VNET） DNS 配置設置為 **"預設**"。

要解決此問題，可以執行以下操作：

1. 打開 Azure 門戶並轉到**虛擬網路**選項卡。
2. 查找您的 VNET，然後選擇**DNS 伺服器**。
3. DNS 伺服器功能表應顯示在螢幕右側。 在該功能表上，選擇 **"自訂**"。
4. 確保自訂下列出的 DNS 伺服器與您的網域控制站或活動目錄域匹配。 如果看不到 DNS 伺服器，則可以通過在"**添加 DNS 伺服器"** 欄位中輸入其值來添加它。

### <a name="error-your-deployment-failedunauthorized"></a>錯誤：您的部署失敗...

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**原因：** 您使用的訂閱是無法訪問客戶嘗試部署的區域所需的功能的類型。 例如，MSDN、免費訂閱或教育訂閱可以顯示此錯誤。

**修復：** 將訂閱類型或區域更改為可以訪問所需功能的區域。

### <a name="error-vmextensionprovisioningerror"></a>錯誤：VM 擴展預配錯誤

![部署的螢幕截圖失敗，終端預配狀態失敗。](media/7aaf15615309c18a984673be73ac969a.png)

**原因 1：** Windows 虛擬桌面環境的暫時性錯誤。

**原因 2：** 連接時瞬態錯誤。

**修復：** 使用 PowerShell 登錄確認 Windows 虛擬桌面環境是否正常。 在[使用 PowerShell 創建主機池](create-host-pools-powershell.md)中手動完成 VM 註冊。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>錯誤：不允許指定管理員使用者名

![部署的螢幕截圖失敗，不允許指定管理員。](media/f2b3d3700e9517463ef88fa41875bac9.png)

原始錯誤示例：

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**原因：** 提供的密碼包含禁止的子字串（管理員、管理員、根）。

**修復：** 更新使用者名或使用不同的使用者。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>錯誤：VM 在處理擴展時報告失敗

![在部署失敗中終端預配狀態完成的資源操作的螢幕截圖。](media/49c4a1836a55d91cd65125cf227f411f.png)

原始錯誤示例：

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**原因：** PowerShell DSC 擴展無法在 VM 上獲取管理員存取權限。

**修復：** 確認使用者名和密碼在虛擬機器上具有管理存取權限，然後再次運行 Azure 資源管理器範本。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>錯誤：部署失敗 – 電源外殼 DSC 配置"第一工作階段主機"完成，錯誤（s）

![使用 PowerShell DSC 配置"第一工作階段主機"完成且錯誤完成的部署螢幕截圖失敗。](media/64870370bcbe1286906f34cf0a8646ab.png)

原始錯誤示例：

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**原因：** PowerShell DSC 擴展無法在 VM 上獲取管理員存取權限。

**修復：** 確認提供的使用者名和密碼在虛擬機器上具有管理存取權限，然後再次運行 Azure 資源管理器範本。

### <a name="error-deploymentfailed--invalidresourcereference"></a>錯誤：部署失敗 = 無效資源引用

原始錯誤示例：

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因：** 資源組名稱的一部分用於範本正在創建的某些資源。 由於與現有資源匹配的名稱，範本可以從其他組中選擇現有資源。

**修復：** 運行 Azure 資源管理器範本以部署工作階段主機 VM 時，使訂閱資源組名稱的前兩個字元唯一。

### <a name="error-deploymentfailed--invalidresourcereference"></a>錯誤：部署失敗 = 無效資源引用

原始錯誤示例：

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**原因：** 此錯誤是因為使用 Azure 資源管理器範本創建的 NIC 的名稱與 VNET 中已有的另一個 NIC 具有相同的名稱。

**修復：** 使用其他主機首碼。

### <a name="error-deploymentfailed--error-downloading"></a>錯誤：部署失敗 = 下載錯誤

原始錯誤示例：

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**原因：** 此錯誤是由於靜態路由、防火牆規則或 NSG 阻止下載綁定到 Azure 資源管理器範本的 ZIP 檔案。

**修復：** 刪除阻塞的靜態路由、防火牆規則或 NSG。 或者，在文字編輯器中打開 Azure 資源管理器範本 json 檔，獲取指向 ZIP 檔案的連結，並將資源下載到允許的位置。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>錯誤：使用者無權查詢管理服務

原始錯誤示例：

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**原因：** 指定的 Windows 虛擬桌面租戶管理員沒有有效的角色指派。

**修復：** 創建 Windows 虛擬桌面租戶的使用者需要登錄到 Windows 虛擬桌面 PowerShell，並分配嘗試的使用者角色指派。 如果您正在運行 GitHub Azure 資源管理器範本參數，請使用 PowerShell 命令按照以下說明操作：

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>錯誤：使用者需要 Azure 多重要素驗證 （MFA）

![由於缺乏多重要素驗證 （MFA），部署的螢幕截圖失敗](media/MFARequiredError.png)

原始錯誤示例：

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**原因：** 指定的 Windows 虛擬桌面租戶管理員需要 Azure 多重要素驗證 （MFA） 才能登錄。

**修復：** 按照教程中的步驟：[使用 PowerShell 創建服務主體和角色指派，](create-service-principal-role-powershell.md)創建服務主體並為 Windows 虛擬桌面租戶分配角色。 在驗證可以使用服務主體登錄到 Windows 虛擬桌面後，請重新運行 Azure 應用商店產品或 GitHub Azure 資源管理器範本，具體取決於您正在使用的方法。 按照以下說明輸入方法的正確參數。

如果運行 Azure 應用商店產品/服務，請提供以下參數的值，以便對 Windows 虛擬桌面進行正確的身份驗證：

- Windows 虛擬桌面租戶 RDS 擁有者：服務主體
- 應用程式 ID：您創建的新服務主體的應用程式標識
- 密碼/確認密碼：您為服務主體生成的密碼金鑰
- Azure AD 租戶 ID：您創建的服務主體的 Azure AD 租戶 ID

如果運行 GitHub Azure 資源管理器範本，請提供以下參數的值，以便對 Windows 虛擬桌面進行正確的身份驗證：

- 租戶管理員使用者主體名稱 （UPN） 或應用程式 ID：您創建的新服務主體的應用程式標識
- 租戶管理員密碼：您為服務主體生成的密碼金鑰
- 服務主體：**真實**
- AadTenantId：您創建的服務主體的 Azure AD 租戶 ID

## <a name="next-steps"></a>後續步驟

- 有關 Windows 虛擬桌面和升級跟蹤的故障排除概述，請參閱[故障排除概述、回饋和支援](troubleshoot-set-up-overview.md)。
- 要在 Windows 虛擬桌面中配置虛擬機器 （VM） 時解決問題，請參閱[工作階段主機虛擬機器配置](troubleshoot-vm-configuration.md)。
- 要解決 Windows 虛擬桌面用戶端連接的問題，請參閱[Windows 虛擬桌面服務連接](troubleshoot-service-connection.md)。
- 要解決遠端桌面用戶端的問題，請參閱[排除遠端桌面用戶端的故障](troubleshoot-client.md)
- 要解決在 Windows 虛擬桌面中使用 PowerShell 時的問題，請參閱[Windows 虛擬桌面電源外殼](troubleshoot-powershell.md)。
- 要瞭解有關該服務的更多資訊，請參閱[Windows 虛擬桌面環境](environment-setup.md)。
- 要完成疑難排解教程，請參閱[教程：解決資源管理器範本部署的疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](../azure-resource-manager/templates/deployment-history.md)。
