---
title: Windows 虛擬桌面診斷問題 - Azure
description: 如何使用 Windows 虛擬桌面診斷功能來診斷問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254257"
---
# <a name="identify-and-diagnose-issues"></a>識別並診斷問題

Windows 虛擬桌面提供診斷功能，允許管理員通過單個介面識別問題。 每當使用者與系統交互時，Windows 虛擬桌面角色都會記錄診斷活動。 每個日誌都包含相關資訊，如事務中涉及的 Windows 虛擬桌面角色、錯誤訊息、租戶資訊和使用者資訊。 診斷活動由最終使用者和管理操作創建，可以分為三個主要存儲桶：

* 源訂閱活動：最終使用者在嘗試通過 Microsoft 遠端桌面應用程式連接到其源時觸發這些活動。
* 連接活動：最終使用者在嘗試通過 Microsoft 遠端桌面應用程式連接到桌面或遠端應用時觸發這些活動。
* 管理活動：管理員在系統上執行管理操作（如創建主機池、將使用者分配給應用組以及創建角色指派）時觸發這些活動。
  
無法訪問 Windows 虛擬桌面的連接不會顯示在診斷結果中，因為診斷角色服務本身是 Windows 虛擬桌面的一部分。 當最終使用者遇到網路連接問題時，可能會發生 Windows 虛擬桌面連接問題。

