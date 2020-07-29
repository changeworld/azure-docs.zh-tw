---
title: Windows 虛擬桌面（傳統）診斷問題-Azure
description: 如何使用 Windows 虛擬桌面（傳統）診斷功能來診斷問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 257ad5aa11bfaece70f676b452119d7800e2d1e2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285045"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>識別及診斷 Windows 虛擬桌面中的問題（傳統）

>[!IMPORTANT]
>此內容適用于 Windows 虛擬桌面（傳統），不支援 Azure Resource Manager Windows 虛擬桌面物件。 如果您正嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../diagnostics-role-service.md)。

Windows 虛擬桌面提供診斷功能，可讓系統管理員透過單一介面識別問題。 每當使用者與系統互動時，Windows 虛擬桌面角色都會記錄診斷活動。 每個記錄都包含相關資訊，例如交易中涉及的 Windows 虛擬桌面角色、錯誤訊息、租用戶資訊、使用者資訊。 診斷活動是由使用者和系統管理動作建立，可分為三大類：

* 摘要訂用帳戶活動：每當使用者嘗試透過 Microsoft 遠端桌面應用程式連線到其摘要時，就會觸發這些活動。
* 連線活動：每當使用者嘗試透過 Microsoft 遠端桌面應用程式連線到桌面或 RemoteApp 時，就會觸發這些活動。
* 管理活動：每當系統管理員在系統上執行管理作業時，例如建立主機集區、指派使用者至應用程式群組、建立角色指派，就會觸發這些活動。
  
因為診斷角色服務本身是 Windows 虛擬桌面的一部分，所以在診斷結果中不會顯示未觸達 Windows 虛擬桌面的連線。 當使用者遇到網路連線問題時，可能是發生了 Windows 虛擬桌面連線問題。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以用於您的 PowerShell 工作階段 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>以 PowerShell 診斷問題

Windows 虛擬桌面診斷只會使用一個 PowerShell Cmdlet，但包含許多選用參數，以協助縮小和隔離問題。 下列各節列出您可以執行以診斷問題的 Cmdlet。 大部分的篩選都可以一起套用。 以方括弧括住的值 (例如 `<tenantName>`) 應取代為適用於您情況的值。

>[!IMPORTANT]
>診斷功能適用於單一使用者的疑難排解。 所有使用 PowerShell 的查詢都必須包含 *-UserName* 或 *-ActivityID* 參數。 如需監視功能，請使用 Log Analytics。 如需如何將診斷資料傳送至工作區的詳細資訊，請參閱[將 Log Analytics 使用於診斷功能](diagnostics-log-analytics-2019.md)。 

### <a name="filter-diagnostic-activities-by-user"></a>依使用者篩選診斷活動

**-UserName** 參數會傳回由指定使用者起始的診斷活動清單，如下列範例 Cmdlet 所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName** 參數也可以與其他選用篩選參數結合。

### <a name="filter-diagnostic-activities-by-time"></a>依時間篩選診斷活動

您可以使用 **-StartTime** 和 **-EndTime** 參數篩選傳回的診斷活動清單。 **-StartTime** 參數會傳回從特定日期開始的診斷活動清單，如下列範例所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

將 **-StartTime** 參數加入已有 **-EndTime** 參數的 Cmdlet，可指定您想要接收結果的特定時間長度。 下列範例 Cmdlet 會傳回 8 月 1 日到 8 月 10 日之間的診斷活動清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime** 和 **-EndTime** 參數也可以與其他選用篩選參數結合。

### <a name="filter-diagnostic-activities-by-activity-type"></a>依活動類型篩選診斷活動

您也可以使用 **-ActivityType** 參數，依活動類型篩選診斷活動。 下列 Cmdlet 會傳回使用者連線的清單：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

下列 Cmdlet 會傳回系統管理員管理工作的清單：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-RdsDiagnosticActivities** Cmdlet 目前不支援指定「摘要」ActivityType。

### <a name="filter-diagnostic-activities-by-outcome"></a>依結果篩選診斷活動

您可以使用 **-Outcome** 參數，依結果篩選傳回的診斷活動清單。 下列範例 Cmdlet 會傳回成功診斷活動的清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

下列範例 Cmdlet 會傳回失敗診斷活動的清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-Outcome** 參數也可以與其他選用篩選參數結合。

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>依活動識別碼擷取特定診斷活動

**-ActivityId** 參數會傳回特定的診斷活動 (如果有的話)，如下列範例 Cmdlet 所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>依據活動識別碼檢視失敗活動的錯誤訊息

若要檢視失敗活動的錯誤訊息，您必須使用 **-Detailed** 參數執行 Cmdlet。 您可以藉由執行 **Select-Object** Cmdlet 來檢視錯誤清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>取得詳細的診斷活動

**-Detailed** 參數可以為每個傳回的診斷活動提供額外的詳細資料。 每個活動的格式會根據其活動類型而有所不同。 **-Detailed** 參數可以加入任何 **Get-RdsDiagnosticActivities** 查詢，如下列範例所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>常見錯誤案例

錯誤案例分成服務內部以及 Windows 虛擬桌面外部。

