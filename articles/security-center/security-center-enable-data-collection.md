---
title: 自動部署 Azure 資訊安全中心的代理程式 | Microsoft Docs
description: 本文說明如何為 Azure 資訊安全中心使用的 Log Analytics 代理程式和其他代理程式設定自動佈建。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: f5cf432e3824ca0bb441a458a08fc7353291cf1f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490052"
---
# <a name="auto-provisioning-agents-and-extensions-from-azure-security-center"></a>從 Azure 資訊安全中心自動佈建代理程式和擴充功能

資訊安全中心會從您的 Azure 虛擬機器 (VM)、虛擬機器擴展集、IaaS 容器和非 Azure (包括內部部署機器) 機器收集資料，以監視是否有安全性弱點和威脅。 

必須收集資料，才能掌握遺漏的更新、設定錯誤的 OS 安全性設定、端點保護狀態，以及健康情況和威脅防護。 計算資源 (VM、虛擬機器擴展集、IaaS 容器和非 Azure 電腦) 才需要收集資料。 即使您未佈建代理程式，也可受惠於 Azure 資訊安全中心；不過，您的安全性有限，而且不支援以上所列的功能。  

收集資料的方式：

- **Log Analytics 代理程式** 會讀取機器的各種安全性相關設定和事件記錄，並將資料複製到工作區進行分析。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄 (Windows 事件記錄)、執行中程序、電腦名稱、IP 位址和已登入的使用者。
- **安全性延伸模組** (例如 [適用於 Kubernetes 的 Azure 原則附加元件](../governance/policy/concepts/policy-for-kubernetes.md)) 也可以提供特殊資源類型的相關資料給資訊安全中心。

> [!TIP]
> 隨著資訊安全中心增長，可以監視的資源類型也會隨之增加。 延伸模組的數目也會增加。 自動佈建會藉由運用 Azure 原則的功能來擴充，以支援其他資源類型。

:::image type="content" source="./media/security-center-enable-data-collection/auto-provisioning-options.png" alt-text="資訊安全中心的自動佈建設定頁面":::