要開始使用[，請下載並導入 Windows 虛擬桌面 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以便如果您尚未在 PowerShell 會話中使用。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>診斷 PowerShell 的問題

Windows 虛擬桌面診斷僅使用一個 PowerShell Cmdlet，但包含許多可選參數，以説明縮小和隔離問題。 以下各節列出了可以運行以診斷問題的 Cmdlet。 大多數篩選器可以一起應用。 括弧中列出的值（如`<tenantName>`）應替換為適用于您的情況的值。

>[!IMPORTANT]
>診斷功能用於單使用者故障排除。 使用 PowerShell 的所有查詢都必須包括 *-使用者名*或 *-ActivityID*參數。 要監視功能，請使用日誌分析。 有關如何將診斷資料發送到工作區的詳細資訊，請參閱[使用日誌分析進行診斷功能](diagnostics-log-analytics.md)。 

### <a name="filter-diagnostic-activities-by-user"></a>按使用者篩選診斷活動

**-UserName**參數返回由指定使用者啟動的診斷活動的清單，如下例 Cmdlet 所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-使用者名**參數也可以與其他可選篩選參數組合。

### <a name="filter-diagnostic-activities-by-time"></a>按時間篩選診斷活動

您可以使用 **-Starttime**和 **-EndTime**參數篩選返回的診斷活動清單。 **-StartTime**參數將返回從特定日期開始的診斷活動清單，如以下示例所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

**-EndTime**參數可以添加到帶有 **-StartTime**參數的 Cmdlet 中，以指定要接收結果的特定時間段。 以下示例 Cmdlet 將返回 8 月 1 日至 8 月 10 日期間的診斷活動清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-Starttime**和 **-EndTime**參數也可以與其他可選篩選參數組合使用。

### <a name="filter-diagnostic-activities-by-activity-type"></a>按活動類型篩選診斷活動

您還可以使用 **-ActivityType**參數按活動類型篩選診斷活動。 以下 Cmdlet 將返回最終使用者連接的清單：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

以下 Cmdlet 將返回管理員管理工作的清單：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-Rds 診斷活動**Cmdlet 當前不支援將源指定為活動類型。

### <a name="filter-diagnostic-activities-by-outcome"></a>按結果篩選診斷活動

您可以使用 **-結果**參數按結果篩選返回的診斷活動清單。 以下示例 Cmdlet 將返回成功診斷活動的清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

以下示例 Cmdlet 將返回失敗的診斷活動的清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-結果**參數也可以與其他可選的篩選參數組合。

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>按活動 ID 檢索特定診斷活動

**-ActivityId**參數如果存在，將返回特定的診斷活動，如下例 Cmdlet 所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>按活動 ID 查看失敗活動的錯誤訊息

要查看失敗活動的錯誤訊息，必須使用 **-詳細**參數運行 Cmdlet。 您可以通過運行**選擇物件**Cmdlet 來查看錯誤清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>檢索詳細的診斷活動

**-詳細**參數提供了返回的每個診斷活動的其他詳細資訊。 每個活動的格式因活動類型而異。 **-詳細**參數可以添加到任何**獲取診斷活動**查詢，如以下示例所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>常見錯誤案例

錯誤方案在服務內部和 Windows 虛擬桌面外部分類。

* 內部問題：指定租戶管理員無法緩解且需要作為支援問題解決的方案。 當通過 Windows[虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提供回饋時 ，包括活動 ID 和問題發生的大致時間範圍。
* 外部問題：與系統管理員可以緩解的方案相關。 這些是 Windows 虛擬桌面的外部。

下表列出了管理員可能會遇到的常見錯誤。

>[!NOTE]
>此清單包含最常見的錯誤，並在常規節奏上更新。 為了確保您擁有最新資訊，請確保每月至少查看一次本文。

### <a name="external-management-error-codes"></a>外部管理錯誤代碼

|數位代碼|錯誤碼|建議的解決方案|
|---|---|---|
|3|未經授權的訪問|嘗試運行管理 PowerShell Cmdlet 的使用者沒有這樣做的許可權或鍵入了錯誤的使用者名。|
|1000|租戶未找到|您輸入的租戶名稱與任何現有租戶不匹配。 查看租戶名稱中的拼寫錯誤，然後重試。|
|1006|無法刪除的租戶具有工作階段主機池|不能刪除租戶，只要它包含物件。 請先刪除工作階段主機池，然後重試。|
|2000|主機池未找到|您輸入的主機池名稱與任何現有主機池不匹配。 查看主機池名稱中的拼寫錯誤，然後重試。|
|2005|主機池不可刪除具有應用程式組|只要主機池包含物件，它就不會刪除它。 首先刪除主機池中的所有應用組。|
|2004|主機池不可刪除的工作階段主機|在刪除工作階段主機池之前，請先刪除所有工作階段主機。|
|5001|工作階段主機未找到|您查詢的工作階段主機可能處於離線狀態。 檢查主機池的狀態。|
|5008|工作階段主機使用者會話存在 |在執行預期的管理活動之前，必須登出工作階段主機上的所有使用者。|
|6000|應用組未找到|您輸入的應用組名稱與任何現有應用組不匹配。 查看應用組名稱中的拼寫錯誤，然後重試。|
|6022|遠端應用未找到|您輸入的 RemoteApp 名稱與任何遠端應用不匹配。 查看 RemoteApp 名稱中有拼寫錯誤，然後重試。|
|6010|已發佈專案存在|您嘗試發佈的資源的名稱與已存在的資源的名稱相同。 請更改資源名稱，然後重試。|
|7002|名稱 無效空白|不要在名稱中使用空格。|
|8000|無效授權功能範圍|您輸入的角色名稱與任何現有角色名稱不匹配。 查看拼寫錯誤的角色名稱，然後重試。 |
|8001|UserNotFound |您輸入的使用者名與任何現有使用者名不匹配。 查看拼寫錯誤的名稱，然後重試。|
|8005|使用者不芬蘭 |您輸入的使用者名與任何現有使用者名不匹配。 查看拼寫錯誤的名稱，然後重試。|
|8008|租戶同意要求|按照[此處](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop)的說明為您的租戶提供同意。|

### <a name="external-connection-error-codes"></a>外部連接錯誤代碼

|數位代碼|錯誤碼|建議的解決方案|
|---|---|---|
|-2147467259|連接失敗AdTrusted關係失敗|工作階段主機未正確加入活動目錄。|
|-2146233088|連接失敗 使用者有效會話但 Rdshis 不健康|連接失敗，因為工作階段主機不可用。 檢查工作階段主機的運行狀況。|
|-2146233088|連接失敗用戶端斷開連接|如果經常看到此錯誤，請確保使用者的電腦已連接到網路。|
|-2146233088|連接失敗 無健康Rdsh可用|主機使用者嘗試連接到的會話不正常。 調試虛擬機器。|
|-2146233088|連接失敗使用者未授權|使用者沒有訪問已發佈的應用或桌面的許可權。 管理員刪除已發佈資源後可能會出現錯誤。 要求使用者刷新遠端桌面應用程式中的源。|
|2|FileNotFound|使用者嘗試訪問的應用程式安裝不正確或設置為錯誤的路徑。|
|3|InvalidCredentials|使用者輸入的使用者名或密碼與任何現有使用者名或密碼不匹配。 查看拼寫錯誤憑據，然後重試。|
|8|連接中斷|用戶端與閘道或伺服器之間的連接斷開。 除非意外發生，否則無需執行任何操作。|
|14|意外網路斷開|與網路的連接斷開。 要求使用者再次連接。|
|24|反向連接失敗|主機虛擬機器沒有直接的視線到 RD 閘道。 確保可以解析閘道 IP 位址。|

## <a name="next-steps"></a>後續步驟

要瞭解有關 Windows 虛擬桌面中角色的更多資訊，請參閱[Windows 虛擬桌面環境](environment-setup.md)。

要查看適用于 Windows 虛擬桌面的可用 PowerShell Cmdlet 的清單，請參閱[PowerShell 參考](/powershell/windows-virtual-desktop/overview)。
