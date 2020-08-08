---
title: Windows 虛擬桌面 (傳統) 診斷記錄分析-Azure
description: 如何搭配使用 log analytics 與 Windows 虛擬桌面 (傳統) 診斷功能。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a7a7a6681be78c2777130ca540319eb829d8f9d
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002357"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>針對 Windows 虛擬桌面 (傳統中的診斷功能使用 Log Analytics) 

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../diagnostics-log-analytics.md)。

Windows 虛擬桌面提供診斷功能，可讓系統管理員透過單一介面識別問題。 每當指派 Windows 虛擬桌面角色的人員使用服務時，此功能就會記錄診斷資訊。 每個記錄檔都包含有關活動涉及哪些 Windows 虛擬桌面角色、在會話期間出現的任何錯誤訊息、租使用者資訊，以及使用者資訊。 診斷功能會建立使用者和系統管理動作的活動記錄。 每個活動記錄都落在三個主要類別之下：

- 摘要訂閱活動：當使用者嘗試透過 Microsoft 遠端桌面應用程式連接到其摘要時。
- 連線活動：當使用者嘗試透過 Microsoft 遠端桌面應用程式連線到桌面或 RemoteApp 時。
- 管理活動：當管理員在系統上執行管理作業時，例如建立主機集區、將使用者指派給應用程式群組，以及建立角色指派。

因為診斷角色服務本身是 Windows 虛擬桌面的一部分，所以在診斷結果中不會顯示未觸達 Windows 虛擬桌面的連線。 當使用者遇到網路連線問題時，會發生 Windows 虛擬桌面連接問題。

## <a name="why-you-should-use-log-analytics"></a>為何應該使用 Log Analytics

我們建議您在 Azure 用戶端中使用 Log Analytics 來分析診斷資料，而不會超過單一使用者的疑難排解。 您可以將 VM 效能計數器提取到 Log Analytics 中，有一個工具可以收集部署的資訊。

## <a name="before-you-get-started"></a>開始之前

您必須先[建立工作區](../../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)，才可以使用 Log Analytics 搭配診斷功能。

建立工作區之後，請依照[將 Windows 電腦連線至 Azure 監視器](../../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)中的指示取得下列資訊：

- 工作區識別碼
- 工作區的主要金鑰

您稍後會在安裝過程中需要此資訊。

## <a name="push-diagnostics-data-to-your-workspace"></a>將診斷資料推送至您的工作區

您可以將診斷資料從您的 Windows 虛擬桌面租使用者推送至您工作區的 Log Analytics。 您可以在第一次建立租使用者時立即設定此功能，方法是將工作區連結至您的租使用者，或者您可以稍後使用現有的租使用者進行設定。

若要在設定新租使用者時，將租使用者連結至 Log Analytics 工作區，請執行下列 Cmdlet，以 TenantCreator 使用者帳戶登入 Windows 虛擬桌面：

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

如果您要連結現有的租使用者，而不是新的租使用者，請改為執行此 Cmdlet：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

針對您想要連結到 Log Analytics 的每個租使用者，您必須執行這些 Cmdlet。

>[!NOTE]
>如果您不想要在建立租使用者時連結 Log Analytics 工作區，請改為執行 `New-RdsTenant` Cmdlet。

## <a name="cadence-for-sending-diagnostic-events"></a>傳送診斷事件的步調

完成時，會將診斷事件傳送至 Log Analytics。

## <a name="example-queries"></a>查詢範例

下列查詢範例會顯示診斷功能如何為系統中最常用的活動產生報告：

第一個範例會顯示使用者使用支援的遠端桌面用戶端所起始的連線活動：

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

下一個範例查詢會顯示租使用者上系統管理員的管理活動：

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

## <a name="stop-sending-data-to-log-analytics"></a>停止將資料傳送至 Log Analytics

若要停止將資料從現有的租使用者傳送到 Log Analytics，請執行下列 Cmdlet 並設定空字串：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

您必須針對您想要停止傳送資料的每個租使用者執行此 Cmdlet。

## <a name="next-steps"></a>後續步驟

若要查看診斷功能可為您識別的常見錯誤案例，請參閱[識別並診斷問題](diagnostics-role-service-2019.md#common-error-scenarios)。