## <a name="why-use-auto-provisioning"></a>為什麼使用自動佈建？
此頁面上所述的任何代理程式和擴充功能都「可以」手動安裝 (請參閱[手動安裝 Log Analytics 代理程式](#manual-agent))。 不過，**自動佈建** 可在現有機器和新機器上安裝所有必要的代理程式和擴充功能，以確保更快速涵蓋所有支援資源的安全性，藉此降低管理負擔。 

建議您啟用預設為停用的自動佈建。

## <a name="how-does-auto-provisioning-work"></a>自動佈建如何運作？
資訊安全中心的自動佈建設定可針對每個支援的擴充類型進行切換。 當您啟用擴充功能的自動佈建時，您可以指派適當的「**不存在時進行部署**」原則，以確保擴充功能會在該類型的所有現有和未來資源上佈建。

> [!TIP]
> 在[了解 Azure 原則效果](../governance/policy/concepts/effects.md)中深入了解 Azure 原則效果，包括「不存在時進行部署」原則。

## <a name="enable-auto-provisioning-of-the-log-analytics-agent"></a>啟用 Log Analytics 代理程式的自動佈建功能<a name="auto-provision-mma"></a>
Log Analytics 代理程式的自動佈建開啟時，資訊安全中心會在所有支援的 Azure 虛擬機器和任何新建立的虛擬機器上部署該代理程式。 如需支援的平台清單，請參閱 [Azure 資訊安全中心支援的平台](security-center-os-coverage.md)。

若要啟用 Log Analytics 代理程式的自動佈建功能：

1. 在資訊安全中心功能表中，選取 [定價和設定]。
1. 選取相關的訂用帳戶。
1. 在 [自動佈建] 頁面上，將代理程式的狀態設定為 [開啟]。
1. 從設定選項窗格中，定義要使用的工作區。

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="自動將 Log Analytics 代理程式佈建至 VM 的設定選項" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **將 Azure VM 連線至資訊安全中心建立的預設工作區** - 資訊安全中心會在相同地理位置中建立新的資源群組和預設工作區，並將代理程式連線到該工作區。 如果訂用帳戶包含來自多個地理位置的 VM，則資訊安全中心會建立多個工作區，以確保符合資料隱私權需求。

        工作區和資源群組的命名慣例如下： 
        - 工作區：DefaultWorkspace-[subscription-ID]-[geo] 
        - 資源群組：DefaultResourceGroup-[geo] 

        資訊安全中心會依據訂用帳戶的已設定定價層，在工作區上自動啟用資訊安全中心解決方案。 

        > [!TIP]
        > 如有關於預設工作區的問題，請參閱：
        >
        > - [在資訊安全中心所建立的工作區上，我是否需支付 Azure 監視器記錄的費用？](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [預設的 Log Analytics 工作區會在何處建立？](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [我可以刪除資訊安全中心所建立的預設工作區嗎？](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **將 Azure VM 連線到不同工作區** - 從下拉式清單中，選取要儲存所收集資料的工作區。 下拉式清單會包含您所有訂用帳戶的所有工作區。 您可以使用此選項，從不同訂用帳戶中執行的虛擬機器收集資料，並將其全部儲存在您選取的工作區中。  

        如果您已有現有的 Log Analytics 工作區，則可以使用相同的工作區 (需要該工作區的讀取和寫入權限)。 如果您在組織中使用集中式工作區，而且想要將它用於安全性資料收集，此選項會很有用。 深入了解[管理 Azure 監視器中記錄資料和工作區的存取](../azure-monitor/platform/manage-access.md)。

        如果您選取的工作區已經啟用 Security 或 SecurityCenterFree 解決方案，將會自動設定定價。 如果尚未這麼做，請在工作區上安裝資訊安全中心解決方案：

        1. 在資訊安全中心功能表中，開啟 [定價和設定]。
        1. 選取要與代理程式連線的工作區。
        1. 選取 [開啟 Azure Defender] 或 [關閉 Azure Defender]。

1. 從 [Windows 安全性事件] 設定中，選取要儲存的原始事件資料量：
    - **無** – 停用安全性事件的儲存空間。 這是預設值。
    - **最小** - 一小部分的事件，適合想要將事件量最小化的使用者。
    - **一般** – 這組事件能滿足大部分客戶的需求並提供完整的稽核記錄。
    - **所有事件** – 適合想要確定已儲存所有事件的客戶。

    > [!TIP]
    > 若要在工作區層級上設定這些選項，請參閱[設定工作區層級上的安全性事件選項](#setting-the-security-event-option-at-the-workspace-level)。
    > 
    > 如需這些選項的詳細資訊，請參閱 [Log Analytics 代理程式的 Windows 安全性事件選項](#data-collection-tier)。

1. 在設定窗格中選取 [套用]。

1. 選取 [儲存]。 如果需要佈建工作區，代理程式安裝最多可能需要 25 分鐘的時間。

1. 系統會詢問您是否要重新設定先前連線到預設工作區的受監控虛擬機器：

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="檢視重新設定受監視 VM 的選項":::

    - **否** - 您的新工作區設定只會套用至探索到未安裝 Log Analytics 代理程式的新 VM。
    - **是** - 您的新工作區設定會套用到所有 VM，且目前連線到資訊安全中心所建工作區的每個 VM 都會重新連線到新的目標工作區。

   > [!NOTE]
   > 如果您選取 [是]，在所有 VM 均重新連線至新的目標工作區之前，請不要刪除資訊安全中心所建立的工作區。 如果過早刪除工作區，這項作業將會失敗。


## <a name="enable-auto-provisioning-of-extensions"></a>啟用擴充功能的自動佈建

若要啟用 Log Analytics 代理程式以外任何擴充功能的自動佈建： 

1. 從 Azure 入口網站中資訊安全中心的功能表中，選取 [定價和設定]。
1. 選取相關的訂用帳戶。
1. 選取 [自動佈建]。
1. 如果您要為 Microsoft Dependency 代理程式啟用自動佈建，請確定 Log Analytics 代理程式也已設定為自動部署。 
1. 將相關擴充功能的狀態切換為 [開啟]。

    :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="切換以啟用 K8s 原則附加元件的自動佈建":::

1. 選取 [儲存]。 已指派 Azure 原則，並建立補救工作。

    |副檔名  |原則  |
    |---------|---------|
    |Kubernetes 的原則附加元件|[將 Azure 原則附加元件部署至 Azure Kubernetes Service 叢集](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
    |Microsoft Dependency 代理程式 (預覽) (Windows VM)|[為 Windows 虛擬機器部署 Dependency Agent](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
    |Microsoft Dependency 代理程式 (預覽) (Linux VM)|[為 Linux 虛擬機器部署 Dependency Agent](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|



## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Log Analytics 代理程式的 Windows 安全性事件選項 <a name="data-collection-tier"></a> 

在 Azure 資訊安全中心中選取 [資料收集層]，只會影響 Log Analytics 工作區中安全性事件的「儲存空間」。 無論您選擇在工作區中儲存的安全性事件層級為何，Log Analytics 代理程式都會收集並分析資訊安全中心威脅防護所需的安全性事件。 選擇儲存安全性事件，將允許在工作區中調查、搜尋和稽核這些事件。

### <a name="requirements"></a>規格需求 
儲存 Windows 安全性事件資料需要 Azure Defender。 [深入了解 Azure Defender](azure-defender.md)。

將資料儲存在 Log Analytics 中，可能會產生額外的資料儲存費用。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。

### <a name="information-for-azure-sentinel-users"></a>Azure Sentinel 使用者的資訊 
Azure Sentinel 的使用者：請注意，可以從 Azure 資訊安全中心或 Azure Sentinel (但不能兩者) 設定在單一工作區的內容中收集安全性事件。 如果您打算將 Azure Sentinel 新增至已收到來自 Azure 資訊安全中心的警示，並已設定要收集安全性事件的工作區，您有兩個選項：
- 讓 Azure 資訊安全中心的 [安全性事件] 收集保持原狀。 您將能夠在 Azure Sentinel 和 Azure Defender 中查詢及分析這些事件。 但是，您將無法在 Azure Sentinel 中監視連接器的連線狀態或變更其組態。 如果這對您很重要，請考慮第二個選項。
- 停用 Azure 資訊安全中心內的安全性事件收集 (在 Log Analytics 代理程式的設定中，將 [Windows 安全性事件] 設定為 [無])。 然後在 Azure Sentinel 中新增安全性事件連接器。 如同第一個選項，您將能夠同時在 Azure Sentinel 和 Azure Defender/ASC 中查詢及分析事件，但您現在能夠監視連接器的連線狀態，或只在 Azure Sentinel 中變更其組態。

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>哪些事件類型會儲存為「一般」和「最小」？
這些集合都是設計用來處理一般情況。 實作之前，請務必評估哪一個適合您的需求。

為了判定 **一般** 和 **最小** 選項的事件，我們致力於與客戶合作並配合業界標準，以了解每個事件及其使用方式的未篩選頻率。 此流程中採取下列指導方針：

- **最小** - 確保這個集合所涵蓋的僅有可能代表發生成功入侵和重大事件的事件，數量很少。 例如，這個集合包含使用者成功和失敗的登入活動 (事件識別碼 4624、4625)，不過不包含對於稽核而言相當重要但對於偵測而言沒有意義而且數量相對多的登出活動。 這個集合的大部分資料量是登入事件和流程建立事件 (事件識別碼 4688)。
- **一般** - 提供此集合中的完整使用者稽核記錄。 例如，這個集合包含使用者登入和使用者登出活動 (事件識別碼 4634)。 納入了各種稽核動作，例如安全性群組變更、主要網域控制站 Kerberos 作業，以及業界組織建議的其他事件。

「一般」集合包含的事件相當少，因為選擇一般集合而非其他事件的主要動機在於降低數量，且不篩選出特定事件。

以下是對於每個集合的安全性和 App Locker 事件識別碼的完整分析：

| 資料層 | 收集的事件指標 |
| --- | --- |
| 最小 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 通用 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - 如果您使用群組原則物件 (GPO)，則建議您啟用稽核原則程序建立事件 4688 和事件 4688 內的 [CommandLine] 欄位。 如需更多程序建立事件 4688 的相關資訊，請參閱資訊安全中心的[常見問題集](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)。 如需更多稽核原則的相關資訊，請參閱[稽核原則建議](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)。
> -  若要啟用[自適性應用程式控制](security-center-adaptive-application.md)的資料收集，資訊安全中心會在稽核模式中設定本機 AppLocker 原則以允許所有的應用程式。 這會導致 AppLocker 產生事件，然後由資訊安全中心收集並利用。 請務必注意，在已經設定 AppLocker 原則的機器上不會設定此原則。 
> - 若要收集 Windows 篩選平台[事件識別碼 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)，您必須啟用[稽核篩選平台連線](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>設定工作區層級上的安全性事件選項

您可以定義要儲存在工作區層級上的安全性事件資料層級。

1. 從 Azure 入口網站中資訊安全中心的功能表中，選取 [定價和設定]。
1. 選取相關的工作區。 工作區的唯一資料收集事件就是本頁所述的 Windows 安全性事件。

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="設定要儲存在工作區中的安全性事件資料":::

1. 選取要儲存的原始事件資料量，然後選取 [儲存]。

## <a name="manual-agent-provisioning"></a>手動佈建代理程式 <a name="manual-agent"></a>
 
若要手動安裝 Log Analytics 代理程式：

1. 停用自動佈建。

1. (選擇性) 建立工作區。

1. 在您要安裝 Log Analytics 代理程式的工作區上啟用 Azure Defender：

    1. 在資訊安全中心功能表中，選取 [定價和設定]。

    1. 設定您要安裝代理程式的工作區。 請確定工作區位於您在資訊安全中心使用的相同訂用帳戶中，而且您具備工作區的讀取/寫入權限。

    1. 選取 [開啟 Azure Defender]，然後 **儲存**。

       >[!NOTE]
       >如果工作區已經啟用 **Security** 或 **SecurityCenterFree** 解決方案，將會自動設定定價。 

1. 若要使用 Resource Manager 範本在新的 VM 上部署代理程式，請安裝 Log Analytics 代理程式：

   - [安裝適用於 Windows 的 Log Analytics 代理程式](../virtual-machines/extensions/oms-windows.md)
   - [安裝適用於 Linux 的 Log Analytics 代理程式](../virtual-machines/extensions/oms-linux.md)

1. 若要在現有的 VM 上部署代理程式，請遵循[收集有關 Azure 虛擬機器的資料](../azure-monitor/learn/quick-collect-azurevm.md)中的指示 ([收集事件和效能資料] 是選擇性區段)。

1. 若要使用 PowerShell 來部署代理程式，請使用虛擬機器文件中的指示：

    - [Windows 電腦](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Linux 機器](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> 如需有關如何使用 PowerShell 上架資訊安全中心的說明，請參閱[使用 PowerShell 自動化上架 Azure 資訊安全中心](security-center-powershell-onboarding.md)。


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>在預先存在的代理程式安裝情況下自動佈建 <a name="preexisting"></a> 

下列使用案例會指定在已經安裝代理程式或擴充功能的情況下，自動佈建的運作方式。 

- **Log Analytics 會安裝在機器上，但不是以擴充功能的形式 (直接代理程式)** - 如果 Log Analytics 代理程式直接安裝在 VM 上 (而不是安裝為 Azure 擴充功能)，資訊安全中心將會安裝 Log Analytics 代理程式擴充功能，並且可將 Log Analytics 代理程式升級至最新版本。
安裝的代理程式會繼續向其已設定的工作區報告，此外也會向資訊安全中心所設定的工作區報告 (Windows 機器上支援多路連接)。
如果已設定的工作區是使用者工作區 (而不是資訊安全中心的預設工作區)，您就將必須在其上安裝 "security/"securityFree" 解決方案，資訊安全中心才能開始處理向該工作區報告的 VM 和電腦所產生的事件。

    對於 Linux 機器尚不支援代理程式多路連接 - 因此，如果偵測到現有的代理程式安裝，將不會進行自動佈建，且不會改變機器的設定。

    對於在 2019 年 3 月 17 日之前上線到資訊安全中心之訂用帳戶中的現有機器，在偵測到現有的代理程式時，將不會安裝 Log Analytics 代理程式延伸模組，且機器將不受影響。 針對這類機器，請參閱「請解決機器上的 Monitoring Agent 健康情況問題」的建議，以解決這些機器上的代理程式安裝問題。
  
- **System Center Operations Manager 代理程式會安裝在機器上** - 資訊安全性中心會將 Log Analytics 代理程式延伸模組與現有的 System Center Operations Manager 代理程式並存安裝。 現有的 Operations Manager 代理程式會繼續正常地向 Operations Manager 伺服器報告。 Operations Manager 代理程式和 Log Analytics 代理程式會共用相同的執行階段程式庫，而該程式庫會在此程序中更新至最新版本。 如果已安裝 Operations Manager 代理程式版本 2012，請 **勿** 啟用自動佈建。

- **預先存在的 VM 擴充功能已存在**：
    - 當監視代理程式安裝為擴充功能時，擴充功能組態僅允許報告至單一工作區。 資訊安全中心不會覆寫既存的使用者工作區連線。 資訊安全中心會將 VM 中的安全性資料儲存在已連線的工作區中，前提是其上已安裝 "security" 或 "securityFree" 解決方案。 資訊安全中心可在此程序中將擴充功能版本升級至最新版本。  
    - 若要查看現有擴充功能將資料傳送到哪個工作區，請執行測試以[驗證與 Azure 資訊安全中心的連線](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center)。 或者，您可以開啟 Log Analytics 工作區、選取工作區、選取 VM，以及查看 Log Analytics 代理程式連線。 
    - 如果您環境中的用戶端工作站上已安裝 Log Analytics 代理程式，並會向現有的 Log Analytics 工作區報告，請檢閱 [Azure 資訊安全中心支援的作業系統](security-center-os-coverage.md)清單，確保您的作業系統受支援。 如需詳細資訊，請參閱[現有的 Log Analytics 客戶](./faq-azure-monitor-logs.md)。
 

## <a name="disable-auto-provisioning"></a>停用自動佈建 <a name="offprovisioning"></a>

當您停用自動佈建後，新的 VM 上將不會佈建代理程式。

若要關閉代理程式的自動佈建：

1. 從入口網站中資訊安全中心的功能表中，選取 [定價和設定]。
1. 選取相關的訂用帳戶。
1. 選取 [自動佈建]。
1. 將相關代理程式的 [狀態] 切換為 [關閉]。

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="切換以停用每個代理程式類型的自動佈建":::

1. 選取 [儲存]。 自動佈建停用後，就不會顯示預設的工作區設定區段：

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="停用自動佈建後，設定儲存格會是空的":::


> [!NOTE]
>  停用自動佈建不會從已佈建代理程式的 Azure VM 移除 Log Analytics 代理程式。 如需移除 OMS 擴充功能的相關資訊，請參閱[我要如何移除資訊安全中心安裝的 OMS 擴充功能](faq-data-collection-agents.md#remove-oms)。
>


## <a name="troubleshooting"></a>疑難排解

-   若要找出自動佈建安裝問題，請參閱[監視代理程式健康情況問題](security-center-troubleshooting-guide.md#mon-agent)。

-  若要識別監視代理程式網路需求，請參閱[針對監視代理程式網路需求進行疑難排解](security-center-troubleshooting-guide.md#mon-network-req)。
-   若要識別手動上架問題，請參閱[如何針對 Operations Management Suite 上架問題進行疑難排解](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)。

- 若要識別未受監視的 VM 和電腦問題：

    如果機器未執行 Log Analytics 代理程式擴充功能，VM 或電腦就不會由資訊安全中心監視。 機器可能已安裝本機代理程式，例如 OMS 直接代理程式或 System Center Operations Manager 代理程式。 具有這些代理程式的機器會識別為未受監視，因為資訊安全中心無法完整支援這些代理程式。 若要能完整享有資訊安全中心的所有功能，則需要 Log Analytics 代理程式擴充功能。

    若要深入了解為什麼資訊安全中心無法成功監視為了自動佈建而初始化的 VM 和電腦，請參閱[監視代理程式健康情況問題](security-center-troubleshooting-guide.md#mon-agent)。




## <a name="next-steps"></a>後續步驟
本文說明資料收集和自動佈建如何在資訊安全中心運作。 若要深入了解資訊安全中心，請參閱下列頁面：

- [Azure 資訊安全中心常見問題集](faq-general.md)-- 尋找有關使用服務的常見問題。
- [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。

本文說明如何安裝 Log Analytics 代理程式，以及設定用於儲存所收集資料的 Log Analytics 工作區。 需要執行這兩項作業，才能啟用資料收集。 將資料儲存在 Log Analytics 中，不論您使用新的或現有的工作區，都可能會產生額外的資料儲存費用。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。

