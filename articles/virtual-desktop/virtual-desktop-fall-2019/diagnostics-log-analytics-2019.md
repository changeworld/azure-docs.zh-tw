---
title: Windows 虛擬桌面 (傳統) 診斷記錄分析-Azure
description: 如何使用 log analytics 搭配 Windows 虛擬桌面 (傳統) 診斷功能。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 257656a29733972672570648d4317b7847fedff1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997671"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>在 Windows 虛擬桌面 (傳統) 中使用適用于診斷功能的 Log Analytics

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../diagnostics-log-analytics.md)。

Windows 虛擬桌面提供診斷功能，可讓系統管理員透過單一介面識別問題。 當有人指派的 Windows 虛擬桌面角色使用服務時，此功能會記錄診斷資訊。 每個記錄檔都包含活動中涉及哪些 Windows 虛擬桌面角色的相關資訊、會話期間出現的任何錯誤訊息、租使用者資訊，以及使用者資訊。 診斷功能會針對使用者和系統管理動作建立活動記錄。 每個活動記錄都落在三個主要類別之下：

- 摘要訂用帳戶活動：當使用者嘗試透過 Microsoft 遠端桌面的應用程式連接到其摘要時。
- 連線活動：當使用者嘗試透過 Microsoft 遠端桌面應用程式連線到桌面或 RemoteApp 時。
- 管理活動：當系統管理員在系統上執行管理作業時，例如建立主機集區、將使用者指派給應用程式群組，以及建立角色指派。

因為診斷角色服務本身是 Windows 虛擬桌面的一部分，所以在診斷結果中不會顯示未觸達 Windows 虛擬桌面的連線。 當使用者遇到網路連線問題時，可能會發生 Windows 虛擬桌面連線問題。

## <a name="why-you-should-use-log-analytics"></a>為何應該使用 Log Analytics

建議您使用 Log Analytics 來分析 Azure 用戶端中超過單一使用者疑難排解的診斷資料。 您可以將 VM 效能計數器提取至 Log Analytics，您可以使用一個工具來收集部署的資訊。

## <a name="before-you-get-started"></a>開始之前

您必須先 [建立工作區](../../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)，才能使用 Log Analytics 搭配診斷功能。

建立工作區之後，請依照 [將 Windows 電腦連線到 Azure 監視器](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) 中的指示取得下列資訊：

- 工作區識別碼
- 您工作區的主要金鑰

您稍後會在安裝程式中需要此資訊。

## <a name="push-diagnostics-data-to-your-workspace"></a>將診斷資料推送至您的工作區

您可以將診斷資料從您的 Windows 虛擬桌面租使用者推送至適用于您工作區的 Log Analytics。 您可以在第一次建立租使用者時立即設定這項功能，方法是將您的工作區連結至您的租使用者，也可以稍後使用現有的租使用者進行設定。

若要在設定新的租使用者時，將您的租使用者連結至 Log Analytics 工作區，請執行下列 Cmdlet 以使用您的 TenantCreator 使用者帳戶登入 Windows 虛擬桌面：

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

如果您要連結現有的租使用者，而不是新的租使用者，請改為執行下列 Cmdlet：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

您必須針對想要連結至 Log Analytics 的每個租使用者執行這些 Cmdlet。

>[!NOTE]
>如果您不想要在建立租使用者時連結 Log Analytics 工作區，請改為執行 `New-RdsTenant` Cmdlet。

## <a name="cadence-for-sending-diagnostic-events"></a>傳送診斷事件的步調

診斷事件會在完成時傳送至 Log Analytics。

## <a name="example-queries"></a>查詢範例

下列查詢範例會示範診斷功能如何針對您系統中最頻繁的活動產生報告：

第一個範例會顯示使用者使用支援的遠端桌面用戶端所起始的連接活動：

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

下一個範例查詢會顯示租使用者的系統管理員管理活動：

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

若要停止將資料從現有的租使用者傳送至 Log Analytics，請執行下列 Cmdlet 並設定空字串：

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

您必須針對您想要停止傳送資料的每個租使用者執行此 Cmdlet。

## <a name="next-steps"></a>後續步驟

若要檢查診斷功能可為您識別的常見錯誤案例，請參閱 [識別及診斷問題](diagnostics-role-service-2019.md#common-error-scenarios)。
