---
title: Windows 虛擬桌面 (傳統) 租使用者主機集區建立-Azure
description: 如何在 Windows 虛擬桌面 (傳統) 租使用者環境中設定時，對租使用者和主機集區問題進行疑難排解並加以解決。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 31219fda04095d48b55165f59c27f3dee85485a9
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843633"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>在 Windows 虛擬桌面中建立租使用者和主機集區 (傳統) 

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../troubleshoot-set-up-issues.md)。

本文涵蓋 Windows 虛擬桌面租使用者和相關工作階段主機集區基礎結構的初始設定期間的問題。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>取得 Windows 10 企業版多會話映射

若要使用 Windows 10 企業版多會話映射，請移至 Azure Marketplace， **Get Started**  >  並 [針對虛擬桌面1809版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)選取開始 **Microsoft Windows 10** > 和 Windows 10 企業版。

> [!div class="mx-imgBorder"]
> ![選取虛擬桌面1809版 Windows 10 企業版的螢幕擷取畫面。](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>正在建立 Windows 虛擬桌面租使用者

本節涵蓋建立 Windows 虛擬桌面租使用者時的潛在問題。

### <a name="error-aadsts650052-the-app-needs-access-to-a-service"></a>錯誤： AADSTS650052 應用程式需要存取服務。

原始錯誤的範例：

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**原因：** 同意未授與 Azure Active directory 實例中的 Windows 虛擬桌面。

**修正：** [遵循本指南](https://docs.microsoft.com/azure/virtual-desktop/virtual-desktop-fall-2019/tenant-setup-azure-active-directory#grant-permissions-to-windows-virtual-desktop) 來授與同意。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>錯誤：使用者未經授權，無法查詢管理服務

> [!div class="mx-imgBorder"]
> ![PowerShell 視窗的螢幕擷取畫面，其中使用者未獲授權查詢管理服務。](../media/UserNotAuthorizedNewTenant.png)

原始錯誤的範例：

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

**原因：** 未在其 Azure Active Directory 中將 TenantCreator 角色指派給已登入的使用者。

**修正：** 遵循 [將 TenantCreator 應用程式角色指派給 Azure Active Directory 租使用者中的使用者](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)的指示。 遵循指示之後，您將會有指派給 TenantCreator 角色的使用者。

> [!div class="mx-imgBorder"]
> ![已指派 TenantCreator 角色的螢幕擷取畫面。](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>建立 Windows 虛擬桌面工作階段主機 Vm

您可以用數種方式建立工作階段主機 Vm，但 Windows 虛擬桌面小組僅支援與 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 供應專案相關的 VM 布建問題。 如需詳細資訊，請參閱 [使用 Windows 虛擬桌面的問題-布建主機集區 Azure Marketplace 供應](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)專案。

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Windows 虛擬桌面的問題–布建主機集區 Azure Marketplace 供應專案

[Windows 虛擬桌面-布建主機集區] 範本可從 Azure Marketplace 取得。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>錯誤：使用來自 GitHub 的連結時，會出現「建立免費帳戶」訊息

> [!div class="mx-imgBorder"]
> ![建立免費帳戶的螢幕擷取畫面。](../media/be615904ace9832754f0669de28abd94.png)

**原因1：** 帳戶中沒有使用中的訂用帳戶可用來登入 Azure，或使用的帳戶沒有許可權可查看訂用帳戶。

**修正1：** 使用具有參與者存取權的帳戶登入 (至少) 到要部署工作階段主機 Vm 的訂用帳戶。

**原因2：** 使用的訂用帳戶是 Microsoft 雲端服務提供者 (CSP) 租使用者的一部分。

**修正2：** 移至 GitHub 位置以 **建立和布建新的 Windows 虛擬桌面主機集** 區，並遵循下列指示：

1. 以滑鼠右鍵按一下 [ **部署至 Azure** ]，然後選取 [ **複製連結位址**]。
2. 開啟 [ **記事本** ] 並貼上連結。
3. 在 # 字元之前，插入 CSP 終端客戶租使用者名稱。
4. 在瀏覽器中開啟新的連結，Azure 入口網站將載入範本。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>錯誤：您收到「範本部署無效」錯誤

> [!div class="mx-imgBorder"]
> ![[範本部署 ...] 的螢幕擷取畫面無效」錯誤](../media/troubleshooting-marketplace-validation-error-generic.png)

採取特定動作之前，您必須檢查活動記錄檔，以查看部署驗證失敗的詳細錯誤。

若要查看活動記錄檔中的錯誤：

1. 結束目前的 Azure Marketplace 部署供應專案。
2. 在頂端搜尋列中，搜尋並選取 [ **活動記錄**]。
3. 尋找名為 [ **驗證部署** ] 且狀態為 [ **失敗** ] 的活動，然後選取活動。

   > [!div class="mx-imgBorder"]
   > ![個別 * * 驗證部署 * * 活動的螢幕擷取畫面，但狀態為 * * 失敗 * *](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. 選取 [JSON]，然後向下滾動到畫面底部，直到您看到 [>statusmessage] 欄位為止。

   > [!div class="mx-imgBorder"]
   > ![失敗活動的螢幕擷取畫面，其中包含在 JSON 文字的 >statusmessage 屬性周圍的紅色方塊。](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

如果您的工作範本超過配額限制，您可以執行下列其中一項來修正此問題：

 - 使用您第一次使用的參數執行 Azure Marketplace，但這次使用較少的 Vm 和 VM 核心。
 - 在瀏覽器中開啟您在 [ **>statusmessage** ] 欄位中看到的連結，提交要求以增加您的 Azure 訂用帳戶在指定的 VM SKU 上的配額。

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager 範本和 PowerShell Desired State Configuration (DSC) 錯誤

請遵循下列指示，針對 Azure Resource Manager 範本和 PowerShell DSC 的不成功部署進行疑難排解。

1. 使用 [Azure Resource Manager 的 View 部署作業](../../azure-resource-manager/resource-manager-deployment-operations.md)，檢查部署中的錯誤。
2. 如果部署中沒有任何錯誤，請使用 View 活動記錄檔審核活動記錄中的錯誤， [以對資源進行審核動作](../../azure-resource-manager/resource-group-audit.md)。
3. 一旦識別出錯誤，請使用錯誤訊息和資源來 [對常見的 Azure 部署錯誤進行疑難排解，Azure Resource Manager](../../azure-resource-manager/resource-manager-common-deployment-errors.md) 來解決問題。
4. 刪除在先前部署期間建立的任何資源，然後再次嘗試部署範本。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>錯誤：您的部署失敗 ... \<hostname> /joindomain

> [!div class="mx-imgBorder"]
> ![您的部署失敗螢幕擷取畫面。](../media/e72df4d5c05d390620e07f0d7328d50f.png)

原始錯誤的範例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因1：** 提供用來將 Vm 加入網域的認證不正確。

**修正1：** 如果 Vm 未加入 [工作階段主機 VM](troubleshoot-vm-configuration-2019.md)設定中的網域，請參閱「不正確的認證」錯誤。

**原因2：** 功能變數名稱無法解析。

**修正2：** 請參閱錯誤：[工作階段主機 VM](troubleshoot-vm-configuration-2019.md)設定中的 [功能變數名稱無法解析](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve)。

**原因3：** 您的虛擬網路 (VNET) DNS 設定會設定為 **預設值**。

若要修正此問題，請執行下列動作：

1. 開啟 Azure 入口網站並移至 [ **虛擬網路** ] 索引標籤。
2. 尋找您的 VNET，然後選取 [ **DNS 伺服器**]。
3. [DNS 伺服器] 功能表應該會出現在畫面的右側。 選取該功能表上的 [ **自訂**]。
4. 請確定 [自訂] 底下列出的 DNS 伺服器符合您的網域控制站或 Active Directory 網域。 如果您沒有看到您的 DNS 伺服器，您可以在 [ **新增 dns 伺服器** ] 欄位中輸入其值來新增。

### <a name="error-your-deployment-failedunauthorized"></a>錯誤：您的部署失敗...\未授權

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**原因：** 您所使用的訂用帳戶是一種無法存取客戶嘗試部署之區域中所需功能的類型。 例如，MSDN、免費版或教育版訂閱可能會顯示此錯誤。

**修正：** 將您的訂用帳戶類型或區域變更為可以存取所需功能的帳戶。

### <a name="error-vmextensionprovisioningerror"></a>錯誤：VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![您的部署因終端機布建狀態失敗而失敗的螢幕擷取畫面。](../media/7aaf15615309c18a984673be73ac969a.png)

**原因1：** Windows 虛擬桌面環境發生暫時性錯誤。

**原因2：** 連接發生暫時性錯誤。

**修正：** 使用 PowerShell 登入，確認 Windows 虛擬桌面環境狀況良好。 [使用 PowerShell 在建立主機集](create-host-pools-powershell-2019.md)區中手動完成 VM 註冊。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>錯誤：不允許指定的管理員使用者名稱

> [!div class="mx-imgBorder"]
> ![您的部署失敗的螢幕擷取畫面，其中指定的系統管理員不允許。](../media/f2b3d3700e9517463ef88fa41875bac9.png)

原始錯誤的範例：

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**原因：** 提供的密碼包含禁止的子字串 (admin、administrator、root) 。

**修正：** 更新使用者名稱或使用不同的使用者。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>錯誤：VM 回報在處理延伸模組時失敗

> [!div class="mx-imgBorder"]
> ![在您的部署中已完成終端布建狀態的資源作業螢幕擷取畫面失敗。](../media/49c4a1836a55d91cd65125cf227f411f.png)

原始錯誤的範例：

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

**原因：** PowerShell DSC 擴充功能無法取得 VM 上的系統管理員存取權。

**修正：** 確認使用者名稱和密碼有虛擬機器的系統管理存取權，然後再次執行 Azure Resource Manager 範本。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>錯誤： DeploymentFailed-PowerShell DSC 設定 ' FirstSessionHost ' 已完成，但發生錯誤 (s) 

> [!div class="mx-imgBorder"]
> ![部署失敗的螢幕擷取畫面，其中 PowerShell DSC 設定 ' FirstSessionHost ' 已完成，但發生錯誤 (s) 。](../media/64870370bcbe1286906f34cf0a8646ab.png)

原始錯誤的範例：

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

**原因：** PowerShell DSC 擴充功能無法取得 VM 上的系統管理員存取權。

**修正：** 確認提供的使用者名稱和密碼有虛擬機器的系統管理存取權，然後再次執行 Azure Resource Manager 範本。

### <a name="error-deploymentfailed--invalidresourcereference"></a>錯誤： DeploymentFailed – InvalidResourceReference

原始錯誤的範例：

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

**原因：** 資源組名的一部分用於範本所建立的特定資源。 由於名稱與現有資源相符，因此範本可能會從不同的群組中選取現有的資源。

**修正：** 執行 Azure Resource Manager 範本以部署工作階段主機 Vm 時，請讓訂用帳戶資源組名的前兩個字元是唯一的。

### <a name="error-deploymentfailed--invalidresourcereference"></a>錯誤： DeploymentFailed – InvalidResourceReference

原始錯誤的範例：

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

**原因：** 此錯誤是因為使用 Azure Resource Manager 範本建立的 NIC 與 VNET 中的另一個 NIC 具有相同的名稱。

**修正：** 使用不同的主機前置詞。

### <a name="error-deploymentfailed--error-downloading"></a>錯誤： DeploymentFailed-下載錯誤

原始錯誤的範例：

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

**原因：** 此錯誤是因為靜態路由、防火牆規則或 NSG 封鎖了與 Azure Resource Manager 範本系結之 zip 檔案的下載。

**修正：** 移除封鎖靜態路由、防火牆規則或 NSG。 （選擇性）在文字編輯器中開啟 Azure Resource Manager 範本 json 檔案、取得 zip 檔案的連結，然後將資源下載至允許的位置。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>錯誤：使用者未經授權，無法查詢管理服務

原始錯誤的範例：

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

**原因：** 指定的 Windows 虛擬桌面租使用者管理員沒有有效的角色指派。

**修正：** 建立 Windows 虛擬桌面租使用者的使用者必須登入 Windows 虛擬桌面 PowerShell，並為嘗試的使用者指派角色指派。 如果您正在執行 GitHub Azure Resource Manager 範本參數，請使用 PowerShell 命令來遵循下列指示：

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>錯誤：使用者需要 Azure AD Multi-Factor Authentication (MFA) 

> [!div class="mx-imgBorder"]
> ![因為沒有 Multi-Factor Authentication (MFA，所以部署的螢幕擷取畫面失敗) ](../media/MFARequiredError.png)

原始錯誤的範例：

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**原因：** 指定的 Windows 虛擬桌面租使用者系統管理員需要 Azure AD Multi-Factor Authentication (MFA) 才能登入。

**修正：** 遵循 [教學課程：使用 PowerShell 建立服務主體和角色指派](create-service-principal-role-powershell.md)中的步驟，建立服務主體並為其指派 Windows 虛擬桌面租使用者的角色。 確認您可以使用服務主體登入 Windows 虛擬桌面之後，請視您使用的方法而定，重新執行 Azure Marketplace 供應專案或 GitHub Azure Resource Manager 範本。 請遵循下列指示，為您的方法輸入正確的參數。

如果您正在執行 Azure Marketplace 供應專案，請提供下列參數的值，以適當地向 Windows 虛擬桌面進行驗證：

- Windows 虛擬桌面租使用者 RDS 擁有者：服務主體
- 應用程式識別碼：您所建立新服務主體的應用程式識別
- 密碼/確認密碼：您為服務主體產生的密碼秘密
- Azure AD 租使用者識別碼：您所建立之服務主體的 Azure AD 租使用者識別碼

如果您正在執行 GitHub Azure Resource Manager 範本，請提供下列參數的值，以適當地向 Windows 虛擬桌面進行驗證：

- 租使用者管理使用者主體名稱 (UPN) 或應用程式識別碼：您所建立新服務主體的應用程式識別
- 租使用者管理員密碼：您為服務主體產生的密碼秘密
- IsServicePrincipal： **true**
- AadTenantId：您所建立之服務主體的 Azure AD 租使用者識別碼

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>錯誤：設定虛擬網路時無法使用 vmSubnet

**原因：** 在 WVD Marketplace 範本中，UI 只會顯示一個子網，其中至少有一個 IP 位址可作為範本中指定的 Vm 總數。 子網中的可用 IP 位址的實際數目只需等於部署的新 Vm 數目，但無法由目前的 UI 計算。

**修正：** 您可以指定一個子網，其中至少要有多個 IP 位址可供使用，而不使用 Marketplace UI，您可以在重新 [部署現有的部署](expand-existing-host-pool-2019.md#redeploy-from-azure)或 [使用來自 GITHUB 的基礎 ARM 範本進行部署](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)時，指定 "**existingSubnetName**" 參數中的子網名稱。

## <a name="next-steps"></a>後續步驟

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview-2019.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration-2019.md)。
- 若要對 Windows 虛擬桌面用戶端連線問題進行疑難排解，請參閱 [Windows 虛擬桌面服務連接](troubleshoot-service-connection-2019.md)。
- 若要疑難排解遠端桌面用戶端的問題，請參閱 [遠端桌面用戶端疑難排解](../troubleshoot-client.md)
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell-2019.md)。
- 若要深入瞭解此服務，請參閱 [Windows 虛擬桌面環境](environment-setup-2019.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](../../azure-resource-manager/templates/deployment-history.md)。
