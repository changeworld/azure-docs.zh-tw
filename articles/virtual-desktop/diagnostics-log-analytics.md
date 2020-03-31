---
title: Windows 虛擬桌面診斷日誌分析 - Azure
description: 如何使用日誌分析與 Windows 虛擬桌面診斷功能。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127957"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>使用日誌分析進行診斷功能

Windows 虛擬桌面提供診斷功能，允許管理員通過單個介面識別問題。 每當分配 Windows 虛擬桌面角色的人員使用該服務時，此功能都會記錄診斷資訊。 每個日誌包含有關活動中涉及的 Windows 虛擬桌面角色的資訊、會話期間出現的任何錯誤訊息、租戶資訊和使用者資訊。 診斷功能為使用者和管理操作創建活動日誌。 每個活動日誌都屬於三個主要類別： 

- 源訂閱活動：當使用者嘗試通過 Microsoft 遠端桌面應用程式連接到其源時。
- 連接活動：當使用者嘗試通過 Microsoft 遠端桌面應用程式連接到桌面或遠端應用時。
- 管理活動：當管理員對系統執行管理操作時，例如創建主機池、將使用者分配給應用組以及創建角色指派。

無法訪問 Windows 虛擬桌面的連接不會顯示在診斷結果中，因為診斷角色服務本身是 Windows 虛擬桌面的一部分。 當使用者遇到網路連接問題時，可能會出現 Windows 虛擬桌面連接問題。

## <a name="why-you-should-use-log-analytics"></a>為什麼應該使用日誌分析

我們建議您使用日誌分析來分析 Azure 用戶端中超出單使用者故障排除的診斷資料。 您可以將 VM 效能計數器拉入日誌分析中，因此有一個工具用於收集部署資訊。

## <a name="before-you-get-started"></a>開始之前

在將日誌分析與診斷功能配合使用之前，您需要[創建一個工作區](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)。

創建工作區後，請按照[將 Windows 電腦連接到 Azure 監視器](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)中的說明來獲取以下資訊： 

- 工作區 ID
- 工作區的主鍵

稍後在設置過程中需要此資訊。

## <a name="push-diagnostics-data-to-your-workspace"></a>將診斷資料推送到工作區 

您可以將 Windows 虛擬桌面租戶的診斷資料推送到工作區的日誌分析中。 首次創建租戶時，可以通過將工作區連結到租戶來設置此功能，也可以稍後使用現有租戶進行設置。

要在設置新租戶時將租戶連結到日誌分析工作區，請運行以下 Cmdlet 以使用租戶建立者使用者帳戶登錄到 Windows 虛擬桌面： 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

如果要連結現有租戶而不是新租戶，請改為運行此 Cmdlet： 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

您需要為每個要連結到日誌分析的租戶運行這些 Cmdlet。 

>[!NOTE]
>如果不想在創建租戶時連結日誌分析工作區，請改為運行`New-RdsTenant`Cmdlet。 

## <a name="cadence-for-sending-diagnostic-events"></a>發送診斷事件的節奏

診斷事件完成後會發送到日誌分析。  

## <a name="example-queries"></a>查詢範例

以下依例查詢顯示診斷功能如何為系統中最頻繁的活動生成報告：

第一個示例顯示由具有受支援的遠端桌面用戶端的使用者啟動的連接活動：

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

下一個依例查詢顯示管理員對租戶的管理活動：

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>停止將資料發送到日誌分析 

要停止將資料從現有租戶發送到 Log Analytics，請運行以下 Cmdlet 並設置空字串：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

您需要為要停止從中發送資料的每個租戶運行此 Cmdlet。 

## <a name="next-steps"></a>後續步驟 

要查看診斷功能可以為您識別的常見錯誤方案，請參閱[識別和診斷問題](diagnostics-role-service.md#common-error-scenarios)。
