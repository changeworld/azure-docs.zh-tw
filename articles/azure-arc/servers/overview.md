---
title: 適用於伺服器的 Azure Arc (預覽) 概觀
description: 了解如何使用適用於伺服器的 Azure Arc 來管理裝載於 Azure 外部的機器，就如同管理 Azure 資源一樣。
keywords: azure 自動化, DSC, powershell, Desired State Configuration, 更新管理, 變更追蹤, 清查, Runbook, python, 圖形, 混合式
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: f11eedaf5f70cb24fa6c1588b7f26b2eed4734ce
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121794"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>什麼是適用於伺服器的 Azure Arc (預覽)？

適用於伺服器的 Azure Arc (預覽) 可讓您在公司網路或其他雲端提供者上管理裝載於 Azure 外部的 Windows 和 Linux 機器，就如同您管理原生 Azure 虛擬機器一樣。 混合式機器連線到 Azure 時就會變成已連線的機器，並且視為 Azure 中的資源。 每個已連線的機器都有資源識別碼，可在訂用帳戶內作為資源群組的一部分來管理，並可從標準的 Azure 結構中 (例如 Azure 原則和套用標籤) 獲益。

若要對裝載於 Azure 外部的混合式機器提供這項體驗，您必須在計畫連線到 Azure 的每部機器上安裝 Azure Connected Machine 代理程式。 此代理程式不會提供任何其他功能，也不會取代 Azure [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。

>[!NOTE]
>此預覽版本主要用於評估，不建議用來管理重要的生產機器。
>

## <a name="supported-scenarios"></a>支援的案例

當您將機器連線到適用於伺服器的 Azure Arc (預覽) 時，其可讓您執行下列設定管理工作：

- 使用與 Azure 虛擬機器原則指派相同的體驗來指派 [Azure 原則客體設定](../../governance/policy/concepts/guest-configuration.md)。

- 監視已連線機器的客體作業系統效能，並探索應用程式元件，以使用[適用於 VM 的 Azure 監視器](../../azure-monitor/insights/vminsights-overview.md)來監視其程序以及與應用程式所通訊的其他資源相依性。

- 使用適用於非 Azure Windows 或 Linux 機器的受支援 [Azure VM 擴充功能](manage-vm-extensions.md)，簡化其他 Azure 服務 (例如 Azure 自動化狀態設定和 Azure 監視器 Log Analytics 工作區) 的部署。 這包括使用自訂指令碼擴充功能來執行部署後設定或軟體安裝。

從混合式機器收集並儲存在 Log Analytics 工作區中的記錄資料，現在會包含機器特有的屬性，例如資源識別碼。 這可以用來支援[資源內容](../../azure-monitor/platform/design-logs-deployment.md#access-mode)記錄存取。

## <a name="supported-regions"></a>支援區域

使用適用於伺服器的 Azure Arc (預覽) 時，只有特定區域可受到支援：

- EastUS
- WestUS2
- WestEurope
- SoutheastAsia

在大部分情況下，您在建立安裝指令碼時所選取的位置，應該是地理位置最接近機器位置的 Azure 區域。 待用資料會儲存在包含您所指定區域的 Azure 地理位置中，如果您有資料落地需求，這可能也會影響選擇的區域。 如果您的機器所連線的 Azure 區域受到中斷影響，連線的機器不會受到影響，但使用 Azure 的管理作業可能無法完成。 在發生區域性中斷的情況下，如果您有多個位置可提供異地備援服務，最好將每個位置中的機器連線到不同的 Azure 區域。

### <a name="agent-status"></a>代理程式狀態

Connected Machine 代理程式每隔 5 分鐘會定期將活動訊號訊息傳送至服務。 如果服務停止接收來自機器的這些活動訊號訊息，系統會將該機器視為離線，且入口網站中的狀態會在 15 到 30 分鐘內自動變更為**中斷連線**。 從 Connected Machine 代理程式收到後續的活動訊號訊息時，其狀態會自動變更為**連線**。

## <a name="next-steps"></a>後續步驟

在為多部混合式電腦評估或啟用伺服器的 ARC (預覽) 之前，請參閱[連線的電腦代理程式概觀](agent-overview.md)一文，了解所需的項目、代理程式的相關技術詳細資料，以及部署方法。
