---
title: 監視本地 Azure AD 密碼保護
description: 瞭解如何監視和查看本地活動目錄域服務環境的 Azure AD 密碼保護日誌
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb533d5565009fb22d686e4082c9b4bfaae6dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671648"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>監視和查看本地 Azure AD 密碼保護環境的日誌

部署 Azure AD 密碼保護之後，監視和報告即為必要工作。 本文將深入探討以協助您了解各種監視技術，包括每個服務要將資訊記錄於何處，以及如何報告 Azure AD 密碼保護的使用情形。

監視和報告由事件日誌消息或運行 PowerShell Cmdlet 來完成。 DC 代理和代理服務都記錄事件日誌消息。 下面介紹的所有 PowerShell Cmdlet 僅在代理伺服器上可用（請參閱 AzureAD密碼保護 PowerShell 模組）。 DC 代理軟體不安裝 PowerShell 模組。

## <a name="dc-agent-event-logging"></a>DC 代理程式事件記錄

在每個網域控制站上，DC 代理程式服務軟體會將每個個別密碼驗證作業的結果 (及其他狀態) 寫入至本機事件記錄檔：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

DC 代理程式的系統管理記錄是有關軟體如何運作之資訊的主要來源。

請注意，追蹤記錄檔預設會關閉。

由各種 DC 代理程式元件所記錄的事件會介於下列範圍內：

|元件 |事件識別碼範圍|
| --- | --- |
|DC 代理程式密碼篩選 dll| 10000-19999|
|DC 應用程式服務裝載處理序| 20000-29999|
|DC 代理程式服務原則驗證邏輯| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>DC 代理程式系統管理事件記錄檔

### <a name="password-validation-outcome-events"></a>密碼驗證結果事件

在每個網域控制站上，DC 代理程式服務軟體會將每個個別密碼驗證的結果寫入至 DC 代理程式系統管理事件記錄檔。

就成功的密碼驗證作業而言，通常會有一個從 DC 代理程式密碼篩選 dll 記錄的事件。 如果密碼驗證作業失敗，則通常會記錄兩個事件，一個來自 DC 代理程式服務，另一個來自 DC 代理程式密碼篩選 dll。

系統會根據下列因素，記錄會擷取這些情況的離散事件：

* 指定的密碼是否設定或變更。
* 指定密碼的驗證是通過還是失敗。
* 驗證是否會因為 Microsoft 全域原則、組織原則或組合有所衝突而失敗。
* 目前的密碼原則目前是否開啟僅限稽核模式。

主要的密碼驗證相關事件如下：

|   |密碼變更 |密碼設定|
| --- | :---: | :---: |
|通過 |10014 |10015|
|失敗 (由於客戶密碼原則)| 10016、30002| 10017、30003|
|失敗 (由於 Microsoft 密碼原則)| 10016、30004| 10017、30005|
|失敗 (由於 Microsoft 和客戶密碼原則的組合)| 10016、30026| 10017、30027|
|僅限稽核通過 (將不會通過客戶密碼原則)| 10024、30008| 10025、30007|
|僅限稽核通過 (將不會通過 Microsoft 密碼原則)| 10024、30010| 10025、30009|
|僅限稽核通過 (將不會通過 Microsoft 和客戶密碼原則的組合)| 10024、30028| 10025、30029|

上表中參考「原則組合」的案例是指下列情況：發現使用者的密碼至少包含一個同時來自 Microsoft 禁用密碼清單和客戶禁用密碼清單的權杖。

一起記錄一對事件時，這兩個事件會因為具有同一個 CorrelationId 而明確地產生關聯。

### <a name="password-validation-summary-reporting-via-powershell"></a>透過 PowerShell 報告的密碼驗證摘要

`Get-AzureADPasswordProtectionSummaryReport` Cmdlet 可能會用來產生密碼驗證活動的摘要檢視。 此 Cmdlet 的範例輸出如下：

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

此 Cmdlet 的報告範圍可使用 –Forest、-Domain 或 –DomainController 參數其中之一來加以影響。 未指定參數意指 –Forest。

`Get-AzureADPasswordProtectionSummaryReport` Cmdlet 的運作方式是查詢 DC 代理程式系統管理事件記錄檔，然後計算對應至每個顯示之結果類別的事件總數。 下表包含每個結果與其相應的事件 ID 之間的映射：

|Get-AzureADPasswordProtectionSummaryReport 屬性 |相應的事件 ID|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

