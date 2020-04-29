---
title: 瞭解如何審核虛擬機器的內容
description: 瞭解 Azure 原則如何使用來賓設定代理程式來審查虛擬機器內的設定。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 89f7cc3931971d70b441490f77b67ace89434c2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025215"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

除了[審查和修復](../how-to/remediate-resources.md)Azure 資源之外，Azure 原則可以在機器內進行設定。 此驗證會由「來賓設定」延伸模組和用戶端執行。 透過用戶端的延伸模組會驗證下列設定：

- 作業系統的設定
- 應用程式設定或目前狀態
- 環境設定

目前，大部分的 Azure 原則來賓設定原則都只會在機器內進行一次的審查。 它們不會套用設定。 例外狀況是[下面所參考](#applying-configurations-using-guest-configuration)的其中一個內建原則。

## <a name="resource-provider"></a>資源提供者

您必須先註冊資源提供者，才能使用「來賓設定」。 如果透過入口網站來指派來賓設定原則，則會自動註冊資源提供者。 您可以透過[入口網站](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、 [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)或[Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)手動進行註冊。

## <a name="extension-and-client"></a>延伸模組和用戶端

若要在電腦內審核設定，會啟用[虛擬機器擴充](../../../virtual-machines/extensions/overview.md)功能。 延伸模組會下載適用的原則指派及相對應的設定定義。

> [!Important]
> 需要來賓設定延伸模組，才能在 Azure 虛擬機器中執行審核。
> 若要大規模部署擴充功能，請指派下列原則定義：
>   - [部署必要條件，以在 Windows VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
>   - [部署必要條件，以在 Linux VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

### <a name="limits-set-on-the-extension"></a>延伸模組上設定的限制

若要限制延伸不會影響在機器內執行的應用程式，來賓設定不得超過5% 的 CPU。 內建和自訂定義都有這項限制。

### <a name="validation-tools"></a>驗證工具

在機器內部，來賓設定用戶端會使用本機工具來執行 audit。

下表顯示每個支援的作業系統上所使用的本機工具清單：

|作業系統|驗證工具|備忘錄|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 如果 Ruby 和 Python 不在電腦上，則會由來賓設定擴充功能進行安裝。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。 一旦收到來賓指派，就會在15分鐘的間隔內重新檢查該設定的設定。
當審核完成時，會將結果傳送至來賓設定資源提供者。 發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。 此更新會導致 Azure 原則評估 Azure Resource Manager 屬性。 隨選 Azure 原則評估會從來賓設定資源提供者抓取最新的值。 不過，它不會在機器內觸發設定的新審核。

## <a name="supported-client-types"></a>支援的用戶端類型

來賓設定原則包含新的版本。 如果來賓設定代理程式不相容，則會排除 Azure marketplace 中可用的舊版作業系統。 下表顯示 Azure 映射上支援的作業系統清單：

|發行者|Name|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04 和更新版本|
|Credativ|Debian|8和更新版本|
|Microsoft|Windows Server|2012和更新版本|
|Microsoft|Windows 用戶端|Windows 10|
|OpenLogic|CentOS|7.3 和更新版本|
|Red Hat|Red Hat Enterprise Linux|7.4 和更新版本|
|Suse|SLES|12 SP3 和更新版本|

來賓設定原則支援自訂虛擬機器映射，前提是它們是上表中的其中一個作業系統。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

任何版本都不支援 Windows Server Nano Server。

## <a name="guest-configuration-extension-network-requirements"></a>來賓設定擴充功能網路需求

若要與 Azure 中的來賓設定資源提供者通訊，機器需要在埠**443**上對 Azure 資料中心進行輸出存取。 如果 Azure 中的網路不允許輸出流量，請使用[網路安全性群組](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則來設定例外狀況。
[服務](../../../virtual-network/service-tags-overview.md)標籤 "GuestAndHybridManagement" 可用於參照來賓設定服務。

## <a name="azure-managed-identity-requirements"></a>Azure 受控識別需求

將擴充功能新增至虛擬機器的**DeployIfNotExists**原則也會啟用系統指派的受控識別（如果不存在的話）。

> [!WARNING]
> 避免在啟用系統指派的受控識別的原則範圍內，將使用者指派的受控識別啟用至虛擬機器。 使用者指派的身分識別會被取代，而且機器會變成沒有回應。

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

每個由來賓設定的 audit 執行都需要兩個原則定義： **DeployIfNotExists**定義和**AuditIfNotExists**定義。 

**DeployIfNotExists** 原則定義會驗證並修正下列項目：

- 驗證電腦是否已獲指派要評估的設定。 如果目前沒有任何指派，請透過下列方式取得指派並準備機器：
  - 使用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)向電腦進行驗證
  - 安裝最新版的 **Microsoft.GuestConfiguration** 延伸模組
  - 安裝[驗證工具](#validation-tools)和相依性 (如有需要)

如果**DeployIfNotExists**指派不符合規範，則可以使用[補救](../how-to/remediate-resources.md#create-a-remediation-task)工作。

一旦**DeployIfNotExists**指派符合規範， **AuditIfNotExists**原則指派會決定來賓指派是否符合規範。 驗證工具會將結果提供給「來賓設定」用戶端。 用戶端會將結果轉送至「來賓延伸模組」，以便透過「來賓設定」資源提供者提供結果。

「Azure 原則」會使用「來賓設定」資源提供者 **complianceStatus** 屬性在 [合規性]**** 節點中回報合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/get-compliance-data.md)。

> [!NOTE]
> **AuditIfNotExists**原則需要**DeployIfNotExists**原則才會傳回結果。 若沒有**DeployIfNotExists**， **AuditIfNotExists**原則會顯示 "0 of 0" 資源作為狀態。

「來賓設定」的所有內建原則都包含在一個方案中，以聚集要在指派中使用的定義。 在_ \[Linux 和 Windows 機器內部，\]名為 Preview： Audit Password security_的內建計畫包含18個原則。 針對 Windows 有 6 組 **DeployIfNotExists** 和 **AuditIfNotExists**，針對 Linux 則有 3 組。 [原則定義](definition-structure.md#policy-rule)邏輯會驗證只會評估目標作業系統。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>遵循業界基準進行作業系統設定的審核

Azure 原則中的一項計畫可讓您依照「基準」來審查作業系統設定。 [定義] _ \[、\][預覽：不符合 Azure 安全性基準的 Windows vm_ ] 設定包含以 Active Directory 群組原則為基礎的一組規則。

大部分的設定都是以參數的形式提供。 參數可讓您自訂要審核的內容。 使原則符合您的需求，或將原則對應至協力廠商資訊，例如產業法規標準。

某些參數支援整數值範圍。 例如，[密碼最長使用期限] 設定可能會審核有效的群組原則設定。 「1，70」範圍會確認使用者至少每隔70天就需要變更密碼，但不能少於一天。

如果您使用 Azure Resource Manager 部署範本指派原則，請使用參數檔案來管理例外狀況。 將檔案簽入至版本控制系統，例如 Git。 有關檔案變更的批註會提供辨識項，說明為何指派是預期值的例外狀況。

#### <a name="applying-configurations-using-guest-configuration"></a>使用來賓設定套用設定

Azure 原則的最新功能會設定機器內的設定。 定義：在_Windows 機器上設定_時區，以對電腦進行變更。

指派以_Configure_開頭的定義時，您也必須指派定義_部署必要條件，以在 Windows Vm 上啟用來賓設定原則_。 如果您選擇，可以將這些定義結合在計畫中。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>將原則指派給 Azure 外部的機器

適用于來賓設定的稽核原則包括**HybridCompute/機器**資源類型。 針對位於原則指派範圍內的伺服器，上架至[Azure Arc 的](../../../azure-arc/servers/overview.md)任何電腦都會自動包含在內。

### <a name="multiple-assignments"></a>多個指派

來賓設定原則目前只支援針對每部電腦指派相同的來賓指派一次，即使原則指派使用不同的參數也一樣。

## <a name="client-log-files"></a>用戶端記錄檔

「來賓設定」延伸模組會將記錄檔寫入下列位置：

Windows：`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux：`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

其中`<version>` ，是指目前的版本號碼。

### <a name="collecting-logs-remotely"></a>從遠端收集記錄檔

針對來賓設定設定或模組進行疑難排解的第一個步驟，應該是`Test-GuestConfigurationPackage`依照如何[建立 Windows 的自訂來賓設定稽核原則](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)中的步驟來使用 Cmdlet。
如果不成功，則收集用戶端記錄檔有助於診斷問題。

#### <a name="windows"></a>Windows

使用[AZURE VM 執行命令](../../../virtual-machines/windows/run-command.md)從記錄檔中捕獲資訊，下列範例 PowerShell 腳本可能會很有説明。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

使用[AZURE VM 執行命令](../../../virtual-machines/linux/run-command.md)從記錄檔中捕獲資訊，下列範例 Bash 腳本會很有説明。

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>來賓設定範例

來賓設定內建原則範例可在下列位置取得：

- [內建原則定義-來賓設定](../samples/built-in-policies.md#guest-configuration)
- [內建計畫-來賓設定](../samples/built-in-initiatives.md#guest-configuration)
- [Azure 原則 GitHub 存放庫範例](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>後續步驟

- 瞭解如何從「來賓設定」[合規性視圖](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)中查看每個設定的詳細資料
- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 請參閱使用[Azure 管理群組來組織資源](../../management-groups/overview.md)的管理群組。
