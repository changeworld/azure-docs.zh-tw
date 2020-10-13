---
title: 針對 Azure Windows 虛擬機器的高 CPU 問題進行疑難排解
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: ffac5ac4d1a8143590e1d72aaafc8a02d6ab04ca
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977250"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>針對 Azure Windows 虛擬機器的高 CPU 問題進行疑難排解

## <a name="summary"></a>總結

效能問題發生在不同的作業系統或應用程式中，而且每個問題都需要一種獨特的方法來進行疑難排解。 這些問題大多都是圍繞 CPU、記憶體、網路和輸入/輸出 (i/o) 作為發生問題的關鍵位置。 每個區域都會產生不同的徵兆 (有時會同時) ，而且需要不同的診斷與解決方案。

本文討論在執行 Windows 作業系統 (Vm) 的 Azure 虛擬機器上發生的高 CPU 使用量問題。

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Azure Windows Vm 上的高 CPU 問題

除了 i/o 和網路延遲問題之外，CPU 和記憶體的疑難排解所需的工具和步驟與內部部署伺服器相同。 Microsoft 通常支援的其中一項工具是 PerfInsights (適用于 Windows 和 Linux) 。 PerfInsights 可以在方便使用的報表中，提供 Azure VM 的最佳作法診斷。 PerfInsights 也是一種包裝函式工具，可協助您收集 Perfmon、Xperf 和 Netmon 資料，視工具內選取的旗標而定。

適用于內部部署伺服器的大部分現有效能疑難排解工具（例如 Perfmon 或 Procmon）都可在 Azure Windows Vm 上運作。 不過，PerfInsights 是針對 Azure Vm 明確設計，以提供更深入的見解，包括 Azure 最佳做法、SQL 最佳作法、高解析度的 i/o 延遲圖形、CPU 和記憶體索引標籤等等。

無論是否以 User-Mode 或核心模式執行，使用中進程的任何執行緒都需要 CPU 迴圈，才能執行其所建立的程式碼。 許多問題與工作負載直接相關。 存在於伺服器上的工作負載種類會驅動資源耗用量，包括 CPU。

#### <a name="common-factors"></a>常見因素

在高 CPU 的情況下，下列因素很常見：

- 最新的程式碼變更或部署，大部分適用于應用程式，例如 Internet Information Services (IIS) 、Microsoft SharePoint、Microsoft SQL Server 或協力廠商應用程式。

- 最新的更新，可能與作業系統層級更新或應用層級的累積更新和修正程式相關。

- 查詢變更或過期的索引。 SQL Server 和 Oracle 資料層應用程式也會將查詢計劃優化作為另一個因素。 資料變更或缺少適當的索引可能會導致多個查詢需要大量計算。

- 特定 Azure VM。 有些進程（例如 RDAgent）和延伸模組特定的進程（例如監視代理程式、MMA 代理程式或安全性用戶端）可能會造成高 CPU 耗用量。 您必須從設定或已知問題的觀點來查看這些流程。

## <a name="troubleshoot-the-issue"></a>針對問題進行疑難排解

本文著重于隔離有問題的進程。 系統會對驅動高 CPU 耗用量的進程，進行進一步的分析。