請注意，`Get-AzureADPasswordProtectionSummaryReport` Cmdlet 隨附於 PowerShell 指令碼表單，而且必要時，可於下列位置直接參考：

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> 此 Cmdlet 的運作方式是針對每個網域控制站開啟 PowerShell 工作階段。 每個網域控制站上都必須啟用 PowerShell 遠端供作階段支援，且用戶端必須要有足夠權限，才能成功。 如需 PowerShell 遠端工作階段需求的詳細資訊，請在 Powershell 視窗中，執行 'Get-help about_Remote_Troubleshooting'。

> [!NOTE]
> 從遠端查詢每個 DC 代理程式服務的系統管理事件記錄即可讓此 Cmdlet 運作。 如果事件記錄包含大量事件，此 Cmdlet 可能需要很長的時間才能完成。 此外，透過網路大量查詢大型資料集可能會影響網域控制站的效能。 因此，在生產環境中請小心使用這個 Cmdlet。

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>事件識別碼 10014 (成功密碼變更) 的範例事件記錄檔訊息

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>事件識別碼 10017 和 30003 (失敗密碼設定) 的範例事件記錄訊息

10017：

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003：

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>事件識別碼 30001 (因為沒有可用原則而接受的密碼) 的範例事件記錄訊息

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>事件識別碼 30006 (強制執行的新原則) 的範例事件記錄訊息

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>事件識別碼 30019 (Azure AD 密碼保護已停用) 的範例事件記錄檔訊息

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>DC 代理程式運作記錄

DC 代理程式服務也會將與運作有關的事件記錄至以下記錄：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>DC 代理程式追蹤記錄

DC 代理程式服務也可以將偵錯層級追蹤事件的詳細資訊記錄至以下記錄：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

依預設會停用追蹤記錄。

> [!WARNING]
> 啟用時，追蹤記錄會接收大量事件，而可能會影響網域控制站的效能。 因此，只有在問題需要更深入的調查時才應啟用此增強型記錄，且應盡可能縮短啟用時間。

## <a name="dc-agent-text-logging"></a>DC 代理程式文字記錄

DC 代理程式服務可以透過設定以下登錄值，設定為寫入至文字記錄檔：

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

依預設會停用文字記錄。 變更此值時需重新啟動 DC 代理程式服務，此變更才會生效。 啟用 DC 代理程式服務時，將會寫入位於以下位置的記錄檔：

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> 文字記錄會收到與可記錄至追蹤記錄的相同偵錯層級項目，但其格式通常是較容易檢閱和分析的格式。

> [!WARNING]
> 啟用時，此記錄會接收大量事件，而可能會影響網域控制站的效能。 因此，只有在問題需要更深入的調查時才應啟用此增強型記錄，且應盡可能縮短啟用時間。

## <a name="dc-agent-performance-monitoring"></a>DC 代理程式效能監視

DC 代理程式服務軟體會安裝名為 **Azure AD 密碼保護**的效能計數器物件。 以下是目前可用的效能計數器：

|效能計數器名稱 | 描述|
| --- | --- |
|已處理的密碼 |此計數器會顯示自上次重新啟動後已處理 (已接受或拒絕) 的密碼總數。|
|已接受的密碼 |此計數器會顯示自上次重新啟動後已接受的密碼總數。|
|已拒絕的密碼 |此計數器會顯示自上次重新啟動後已拒絕的密碼總數。|
|進行中的密碼篩選要求 |此計數器會顯示目前正在進行中的密碼篩選要求數目。|
|尖峰密碼篩選要求 |此計數器會顯示自上次重新啟動後的並行密碼篩選要求的尖峰數目。|
|密碼篩選要求錯誤 |此計數器會顯示自上次重新啟動後因錯誤而失敗的密碼篩選要求總數。 當 Azure AD 密碼保護 DC 代理程式服務未執行時，就可能發生錯誤。|
|密碼篩選要求/秒 |此計數器會顯示密碼的處理速率。|
|密碼篩選要求處理時間 |此計數器會顯示處理密碼篩選要求所需的平均時間。|
|尖峰密碼篩選要求處理時間 |此計數器會顯示自上次重新啟動後的尖峰密碼篩選要求處理時間。|
|因稽核模式而接受的密碼 |此計數器會顯示自上次重新啟動後，在正常情況下會遭到拒絕、但因密碼原則設定為稽核模式而被接受的密碼總數。|

## <a name="dc-agent-discovery"></a>DC 代理程式探索

`Get-AzureADPasswordProtectionDCAgent` Cmdlet 可用來顯示與執行於網域或樹系中的各種 DC 代理程式有關的基本資訊。 您可以從執行中的 DC 代理程式服務所註冊的 serviceConnectionPoint 物件中擷取這項資訊。

