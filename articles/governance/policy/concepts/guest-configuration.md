---
title: 了解如何稽核虛擬機器的內容
description: 了解 Azure 原則如何使用「來賓設定」代理程式來稽核虛擬機器內的設定。
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: 951960793ebda50fdb87d266c4dc8561f2fcd70f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88756685"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

Azure 原則可以針對在 Azure 中執行的機器和 [Arc 連接的機器](../../../azure-arc/servers/overview.md)，在機器內進行設定。 此驗證會由「來賓設定」延伸模組和用戶端執行。 透過用戶端的延伸模組會驗證下列設定：

- 作業系統的設定
- 應用程式設定或目前狀態
- 環境設定

Azure 原則的「來賓設定」原則目前只會稽核機器內的設定。
其不會套用設定。 例外狀況是[下面所參照](#applying-configurations-using-guest-configuration)的內建原則。

## <a name="enable-guest-configuration"></a>啟用來賓設定

若要在您的環境中審核機器的狀態，包括 Azure 中的機器和 Arc 連接的機器，請參閱下列詳細資料。

## <a name="resource-provider"></a>資源提供者

您必須先註冊資源提供者，才能使用「來賓設定」。 如果透過入口網站來指派「來賓設定」原則，則會自動註冊資源提供者。 您可以透過[入口網站](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 或 [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)手動註冊。

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure 虛擬機器的部署需求

若要在機器內審核設定，則會啟用 [虛擬機器擴充](../../../virtual-machines/extensions/overview.md) 功能，且電腦必須具有系統管理的身分識別。 延伸模組會下載適用的原則指派及相對應的設定定義。 當電腦讀取和寫入至來賓設定服務時，會使用身分識別來進行驗證。 因為已連接 Arc 的電腦代理程式中包含了 Arc 連接的機器，所以不需要此延伸模組。

> [!IMPORTANT]
> 需要來賓設定延伸模組和受控識別，才能審核 Azure 虛擬機器。 若要大規模部署擴充功能，請指派下列原則方案：
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>擴充模組上設定的限制

若要限制擴充功能不會影響在機器內執行的應用程式，「來賓設定」不得超過 5% 的 CPU。 內建和自訂定義都有這項限制。 這同樣適用于 Arc 連接電腦代理程式中的「來賓設定」服務。

### <a name="validation-tools"></a>驗證工具

在機器內，「來賓設定」會使用本機工具來執行稽核。

下表顯示每個支援的作業系統上所使用的本機工具清單。 針對內建內容，來賓設定會自動處理載入這些工具。

|作業系統|驗證工具|注意|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| 側載至僅供 Azure 原則使用的資料夾。 不會與 Windows PowerShell DSC 發生衝突。 PowerShell Core 不會新增至系統路徑。|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 在預設位置中安裝 Chef InSpec 版本2.2.61，並新增至系統路徑。 也會安裝 InSpec 套件（包括 Ruby 和 Python）的相依性。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。 一旦收到來賓指派，系統便會每隔 15 分鐘重新檢查一次該組態的設定。 稽核完成時，系統會立即將結果傳送給「來賓設定」資源提供者。 發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。 此更新會導致 Azure 原則評估 Azure Resource Manager 屬性。 隨選 Azure 原則評估會從「來賓設定」資源提供者擷取最新的值。 不過，該評估不會對機器內的設定觸發新的稽核作業。

## <a name="supported-client-types"></a>支援的用戶端類型

「來賓設定」原則包含在新版本中。 如果來賓設定代理程式不相容，則會排除 Azure Marketplace 中可用的舊版作業系統。
下表顯示 Azure 映像上的支援作業系統清單：

|發行者|名稱|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04 和更新版本|
|Credativ|Debian|8 和更新版本|
|Microsoft|Windows Server|2012 和更新版本|
|Microsoft|Windows 用戶端|Windows 10|
|OpenLogic|CentOS|7.3 和更新版本|
|Red Hat|Red Hat Enterprise Linux|7.4-7。8|
|Suse|SLES|12 SP3-SP5|

只要自訂虛擬機器映像是上表中的其中一個作業系統，「來賓設定」原則就會提供支援。

## <a name="network-requirements"></a>網路需求

Azure 中的虛擬機器可以使用其局域網路介面卡或私人連結來與來賓設定服務進行通訊。

Azure Arc 的機器會使用內部部署網路基礎結構進行連線，以連線到 Azure 服務和報告合規性狀態。

### <a name="communicate-over-virtual-networks-in-azure"></a>透過 Azure 中的虛擬網路進行通訊

使用虛擬網路進行通訊的虛擬機器將需要在埠上對 Azure 資料中心進行輸出存取 `443` 。 如果您是在 Azure 中使用不允許輸出流量的私人虛擬網路，請使用網路安全性群組規則來設定例外狀況。 服務標籤 "GuestAndHybridManagement" 可用於參照「來賓設定」服務。

### <a name="communicate-over-private-link-in-azure"></a>透過 Azure 中的私人連結進行通訊

虛擬機器可以使用 [私人連結](../../../private-link/private-link-overview.md) 來與來賓設定服務進行通訊。 套用具有名稱 `EnablePrivateNeworkGC` 和值的標記 `TRUE` 以啟用此功能。 在將來賓設定原則套用至電腦之前或之後，都可以套用標記。

使用 Azure [虛擬公用 IP 位址](../../../virtual-network/what-is-ip-address-168-63-129-16.md) 來路由傳送流量，以使用 azure 平臺資源建立安全、已驗證的通道。

### <a name="azure-arc-connected-machines"></a>Azure Arc 連線的電腦

位於 Azure 外部且由 Azure Arc 連接的節點需要連線至「來賓設定」服務。 [Azure Arc 檔](../../../azure-arc/servers/overview.md)中提供的網路和 proxy 需求的詳細資料。

若要與 Azure 中的「來賓設定」資源提供者通訊，機器需要在連接埠 **443** 上對 Azure 資料中心進行輸出存取。 如果 Azure 中的網路不允許輸出流量，請使用[網路安全性群組](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則來設定例外狀況。 [服務標籤](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" 可用於參照「來賓設定」服務。

針對私人資料中心內的 Arc 連線伺服器，請使用下列模式來允許流量：

- 連接埠︰只需要 TCP 443，即可存取輸出網際網路
- 全域 URL： `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>受控識別需求

在 [虛擬機器上啟用來賓設定原則的方案部署必要條件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8) 中的原則定義會啟用系統指派的受控識別（如果不存在的話）。 方案中有兩個原則定義可管理身分識別建立。 原則定義中的 IF 條件會根據 Azure 中電腦資源的目前狀態，來確保正確的行為。

如果電腦目前沒有任何受控識別，有效原則將會是： [ \[ 預覽 \] ：新增系統指派的受控識別，以在沒有身分識別的虛擬機器上啟用來賓設定指派](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

如果電腦目前有使用者指派的系統身分識別，有效的原則將會是： [ \[ 預覽 \] ：新增系統指派的受控識別，以在具有使用者指派身分識別的虛擬機器上啟用來賓設定指派](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

來賓設定原則會使用 **AuditIfNotExists** 效果。 當指派定義時，後端服務會自動處理 `Microsoft.GuestConfiguration` Azure 資源提供者中所有需求的生命週期。

**AuditIfNotExists**原則在電腦上符合所有需求之前，不會傳回合規性結果。 [Azure 虛擬機器的部署需求](#deploy-requirements-for-azure-virtual-machines)一節中會說明這些需求

> [!IMPORTANT]
> 在先前的來賓設定版本中，需要有一個方案來結合 **DeployIfNoteExists** 和 **AuditIfNotExists** 定義。 不再需要**DeployIfNotExists**定義。 定義和 intiaitives 會加上標籤， `[Deprecated]` 但現有的指派仍將繼續運作。
>
> 需要手動步驟。 如果您先前已在類別中指派原則方案 `Guest Configuration` ，請刪除原則指派並指派新的定義。 來賓設定原則具有名稱模式，如下所示： `Audit <Windows/Linux> machines that <non-compliant condition>`

Azure 原則使用 [來賓設定資源提供者 **>compliancestatus** ] 屬性來報告 **合規** 性節點中的合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/get-compliance-data.md)。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>依照業界基準來稽核作業系統設定

Azure 原則中的一項計畫可讓您依照「基準」來稽核作業系統設定。 _\[Preview\]:Audit Windows VMs that do not match Azure security baseline settings_ 這項定義包含一組以 Active Directory 群組原則為基礎的規則。

大部分的設定都是以參數的形式提供。 參數可讓您自訂所要稽核的內容。
使原則符合您的需求，或將原則對應至第三方資訊，例如產業法規標準。

有些參數支援整數值範圍。 例如，[密碼存留期上限] 設定可能會稽核有效的群組原則設定。 「1,70」範圍會確認使用者至少每隔 70 天 (但不能少於一天) 就需要變更其密碼。

如果您使用 Azure Resource Manager 範本 (ARM 範本) 指派原則，請使用參數檔案來管理例外狀況。 將檔案簽入至版本控制系統，例如 Git。 檔案變更的相關註解會提供證明，說明為何指派是預期值的例外狀況。

#### <a name="applying-configurations-using-guest-configuration"></a>使用「來賓設定」套用設定

只有定義會 _設定 Windows 電腦上的時區_ ，藉由設定時區來變更電腦。 不支援在機器內部設定設定的自訂原則定義。

指派以 _Configure_ 開頭的定義時，您也必須指派 _Deploy prerequisites to enable Guest Configuration Policy on Windows VMs_ 這項定義。 如果您想要，可以將這些定義合併在計畫中。

> [!NOTE]
> 內建的時區原則是唯一支援在機器內部設定設定的定義，以及不支援在機器內設定設定的自訂原則。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>將原則指派給 Azure 外部的機器

「來賓設定」可用的稽核原則包括 **Microsoft.HybridCompute/machines** 資源類型。 系統會自動包含任何上線至 [適用於伺服器的 Azure Arc](../../../azure-arc/servers/overview.md) 且在原則指派範圍內的機器。

### <a name="multiple-assignments"></a>多重指派

「來賓設定」原則目前僅支援對每部電腦指派相同的來賓指派一次，即使原則指派使用不同的參數也一樣。

## <a name="client-log-files"></a>用戶端記錄檔

「來賓設定」擴充功能會將記錄檔寫入至下列位置：

Windows：`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux：`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

其中 `<version>` 是指目前的版本號碼。

### <a name="collecting-logs-remotely"></a>在遠端收集記錄

針對「來賓設定」組態或模組進行疑難排解的第一步，應該是依照如何[建立適用於 Windows 的自訂來賓設定稽核原則](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)的步驟，使用 `Test-GuestConfigurationPackage` Cmdlet。
如果不成功，則收集用戶端記錄有助於診斷問題。

#### <a name="windows"></a>Windows

使用 [Azure VM 執行命令](../../../virtual-machines/windows/run-command.md)從記錄檔中擷取資訊，下列範例 PowerShell 指令碼可能有所幫助。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

使用 [Azure VM 執行命令](../../../virtual-machines/linux/run-command.md)從記錄檔中擷取資訊，下列範例 Bash 指令碼可能有所幫助。

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>來賓設定範例

「來賓設定」內建原則範例可在下列位置取得：

- [內建原則定義 - 來賓設定](../samples/built-in-policies.md#guest-configuration)
- [內建計畫 - 來賓設定](../samples/built-in-initiatives.md#guest-configuration)
- [Azure 原則範例 Github 存放庫](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>後續步驟

- 了解如何從[來賓設定合規性檢視](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)中檢視每個設定的詳細資料
- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](./definition-structure.md)。
- 檢閱[了解原則效果](./effects.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
