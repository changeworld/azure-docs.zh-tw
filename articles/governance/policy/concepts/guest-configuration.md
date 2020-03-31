---
title: 學習審核虛擬機器的內容
description: 瞭解 Azure 策略如何使用來賓配置代理審核虛擬機器內的設置。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: cc2ba11f75da5f993b99c90e5d0cc1030003203e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257251"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

除了審核和[修復](../how-to/remediate-resources.md)Azure 資源之外，Azure 策略還可以審核電腦內的設置。 此驗證會由「來賓設定」延伸模組和用戶端執行。 透過用戶端的延伸模組會驗證下列設定：

- 作業系統的配置
- 應用程式設定或目前狀態
- 環境設定

Azure 原則來賓設定目前只會稽核機器內的設定。 其不會套用設定。

## <a name="extension-and-client"></a>延伸模組和用戶端

要審核電腦內的設置，將啟用[虛擬機器擴展](../../../virtual-machines/extensions/overview.md)。 延伸模組會下載適用的原則指派及相對應的設定定義。

### <a name="limits-set-on-the-extension"></a>在擴展上設置的限制

為了限制擴展影響在電腦內運行的應用程式，來賓配置不允許超過 CPU 利用率的 5%。 對於內置定義和自訂定義，都存在此限制。

## <a name="register-guest-configuration-resource-provider"></a>註冊來賓設定資源提供者

您必須先註冊資源提供者，才能使用「來賓設定」。 您可以透過入口網站或透過 PowerShell 註冊。 如果通過門戶分配來賓配置策略，則會自動註冊資來源提供者。

### <a name="registration---portal"></a>註冊 - 入口網站

若要透過 Azure 入口網站註冊「來賓設定」的資源提供者，請依照下列步驟進行操作：

1. 啟動 Azure 入口網站，然後按一下 [所有服務]****。 搜尋並選取 [訂用帳戶]****。

1. 尋找並按一下您想要啟用「來賓設定」的訂用帳戶。

1. 在 [訂用帳戶]**** 頁面的左側功能表中，按一下 [資源提供者]****。

1. 篩選或捲動，直到找出 **Microsoft.GuestConfiguration** 為止，然後按一下同一列上的 [註冊]****。

### <a name="registration---powershell"></a>註冊 - PowerShell

若要透過 PowerShell 註冊「來賓設定」的資源提供者，請執行下列命令：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>驗證工具

在電腦內部，來賓配置用戶端使用本地工具運行審核。

下表顯示每個支援的作業系統上所使用的本機工具清單：

|作業系統|驗證工具|注意|
|-|-|-|
|Windows|[Windows 電源外殼所需狀態配置](/powershell/scripting/dsc/overview/overview)v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 「來賓設定」延伸模組會安裝 Ruby 和 Python。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。 一旦收到來賓指派，系統便會每隔 15 分鐘檢查一次設定。 稽核完成後，系統會立即將結果傳送給來賓設定資源提供者。 發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。 此更新使 Azure 策略評估 Azure 資源管理器屬性。 按需 Azure 策略評估從來賓配置資來源提供者檢索最新值。 但是，它不會觸發對電腦內配置進行新的審核。

## <a name="supported-client-types"></a>支援的用戶端類型

下表顯示 Azure 映像上的支援作業系統清單：

|發行者|名稱|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 資料中心， 2012 R2 資料中心， 2016 資料中心， 2019 資料中心|
|Microsoft|Windows 用戶端|Windows 10|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 來賓配置可以審核運行受支援的作業系統的節點。 如果要審核使用自訂映射的虛擬機器，則需要複製**DeployIfNotExists**定義並修改**If**部分以包括映射屬性。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

任何版本都不支援 Windows 伺服器 Nano 伺服器。

## <a name="guest-configuration-extension-network-requirements"></a>來賓配置擴展網路要求