例如，如果進程 SQL Server ( # A0) ，則接下來的步驟將是分析在特定時間週期內使用最多 CPU 週期的查詢。

### <a name="scope-the-issue"></a>界定問題範圍

當您針對問題進行疑難排解時，需要詢問幾個問題：

- 是否有問題的模式？ 例如，在每日、每週或每個月的特定時間，是否發生高 CPU 問題？ 如果是，您可以將此問題與工作、報表或使用者登入相互關聯嗎？

- 在最近的程式碼變更之後，高 CPU 問題是否已啟動？ 您是否將更新套用到 Windows 或應用程式中？

- 高 CPU 問題是否在工作負載變更之後啟動，例如增加的使用者數目、更高的資料異常湧入或更大量的報表？

- 針對 Azure，高 CPU 的問題是否會在下列任何情況下啟動？

  - 最近一次重新部署或重新開機之後
  - 當 SKU 或 VM 類型變更時
  - 新增延伸模組時
  - 完成負載平衡器變更之後

### <a name="azure-caveats"></a>Azure 注意事項

了解您的工作量。 當您選取 VM 時，當您查看每月總託管成本時，可能會低估虛擬 CPU (vCPU) 計數。 如果您的工作負載需要大量計算，則選取具有一或兩個個 vcpu 的較小 VM SKU 可能會導致工作負載問題。 為您的工作負載測試不同的設定，以判斷所需的最佳計算功能。

有一些 VM 系列（例如 B (高載模式) 系列）建議用於品質保證 (QA) 和測試。 在實際執行環境中使用這些系列，可在 CPU 點數用盡後限制計算功能。

針對已知的應用程式（例如 SQL Server、Oracle、RDS (遠端桌面服務) 、Windows 虛擬桌面、IIS 或 SharePoint），有 Azure 最佳做法文章，其中包含針對這些工作負載進行最基本設定的建議。

### <a name="ongoing-high-cpu-issues"></a>持續性高 CPU 問題

如果問題目前發生，這是捕捉程式追蹤以判斷造成問題的最佳機會。 您可以使用現有的工具，在內部部署 Windows 伺服器上使用這些工具來找出進程。 適用于 Azure Vm 的 Azure 支援建議使用下列工具。

### <a name="perfinsights"></a>PerfInsights

PerfInsights 是 Azure 支援 VM 效能問題的建議工具。 其設計目的是要涵蓋 CPU、記憶體和高解析度 i/o 圖表的最佳作法和專用分析索引標籤。 您可以透過 Azure 入口網站或從 VM 內執行。 您可以與 Azure 支援團隊共用資料。

#### <a name="run-perfinsights"></a>執行 PerfInsights

PerfInsights 適用于 [Windows](./how-to-use-perfinsights.md) 和 [Linux](./how-to-use-perfinsights-linux.md) 作業系統。 Windows 的選項如下。

#### <a name="run-and-analyze-reports-through-azure-portal"></a>透過 Azure 入口網站執行及分析報表

[透過 Azure 入口網站安裝](./performance-diagnostics.md)時，實際上會在 VM 上安裝擴充功能。 使用者也可以直接前往 [VM blade 中](./performance-diagnostics-vm-extension.md)的延伸模組，然後選擇效能診斷選項，將 PerfInsights 安裝為擴充功能。

#### <a name="azure-portal-option-1"></a>Azure 入口網站選項1

流覽 VM 分頁，然後選取 [ **效能診斷** ] 選項。 系統會要求您安裝選項 (在您選取它的 VM 上使用延伸模組) 。

  ![安裝效能診斷](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Azure 入口網站選項2

流覽以 **診斷並解決** vm 分頁中的問題，並尋找 **vm 效能問題**。

  ![針對 VM 效能問題進行疑難排解](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

如果您選取 [ **疑難排解**]，就會載入 PerfInsights 安裝畫面。

如果您選取 [ **安裝**]，安裝會提供不同的集合選項。

  ![效能分析](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

螢幕擷取畫面中的編號選項與下列批註相關：

1. 若為 **高 CPU**選項，請選取 [ **效能分析** ] 或 [ **Advanced**]。

2. 當您在這裡新增徵兆時，系統會將其新增至報告，以協助您與 Azure 支援服務共用資訊。

3. 選取資料收集的持續時間。 若為高 CPU 選項，請至少選取15分鐘以上。 在 Azure 入口網站模式中，您最多可以收集15分鐘的資料。 針對較長的收集期間，您必須在 VM 內以可執行檔的形式執行程式。

4. 如果您的 Azure 支援要求您收集此資料，您可以在此新增票證號碼。 此為選擇性欄位。

5. 選取此欄位可接受 (EULA) 的使用者授權合約。

6. 如果您想要將此報表提供給 Azure 支援小組，可在此案例中協助您，請選取此欄位。

報表會儲存在您訂用帳戶下的其中一個儲存體帳戶。 稍後可供觀看和下載。

#### <a name="run-perfinsights-from-within-the-vm"></a>從 VM 內執行 PerfInsights

如果您想要針對較長的持續時間執行 PerfInsights，則可以使用這個方法。 [PerfInsights 文章](./how-to-use-perfinsights.md#how-do-i-run-perfinsights)提供執行 PerfInsights 做為可執行檔時所需之不同命令和旗標的詳細逐步解說。 基於高 CPU 使用率的目的，您需要下列其中一種模式：

- Advanced 案例

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- VM 慢速 (效能) 案例

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

命令輸出將位於您儲存 PerfInsights 可執行檔所在的相同資料夾中。

#### <a name="what-to-look-for-in-the-report"></a>要在報表中尋找的內容

在您執行報表之後，內容的位置取決於它是透過 Azure 入口網站還是可執行檔來執行。 針對任一選項，存取產生的記錄檔資料夾，或下載 (（如果 Azure 入口網站) 在本機進行分析）。

### <a name="run-through-the-azure-portal"></a>執行 Azure 入口網站

  ![高影響力效能診斷](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![下載報表](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>從 VM 內執行

您的資料夾結構看起來應該類似下列影像：

  ![選取輸出](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![資料夾結構](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. 任何額外的集合（例如 Perfmon、Xperf、Netmon、SMB 記錄檔、事件記錄檔等等）都可以在輸出檔案夾中找到。

1. 實際的報表以及分析和建議。

1. 針對效能 (VMslow) 和 Advanced，此報表會收集 PerfInsights 執行期間的 **perfmon** 資訊。

1. 事件記錄檔會顯示有用的系統層級或進程損毀詳細資料的快速觀點。

#### <a name="where-to-start"></a>執行位置

開啟 PerfInsights 報表。 [ **結果** ] 索引標籤會根據資源耗用量記錄任何極端值。 如果有高 CPU 使用率的實例，[ **結果** ] 索引標籤會將它分類為 [高影響] 或 [中度影響]。

  ![影響等級資源](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

與上述範例類似，PerfInsights 已執行30分鐘。 在一半的時間中，反白顯示的進程是在較高的一端耗盡 CPU。 如果整個收集時間都有相同的進程正在執行，影響層級會變更為 [ **高**]。

如果您展開 **結果** 事件，您將會看到數個重要的詳細資料。 此索引標籤會以遞減順序列出進程（依 **平均 CPU** 耗用量），並顯示進程是否與系統、Microsoft 擁有的應用程式 (SQL、IIS) 或協力廠商進程相關。

#### <a name="more-details"></a>其他詳細資訊

**CPU**底下有專用的子標籤，可用於詳細的模式分析、每個核心或每個進程。

[ **最高 CPU 取用者** ] 索引標籤有兩個不同的區段，您可以在這裡查看每個處理器的統計資料。 應用程式設計通常會 Single-Threaded 或釘選到單處理器。 在此案例中，有一個或幾個核心在100% 的執行，而其他核心在預期的層級執行。 這些案例較複雜，因為伺服器上的平均 CPU 看起來像預期般執行，但在具有高使用量的核心上釘選的處理常式會比預期更慢。

  ![高 CPU 使用率](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

第二個區段 (同樣重要的) ，是 **最長**的執行中 CPU 取用者。 此區段會顯示程式詳細資料和其 CPU 使用量模式。 清單的排序方式是在頂端有很高的平均 CPU 取用者。

  ![Tom 長時間執行的 CPU 取用者](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

這兩個索引標籤將足以設定下一個疑難排解步驟的路徑。 根據驅動高 CPU 狀況的處理常式，您必須解決先前所詢問的問題。 進程（例如 SQL Server ( # A0) 或 IIS ( # A1) ）需要針對造成此情況的查詢或程式碼變更進行特定的向下切入。 針對 WMI 或 Lsass.exe 之類的系統進程，您必須遵循不同的路徑。

針對 Azure VM 相關進程（例如 RDAgent、OMS 和監視延伸模組可執行檔），您可能必須透過取得 Azure 支援小組的協助來修正新的組建或版本。

### <a name="perfmon"></a>Perfmon

**Perfmon** 是針對 Windows Server 上的資源問題進行疑難排解的最早工具之一。 它不會提供有建議或結果的清楚報告。 相反地，它會要求使用者流覽收集到的資料，並在不同的計數器類別下使用特定的篩選準則。

PerfInsights 會將 Perfmon 收集為 VMSlow 和 advanced 案例的額外記錄。 不過，您可以獨立收集 Perfmon，並擁有下列額外優點：

- 您可以從遠端收集。

- 您 **可以透過工作**進行排程。

- 您可以使用向前復原功能，以較長的持續時間或連續模式來收集它。

請考慮 PerfInsights 中所示的相同範例，以瞭解 Perfmon 如何顯示此資料。 必要的計數器類別如下所示：

- 處理器資訊 >% Processor Time > _Total

- 進程 >% ProcessorTime > 所有實例

#### <a name="where-to-start"></a>執行位置

Perfmon 的輸出檔名稱具有 `.blg` 副檔名。 您可以單獨收集這些檔案，也可以使用 PerfInsights 來收集這些檔案。 在此討論中，您將會使用 `.blg` PerfInsights 資料中包含的 Perfmon，並根據先前的範例收集。

Perfmon 中沒有任何預設的使用者就緒報表可用。 有不同的視圖會變更圖形類型，但處理常式會進行篩選 (或) 為手動識別進程中所需的工作。

> [!NOTE]
> [PAL 工具](https://github.com/clinthuffman/PAL)可以取用檔案 `.blg` 並產生詳細的報告。

若要開始，請選取 [ **加入計數器** ] 類別。

1. 在 [**可用的計數器**] 下，選取 [**處理器資訊**] 類別中的 [ **% ProcessorTime** ] 計數器。

1. 選取 **_Total**，以提供所有合併核心的統計資料。

1. 選取 [新增]。 此視窗會在 [**新增的計數器**] 下顯示 **% ProcessorTime** 。

  ![新增處理器時間](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

載入計數器之後，您會在收集時間範圍內看到折線圖趨勢圖。 您可以選取或清除計數器。 到目前為止，您只新增了一個計數器。

  ![效能監視器設定](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

每個計數器都會有 **平均值**、 **最小**值和 **最大** 值。 將焦點放在 **平均** 和 **最大** 值，因為平均值可能會隨著資料收集的持續時間而不同。 如果在整體集合為40分鐘的時間內，發生了10分鐘的高 CPU 活動，平均值將會更低。

先前的趨勢圖表顯示大約15分鐘內 **處理器總數** 的範圍接近80%。

#### <a name="identify-the-process"></a>識別進程

我們發現伺服器在指定的一段時間內有高 CPU 耗用量，但尚未識別驅動程式。 不同于使用 PerfInsights，在此情況下，您必須以手動方式搜尋罪犯進程。

針對這項工作，您必須清除或移除先前新增的 **% ProcessorTime** 計數器，然後加入新的類別：

- 進程 >% ProcessorTime > 所有實例

此類別會針對在該時間執行的所有進程載入計數器。

  ![新增計數器](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

在一般的實際執行電腦上，可以執行數百個或進程。 因此，可能需要一段時間來清除每個看似低或平趨勢圖表的計數器。

若要加速此程式，請使用 **長條圖** 視圖，然後將檢視類型從 **線條** 變更為 **長條圖**，這會為您提供橫條圖。 您會發現在收集期間，選擇發生高 CPU 使用率的進程比較容易。

因為 **總**有一個橫條，所以會將焦點放在顯示高耗盡率的橫條圖上。 您可以刪除其他橫條來清除視圖。 現在，shift 回 **行** 視圖。

  ![效能監視器指示器](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

現在可以更輕鬆地捕捉遇到問題的進程。 根據預設， **最大** 值和 **最小** 值為伺服器上的核心數目或進程執行緒數目的倍數。

  ![效能監視器結果](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

可用的工具清單不是在 PerfInsights for Perfmon 結尾。 您可以存取其他工具，例如 **ProcessMonitor** (ProcMon) 或 **Xperf**。 有許多協力廠商工具可供您視需要使用。

### <a name="azure-monitoring-tools"></a>Azure 監視工具

Azure Vm 有可靠的計量，包括 CPU、網路 i/o 和 i/o 位元組等基本資訊。 針對先進的計量（例如 Azure 監視器），您只需要進行幾個選項，即可設定及使用您指定的儲存體帳戶。

#### <a name="basic-default-counters"></a>基本 (預設) 計數器

  ![圖表計量](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>啟用 Azure 監視器

啟用 Azure 監視器計量之後，軟體會在 VM 上安裝擴充功能，然後開始收集包含 Perfmon 計數器的細微計量。

  ![診斷儲存體帳戶](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

**基本**計數器類別會設定為**預設值**。 不過，您也可以設定 **自訂** 集合。

  ![效能計數器](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

啟用設定之後，您可以在 [**計量**] 區段中查看這些**來賓**計數器。 如果計量達到特定臨界值，您也可以設定 **警示** (包括電子郵件訊息) 。

  ![計量命名空間](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

如需有關如何使用 Azure 監視器來管理 Azure Vm 的詳細資訊，請參閱 [使用 Azure 監視器監視 azure 虛擬機器](../../azure-monitor/insights/monitor-vm-azure.md)。

### <a name="reactive-troubleshooting"></a>被動疑難排解

如果已發生此問題，您必須先探索造成高 CPU 問題的原因。 回應式的立場可能有點棘手。 因為已發生問題，所以資料收集模式不會很有用。

如果這個問題是一次性發生，可能會很難判斷哪個應用程式造成它。 如果 Azure VM 設定為使用 OMS 或其他診斷追蹤，您仍可深入瞭解造成此問題的原因。

如果您要處理重複的模式，請在問題發生時收集資料。

PerfInsights 還沒有 **排程的執行** 功能。 不過，您可以透過命令列來執行和排程 Perfmon。

### <a name="logman-command"></a>Logman 命令

**Logman Create Counter**命令是用來透過命令列執行 Perfmon 收集、透過**工作管理員**進行排程，或在遠端執行。

**範例** (包括遠端收集模式) 

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

您也可以從相同 VNET 中的對等 Azure VM 電腦啟動 Logman.exe。

若要深入瞭解這些參數，請參閱 [logman create counter](/windows-server/administration/windows-commands/logman-create-counter)。

在問題發生時收集 Perfmon 資料之後，分析資料的其餘步驟會與稍早所述的步驟相同。

## <a name="conclusion"></a>結論

對於任何效能問題，瞭解您的工作負載是解決問題的關鍵。 您必須將焦點放在生產工作負載，以評估不同 VM Sku 和不同磁片儲存體選項上的選項。 在不同的 Vm 上測試解決方案的程式可協助您做出最佳決策。

由於使用者作業和資料量會有所不同，因此請一律在 VM 的計算、網路和 i/o 功能中保留緩衝區。 現在，工作負載中的任何突然變更都不會有很大的效果。

如果您預見工作負載即將增加，請移至具有更多運算能力的更高 SKU。 如果工作負載需要大量計算，請明智地選擇 VM Sku。