此 Cmdlet 的範例輸出如下：

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

每個 DC 代理程式服務大約每小時會更新一次各種屬性。 資料仍會受限於 Active Directory 複寫延遲。

此 Cmdlet 的查詢範圍可使用 –Forest 或 –Domain 參數來變更。

如果 HeartbeatUTC 值過時，這可能是一個徵兆：該網域控制站上的 Azure AD 密碼保護 DC 代理程式並未執行或已解除安裝，或者該機器已降級且不再是網域控制站。

如果密碼原則DateUTC值過時，這可能是該電腦上的 Azure AD 密碼保護 DC 代理無法正常工作的症狀。

## <a name="dc-agent-newer-version-available"></a>DC 代理較新版本可用

DC 代理服務在檢測到 DC 代理軟體的較新版本可用後，將 30034 警告事件記錄到動作記錄，例如：

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

上述事件未指定較新版本的版本。 您應該轉到事件消息中的連結以獲取有關該資訊。

> [!NOTE]
> 儘管上述事件消息中引用了"自動升級"，但 DC 代理軟體當前不支援此功能。

## <a name="proxy-service-event-logging"></a>Proxy 服務事件記錄

Proxy 服務會發出最小集合的事件至下列事件記錄：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

請注意，追蹤記錄檔預設會關閉。

> [!WARNING]
> 啟用時，追蹤記錄會接收大量事件，而可能會影響 Proxy 主機的效能。 因此，只有在問題需要更深入的調查時才應啟用此記錄，且應盡可能縮短啟用時間。

事件會由各種 Proxy 元件使用下列範圍進行記錄：

|元件 |事件識別碼範圍|
| --- | --- |
|Proxy 服務裝載處理序| 10000-19999|
|Proxy 服務核心商務邏輯| 20000-29999|
|PowerShell Cmdlet| 30000-39999|

## <a name="proxy-service-text-logging"></a>Proxy 服務文字記錄

Proxy 服務可以透過設定以下登錄值，設定為寫入至文字記錄檔：

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD 值)

依預設會停用文字記錄。 變更此值時需重新啟動 Proxy 服務，此變更才會生效。 啟用 Proxy 服務時，將會寫入位於以下位置的記錄檔：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> 文字記錄會收到與可記錄至追蹤記錄的相同偵錯層級項目，但其格式通常是較容易檢閱和分析的格式。

> [!WARNING]
> 啟用時，此記錄會接收大量事件，而可能會影響機器的效能。 因此，只有在問題需要更深入的調查時才應啟用此增強型記錄，且應盡可能縮短啟用時間。

## <a name="powershell-cmdlet-logging"></a>PowerShell Cmdlet 記錄

導致狀態變更的 PowerShell Cmdlet (例如 Register-AzureADPasswordProtectionProxy) 通常會將結果事件記錄到作業記錄。

此外，大多數 Azure AD 密碼保護 PowerShell Cmdlet 將寫入位於以下的文本日誌：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

如果發生 Cmdlet 錯誤，且原因和/或解決方式不明顯，可能也會參考這些文字記錄。

## <a name="proxy-discovery"></a>Proxy 探索

`Get-AzureADPasswordProtectionProxy` Cmdlet 可能會用來顯示有關在網域或樹系中執行之各種 Azure AD 密碼保護 Proxy 服務的基本資訊。 您可以從執行中 Proxy 服務所註冊的 serviceConnectionPoint 物件中擷取此資訊。

此 Cmdlet 的範例輸出如下：

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

每個 Proxy 服務大約每小時都會更新各種屬性。 資料仍會受限於 Active Directory 複寫延遲。

此 Cmdlet 的查詢範圍可使用 –Forest 或 –Domain 參數來變更。

如果 HeartbeatUTC 值過時，這可能是一個徵兆：該機器上的 Azure AD 密碼保護 Proxy 並未執行或已解除安裝。

## <a name="proxy-agent-newer-version-available"></a>代理更新版本可用

代理服務在檢測到代理軟體的較新版本可用後，將 20002 警告事件記錄到動作記錄，例如：

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

上述事件未指定較新版本的版本。 您應該轉到事件消息中的連結以獲取有關該資訊。

即使啟用了自動升級配置代理，也會發出此事件。

## <a name="next-steps"></a>後續步驟

[對 Azure AD 密碼保護進行疑難排解](howto-password-ban-bad-on-premises-troubleshoot.md)

如需關於全域和自訂禁用密碼清單的詳細資訊，請參閱[禁用不當密碼](concept-password-ban-bad.md)一文