* 內部問題：指定租用戶系統管理員無法減輕的案例，而且需要靠支援問題解決。 透過 [Windows 虛擬桌面技術社區](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提供意見反應時，請包含活動識別碼，以及發生問題時的大約時間範圍。
* 外部問題：與系統管理員可減輕的案例相關。 這些是在 Windows 虛擬桌面的外部。

下表列出系統管理員可能遇到的常見錯誤。

>[!NOTE]
>這份清單包含最常見的錯誤，並且會定期更新。 為確保您擁有最新的資訊，請務必至少每個月重看一次本文。

### <a name="external-management-error-codes"></a>外部管理錯誤碼

|數字碼|錯誤碼|建議的解決辦法|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|使用者不是 Azure Active Directory 的成員。 遵循 [Active Directory 管理中心](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)的指示新增他們。|
|3|UnauthorizedAccess|嘗試執行系統管理 PowerShell Cmdlet 的使用者沒有權限可執行此動作或輸入錯誤的使用者名稱。|
|1000|TenantNotFound|您輸入的租用戶名稱不符合任何現有的租用戶。 請檢查租用戶名稱是否有輸入錯誤，然後再試一次。|
|1006|TenantCannotBeRemovedHasSessionHostPools|只要租用戶包含物件，您就無法將它刪除。 請先刪除工作階段主機集區，然後再試一次。|
|2000|HostPoolNotFound|您輸入的主機集區名稱不符合任何現有的主機集區。 請檢查主機集區名稱是否有輸入錯誤，然後再試一次。|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|只要主機集區包含物件，您就無法將它刪除。 請先移除主機集區中的所有應用程式群組。|
|2004|HostPoolCannotBeRemovedHasSessionHosts|刪除工作階段主機集區之前，請先移除所有工作階段主機。|
|5001|SessionHostNotFound|您所查詢的工作階段主機可能已離線。 檢查主機集區的狀態。|
|5008|SessionHostUserSessionsExist |您必須先登出工作階段主機上的所有使用者，才能執行您想要的管理活動。|
|6000|AppGroupNotFound|您輸入的應用程式群組名稱不符合任何現有的應用程式群組。 請檢查應用程式群組名稱是否有輸入錯誤，然後再試一次。|
|6022|RemoteAppNotFound|您輸入的 RemoteApp 名稱不符合任何 Remoteapp。 請檢查 RemoteApp 名稱是否有輸入錯誤，然後再試一次。|
|6010|PublishedItemsExist|您嘗試發佈的資源名稱與已經存在的資源相同。 請變更資源名稱，然後再試一次。|
|7002|NameNotValidWhiteSpace|不可在名稱中使用空白字元。|
|8000|InvalidAuthorizationRoleScope|您輸入的角色名稱不符合任何現有的角色名稱。 請檢查角色名稱是否有輸入錯誤，然後再試一次。 |
|8001|UserNotFound |您輸入的使用者名稱不符合任何現有的使用者名稱。 請檢查名稱是否有輸入錯誤，然後再試一次。|
|8005|UserNotFoundInAAD |您輸入的使用者名稱不符合任何現有的使用者名稱。 請檢查名稱是否有輸入錯誤，然後再試一次。|
|8008|TenantConsentRequired|依照[這裡](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) 的指示為您的租用戶提供同意。|

### <a name="external-connection-error-codes"></a>外部連線錯誤碼

|數字碼|錯誤碼|建議的解決辦法|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|使用者不是 Active Directory 的成員。 遵循 [Active Directory 管理中心](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)的指示新增他們。|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|工作階段主機未正確聯結至 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|連線失敗，因為無法使用工作階段主機。 檢查工作階段主機的健康情況。|
|-2146233088|ConnectionFailedClientDisconnect|如果您經常看到此錯誤，請確定使用者的電腦已連線到網路。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主機使用者嘗試連線的工作階段健康情況不良。 對虛擬機器進行偵錯。|
|-2146233088|ConnectionFailedUserNotAuthorized|使用者沒有權限可存取已發佈的應用程式或桌面。 此錯誤可能會在系統管理員移除已發佈的資源之後出現。 要求使用者重新整理遠端桌面應用程式中的摘要。|
|2|FileNotFound|使用者嘗試存取的應用程式未正確安裝或設定為不正確的路徑。|
|3|InvalidCredentials|使用者輸入的使用者名稱或密碼不符合任何現有的使用者名稱或密碼。 請檢查登入資訊是否有輸入錯誤，然後再試一次。|
|8|ConnectionBroken|用戶端與閘道或伺服器之間的連線已中斷。 除非是意外發生，否則不需要採取任何動作。|
|14|UnexpectedNetworkDisconnect|網路連線已中斷。 要求使用者再次連線。|
|24|ReverseConnectFailed|主機虛擬機器無法直接看到 RD 閘道。 請確定可以解析閘道 IP 位址。|
|1322|ConnectionFailedNoMappingOfSIDinAD|使用者不是 Active Directory 的成員。 遵循 [Active Directory 管理中心](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)的指示新增他們。|

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Windows 虛擬桌面內的角色，請參閱 [Windows 虛擬桌面環境](environment-setup-2019.md)。

若要查看 Windows 虛擬桌面可用的 PowerShell Cmdlet 清單，請參閱 [PowerShell 參考資訊](/powershell/windows-virtual-desktop/overview)。
