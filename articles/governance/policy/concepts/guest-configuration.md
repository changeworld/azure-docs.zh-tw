---
title: 學習稽核虛擬機器的內容
description: 瞭解 Azure 策略如何使用來賓配置代理審核虛擬機器內的設置。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025215"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

除了審核和[修復](../how-to/remediate-resources.md)Azure 資源之外,Azure 策略還可以審核計算機內的設置。 此驗證會由「來賓設定」延伸模組和用戶端執行。 透過用戶端的延伸模組會驗證下列設定：

- 作業系統的設定
- 應用程式設定或目前狀態
- 環境設定

此時,大多數 Azure 策略來賓配置策略僅審核計算機內的設置。 它們不應用配置。 例外情況是[下面引用](#applying-configurations-using-guest-configuration)的一個內置策略。

## <a name="resource-provider"></a>資源提供者

您必須先註冊資源提供者，才能使用「來賓設定」。 如果通過門戶分配來賓配置策略,則會自動註冊資源提供程式。 您可以通過[門戶](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)Azure [PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)或[Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)手動註冊。

## <a name="extension-and-client"></a>延伸模組和用戶端

要稽核電腦內的設定,將啟用[虛擬機器延伸](../../../virtual-machines/extensions/overview.md)。 延伸模組會下載適用的原則指派及相對應的設定定義。

> [!Important]
> 在 Azure 虛擬機器中執行審核需要來賓配置擴展。
> 要大規模部署延伸,請配置以下策略定義:
>   - [部署必要條件，以在 Windows VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [部署必要條件，以在 Linux VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>在延伸上設定的限制

為了限制擴展影響在計算機內運行的應用程式,不允許來賓配置超過 CPU 的 5%。 對於內置定義和自定義定義,都存在此限制。

### <a name="validation-tools"></a>驗證工具

在計算機內部,來賓配置用戶端使用本地工具運行審核。

下表顯示每個支援的作業系統上所使用的本機工具清單：

|作業系統|驗證工具|注意|
|-|-|-|
|Windows|[Windows 電源外殼所需狀態配置](/powershell/scripting/dsc/overview/overview)v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 如果 Ruby 和 Python 不在電腦上,則它們由來賓配置擴展安裝。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。 收到來賓分配后,該配置的設置將每隔 15 分鐘重新檢查。
審核完成後,結果將發送給來賓配置資源提供程式。 發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。 此更新使 Azure 策略評估 Azure 資源管理員屬性。 按需 Azure 策略評估從來賓配置資源提供程式檢索最新值。 但是,它不會觸發對計算機內配置進行新的審核。

## <a name="supported-client-types"></a>支援的用戶端類型

來賓配置策略包括新版本。 如果來賓配置代理不相容,則排除 Azure 應用商店中可用的舊版本的作業系統。 下表顯示 Azure 映像上受支援的作業系統的清單:

|發行者|名稱|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04 及更高版本|
|Credativ|Debian|8 及更高版本|
|Microsoft|Windows Server|2012年及以後|
|Microsoft|Windows 用戶端|Windows 10|
|OpenLogic|CentOS|7.3 及更高版本|
|Red Hat|Red Hat Enterprise Linux|7.4 及更高版本|
|Suse|SLES|12 SP3 及更高版本|

來賓配置策略支援自定義虛擬機映射,只要它們是上表中的操作系統之一。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

任何版本都不支援 Windows 伺服器 Nano 伺服器。

## <a name="guest-configuration-extension-network-requirements"></a>來賓設定延伸網路要求

要與 Azure 中的來賓配置資源提供程式通訊,電腦需要對埠**443**上的 Azure 資料中心進行出站訪問。 如果 Azure 中的網路不允許出站流量,請使用[網路安全組](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則配置異常。
[服務標籤](../../../virtual-network/service-tags-overview.md)「來賓和混合管理」可用於引用來賓配置服務。

## <a name="azure-managed-identity-requirements"></a>Azure 託管識別要求

將擴展添加到虛擬機器的**DeployIf NotExists**策略還啟用系統分配的託管標識(如果不存在)。

> [!WARNING]
> 避免在啟用系統分配託管標識的策略範圍內為虛擬機器啟用使用者分配的託管標識。 使用者分配的標識將被替換,並且計算機可能會無回應。

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

來賓配置運行的每個審核都需要兩個策略定義:**部署IfNotExists**定義和**AuditIfNot存在**定義。 

**DeployIfNotExists** 原則定義會驗證並修正下列項目：

- 驗證已為計算機分配了要評估的配置。 如果當前不存在分配,請取得分配,並通過以下條件準備計算機:
  - 使用[託管識別](../../../active-directory/managed-identities-azure-resources/overview.md)對電腦進行認證
  - 安裝最新版的 **Microsoft.GuestConfiguration** 延伸模組
  - 安裝[驗證工具](#validation-tools)和相依性 (如有需要)

如果**DeployIfNotExists**分配不符合要求,則可以使用[修正工作](../how-to/remediate-resources.md#create-a-remediation-task)。

一旦**部署非存在分配**符合要求,**審核非存在**策略分配將確定來賓分配是合規還是不合規。 驗證工具會將結果提供給「來賓設定」用戶端。 用戶端會將結果轉送至「來賓延伸模組」，以便透過「來賓設定」資源提供者提供結果。

「Azure 原則」會使用「來賓設定」資源提供者 **complianceStatus** 屬性在 [合規性]**** 節點中回報合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/get-compliance-data.md)。

> [!NOTE]
> **"已部署不存在'** 策略是 **"審核不存在"** 策略返回結果所必需的。 如果沒有**部署"不存在"****審核不存在**策略將顯示" 0 的 0" 資源作為狀態。

「來賓設定」的所有內建原則都包含在一個方案中，以聚集要在指派中使用的定義。 名為_\[「預\]覽 :在 Linux 和 Windows 計算機中審核密碼安全性_」的內建計畫包含 18 個策略。 針對 Windows 有 6 組 **DeployIfNotExists** 和 **AuditIfNotExists**，針對 Linux 則有 3 組。 [策略定義](definition-structure.md#policy-rule)邏輯驗證是否僅評估目標操作系統。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>依產業基線稽核作業系統設定

Azure 策略中的一個計劃提供了按照"基線"審核操作系統設置的功能。 定義:_\[預\]覽 :審核與 Azure 安全基線設定不匹配_的 Windows VM 包括一組基於活動目錄組策略的規則。

大多數設置都可以作為參數提供。 參數允許您自定義審核的內容。 使政策符合您的要求,或將政策映射到第三方資訊(如行業監管標準)。

某些參數支援整數值範圍。 例如,"最大密碼期限"設置可以審核有效的組策略設置。 "1,70"範圍將確認用戶必須至少每 70 天更改一次密碼,但不少於一天。

如果使用 Azure 資源管理器部署範本分配策略,請使用參數檔來管理異常。 將檔簽入版本控制系統(如 Git)。 有關檔更改的註釋提供了為什麼分配是預期值的例外的證據。

#### <a name="applying-configurations-using-guest-configuration"></a>使用來賓設定應用設定

Azure 策略的最新功能配置了計算機內的設置。 定義 通過配置時區來_配置 Windows 計算機上的時區_進行更改。

配置以_設定_開頭的定義時,還必須分配定義 _「部署先決條件」,以便在 Windows VM 上啟用來賓設定策略_。 如果願意,可以將這些定義合併到一個計劃中。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>將策略配置給 Azure 以外的電腦

可用於來賓配置的審核策略包括**Microsoft.混合計算/計算機**資源類型。 自動包含在策略分配範圍內的伺服器 Azure [Arc](../../../azure-arc/servers/overview.md)上的任何電腦。

### <a name="multiple-assignments"></a>多個配置

來賓配置策略當前僅支援每台計算機分配同一來賓分配一次,即使策略分配使用不同的參數也是如此。

## <a name="client-log-files"></a>用戶端記錄檔

以賓設定延伸將紀錄檔寫入以下位置:

Windows：`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux：`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

其中`<version>`引用當前版本號。

### <a name="collecting-logs-remotely"></a>遠端收集紀錄

排除來賓配置或模組的故障排除的第一步應該是使用`Test-GuestConfigurationPackage`cmdlet,按照如何為 Windows[創建自定義來賓配置審核策略](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)的步驟。
如果沒有成功,收集用戶端日誌可以幫助診斷問題。

#### <a name="windows"></a>Windows

使用 Azure VM[執行命令](../../../virtual-machines/windows/run-command.md)從日誌檔中捕獲資訊,以下示例 PowerShell 腳本可能很有用。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

使用 Azure VM[執行命令](../../../virtual-machines/linux/run-command.md)從日誌檔中捕獲資訊,下面的示例 Bash 腳本可能很有用。

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>來賓設定範例

以主要設定的原則範例在以下位置可用:

- [內建策略定義 ─ 來賓設定](../samples/built-in-policies.md#guest-configuration)
- [內建計劃 ─ 訪客設定](../samples/built-in-initiatives.md#guest-configuration)
- [Azure 政策範例 GitHub 儲存函式庫](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>後續步驟

- 瞭解如何從[來賓配置合規性檢視](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)查看每個設置的詳細資訊
- 查看[Azure 策略範例](../samples/index.md)中的範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 瞭解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