要與 Azure 中的來賓配置資來源提供者通信，電腦需要對埠**443**上的 Azure 資料中心進行出站訪問。 如果在 Azure 中使用不允許出站流量的專用虛擬網路，請使用[網路安全性群組](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則配置異常。
[服務標籤](../../../virtual-network/service-tags-overview.md)"來賓和混合管理"可用於引用來賓佈建服務。

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

來賓配置運行的每個審核都需要兩個策略定義：**部署IfNotExists**定義和**AuditIfNot存在**定義。 **DeployIfNotExists**定義用於使用來賓配置代理和其他元件準備電腦以支援[驗證工具](#validation-tools)。

**DeployIfNotExists** 原則定義會驗證並修正下列項目：

- 驗證已為電腦分配了要評估的配置。 如果當前不存在分配，請獲取分配，並通過以下條件準備電腦：
  - 使用[託管標識](../../../active-directory/managed-identities-azure-resources/overview.md)對電腦進行身份驗證
  - 安裝最新版的 **Microsoft.GuestConfiguration** 延伸模組
  - 安裝[驗證工具](#validation-tools)和相依性 (如有需要)

如果**DeployIfNotExists**分配不符合要求，則可以使用[修正任務](../how-to/remediate-resources.md#create-a-remediation-task)。

**部署非存在**分配符合時，**審核非存在**策略分配將使用本地驗證工具來確定配置分配是合規還是不合規。 驗證工具會將結果提供給「來賓設定」用戶端。 用戶端會將結果轉送至「來賓延伸模組」，以便透過「來賓設定」資源提供者提供結果。

「Azure 原則」會使用「來賓設定」資源提供者 **complianceStatus** 屬性在 [合規性]**** 節點中回報合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/get-compliance-data.md)。

> [!NOTE]
> **"已部署不存在"** 策略是 **"審核不存在"** 策略返回結果所必需的。 如果沒有**部署"不存在"，****審核不存在**策略將顯示"0 的 0"資源作為狀態。

「來賓設定」的所有內建原則都包含在一個方案中，以聚集要在指派中使用的定義。 名為_\["預覽\]：審核 Linux 和 Windows 電腦內密碼安全設置_"的內置計畫包含 18 個策略。 針對 Windows 有 6 組 **DeployIfNotExists** 和 **AuditIfNotExists**，針對 Linux 則有 3 組。 [策略定義](definition-structure.md#policy-rule)邏輯驗證是否僅評估目標作業系統。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>按照行業基線審核作業系統設置

Azure 策略中提供的一個計畫提供了根據 Microsoft 的"基線"審核虛擬機器內的作業系統設置的功能。 定義：_\[預覽\]：審核與 Azure 安全基線設置不匹配_的 Windows VM 包括一組完整的審核規則，這些規則基於活動目錄群組原則的設置。

大多數設置都可以作為參數提供。 此功能允許您自訂審核的內容，以使策略與組織要求保持一致，或者將策略映射到協力廠商資訊（如行業監管標準）。

某些參數支援整數值範圍。 例如，可以使用範圍運算子設置最大密碼期限參數，以便機器擁有者具有靈活性。 您可以審核要求使用者更改密碼的有效群組原則設置不應超過 70 天，但不應少於一天。 如資訊氣泡中所述的參數，要使此業務策略成為有效的審核值，將值設置為"1，70"。

如果使用 Azure 資源管理器部署範本分配策略，則可以使用參數檔從原始程式碼管理管理這些設置。 使用 Git 等工具管理對稽核原則的更改，並在每次簽入時提供注釋，可以證明為什麼分配應該是預期值的例外。

#### <a name="applying-configurations-using-guest-configuration"></a>使用來賓配置應用配置

Azure 策略的最新功能配置了電腦內的設置。 定義 通過配置時區來_配置 Windows 電腦上的時區_進行更改。

分配以_配置_開頭的定義時，還必須分配定義 _"部署先決條件"，以便在 Windows VM 上啟用來賓配置策略_。 如果願意，可以將這些定義合併到一個計畫中。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>將策略分配給 Azure 以外的電腦

可用於來賓配置的稽核原則包括**Microsoft.混合計算/電腦**資源類型。 自動包含在策略分配範圍內的伺服器 Azure [Arc](../../../azure-arc/servers/overview.md)上的任何電腦。

### <a name="multiple-assignments"></a>多個分配

來賓配置策略當前僅支援每台電腦分配同一來賓分配一次，即使策略分配使用不同的參數也是如此。

## <a name="built-in-resource-modules"></a>內置資源模組

安裝來賓配置擴展時，"來賓配置"PowerShell 模組包含在最新版本的 DSC 資源模組中。 此模組可以使用模組頁面[Guest 配置](https://www.powershellgallery.com/packages/GuestConfiguration/)中的"手動下載"連結從 PowerShell 庫下載。 可以將".nupkg"檔案格式重命名為".zip"以解壓縮和查看。

## <a name="client-log-files"></a>用戶端記錄檔

來賓配置擴展將日誌檔寫入以下位置：

Windows：`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux：`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

其中`<version>`引用當前版本號。

### <a name="collecting-logs-remotely"></a>遠端收集日誌

排除來賓配置或模組的故障排除的第一步應該是使用`Test-GuestConfigurationPackage`Cmdlet，按照如何為 Windows[創建自訂來賓配置稽核原則](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)的步驟。
如果沒有成功，收集用戶端日誌可以説明診斷問題。

#### <a name="windows"></a>Windows

要使用 Azure VM 運行命令功能從 Windows 電腦中的日誌檔捕獲資訊，以下示例 PowerShell 腳本非常有用。 有關詳細資訊，請參閱[使用運行命令在 Windows VM 中運行 PowerShell 腳本](../../../virtual-machines/windows/run-command.md)。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

要使用 Azure VM 運行命令功能從 Linux 電腦中的日誌檔中捕獲資訊，以下示例 Bash 腳本可能很有用。 有關詳細資訊，請參閱[使用運行命令在 Linux VM 中運行 shell 腳本](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>來賓配置示例

策略來賓配置內置計畫的源可在以下位置提供：

- [內置策略定義 - 來賓配置](../samples/built-in-policies.md#guest-configuration)
- [內置計畫 - 訪客配置](../samples/built-in-initiatives.md#guest-configuration)
- [Azure 策略示例 GitHub 存儲庫](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 回顧[瞭解政策效果](effects.md)。
- 瞭解如何[以程式設計方式創建策略](../how-to/programmatically-create.md)。
- 瞭解如何[獲取合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
