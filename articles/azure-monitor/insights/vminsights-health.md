---
title: 瞭解 Azure 虛擬機器的健康情況 |Microsoft Docs
description: 本文說明如何使用適用於 VM 的 Azure 監視器來瞭解虛擬機器和基礎作業系統的健全狀況。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3cecb04a4f299051860c45425f0fc4e13c3722ea
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275122"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>瞭解 Azure 虛擬機器的健康情況

Azure 包含監視空間中特定角色或工作的服務，但它不會針對裝載于 Azure 虛擬機器（Vm）上的作業系統（OSs）提供深入的健全狀況。 雖然您可以針對不同的條件使用 Azure 監視器，但它並不是設計來建立核心元件的健康情況，也不能代表 Vm 的整體健康情況。

藉由使用適用於 VM 的 Azure 監視器健全狀況，您可以主動監視 Windows 或 Linux 來賓 OS 的可用性和效能。 健全狀況功能會使用代表主要元件及其關聯性的模型，提供指定如何測量元件健全狀況的準則，並在偵測到狀況不良的情況時傳送警示。

若要查看 Azure VM 和基礎 OS 的整體健全狀況狀態，可以從兩個觀點來觀察：直接從 VM，或從 Azure 監視器資源群組中的所有 Vm。

本文說明如何在適用於 VM 的 Azure 監視器健全狀況功能偵測到健康情況時，快速地評估、調查和解決健全狀況問題。

如需設定適用於 VM 的 Azure 監視器相關資訊，請參閱[啟用適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。

>[!NOTE]
>我們最近[宣佈](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)根據我們從公開預覽客戶收到的意見反應，對健全狀況功能進行的變更。 由於我們將進行的變更數目，我們將停止為新客戶提供健康情況功能。 現有的客戶可以繼續使用健全狀況功能。 如需詳細資訊，請參閱我們的[一般可用性常見問題](vminsights-ga-release-faq.md)。 

## <a name="monitoring-configuration-details"></a>監視設定詳細資料

本節概述監視 Azure Windows 和 Linux Vm 的預設健全狀況準則。 所有健康情況準則皆已預先設定為在偵測到狀況不良的情況時傳送警示。

| 監視名稱 | 頻率（分鐘） | 回顧持續期間（分鐘） | 運算子 | 閾值 | 警示狀態 | Severity | 工作負載類別 | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| 邏輯磁片連線 | 5 | 15 | <> | 1（true） | 重大 | Sev1 | Linux | 
| 邏輯磁碟可用空間 | 5 | 15 | < | 200 MB （警告）<br> 100 MB （重大） | 警告 | Sev1<br> Sev2 | Linux | 
| 邏輯磁碟 % 可用閒置空間 | 5 | 15 | < | 5% | 重大 | Sev1 | Linux | 
| 邏輯磁碟 % 可用空間 | 5 | 15 | < | 5% | 重大 | Sev1 | Linux | 
| 網路介面卡狀態 | 5 | 15 | <> | 1（true） | 警告 | Sev2 | Linux | 
| 作業系統 Available Megabytes Memory | 5 | 10 | < | 2.5 MB | 重大 | Sev1 | Linux | 
| Disk Avg. Disk sec/Read | 5 | 25 | > | 0.05 秒 | 重大 | Sev1 | Linux | 
| Disk Avg. Disk sec/Transfer | 5 | 25 | > | 0.05 秒 | 重大 | Sev1 | Linux | 
| Disk Avg. Disk sec/Write | 5 | 25 | > | 0.05 秒 | 重大 | Sev1 | Linux | 
| 磁片狀態 | 5 | 25 | <> | 1（true） | 重大 | Sev1 | Linux | 
| 作業系統 Total Percent Processor Time | 5 | 10 | >= | 95% | 重大 | Sev1 | Linux | 
| CPU 使用率百分比總計 | 5 | 10 | >= | 95% | 重大 | Sev1 | Windows | 
| 檔案系統錯誤或損毀 | 60 | 60 | <> | 4 | 重大 | Sev1 | Windows | 
| 每次讀取的平均邏輯磁碟秒數 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 每次傳輸的平均邏輯磁碟秒數 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 每次邏輯磁片寫入的平均秒數（邏輯磁片） | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 目前磁碟佇列長度 (邏輯磁碟) | 5 | 60 | >= | 32 | 警告 | Sev2 | Windows | 
| 邏輯磁片可用空間（MB） | 15 | 60 | > | 500 MB 警告<br> 300 MB 嚴重 | 重大 | Sev1<br> Sev2 | Windows | 
| 邏輯磁片可用空間（%） | 15 | 60 | > | 10% 警告<br> 5% 重大 | 重大 | Sev1<br> Sev2 | Windows |
| 邏輯磁碟閒置時間百分比 | 15 | 360 | <= | 20% | 警告 | Sev2 | Windows | 
| 已用來讀取的頻寬百分比 | 5 | 60 | >= | 60% | 警告 | Sev2 | Windows | 
| 已使用的頻寬百分比總計 | 5 | 60 | >= | 75% | 警告 | Sev2 | Windows | 
| 已用來寫入的頻寬百分比 | 5 | 60 | >= | 60% | 警告 | Sev2 | Windows | 
| DHCP 用戶端服務健康狀態 | 5 | 12 | <> | 4（正在執行） | 重大 | Sev1 | Windows | 
| DNS 用戶端服務健康狀態 | 5 | 12 | <> | 4（正在執行） | 重大 | Sev1 | Windows | 
| Windows 事件記錄服務健康狀態 | 5 | 12 | <> | 4（正在執行） | 重大 | Sev1 | Windows | 
| Windows 防火牆服務健康狀態 | 5 | 12 | <> | 4（正在執行） | 重大 | Sev1 | Windows | 
| RPC 服務健康狀態 | 5 | 12 | <> | 4（正在執行） | 重大 | Sev1 | Windows | 
| 伺服器服務健康狀態 | 5 | 12 | <> | 4（正在執行） | 重大 | Sev1 | Windows | 
| Windows 遠端管理服務健康狀態 | 5 | 12 | <> | 4（正在執行） | 重大 | Sev1 | Windows | 
| 記憶體的可用 MB | 5 | 10 | < | 100 MB | 重大 | Sev1 | Windows | 
| 可用系統分頁表項目 | 5 | 10 | <= | 5000 | 重大 | Sev1 | Windows | 
| 每秒記憶體分頁數 | 5 | 10 | >= | 5000/秒 | 警告 | Sev1 | Windows | 
| 使用中認可的記憶體百分比 | 5 | 10 | > | 80% | 重大 | Sev1 | Windows | 
| 每次傳輸的平均磁碟秒數 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 每次磁碟寫入的平均秒數 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| Current Disk Queue Length | 5 | 60 | >= | 32 | 警告 | Sev2 | Windows | 
| 磁碟閒置時間百分比 | 5 | 60 | >= | 20% | 警告 | Sev2 | Windows | 

>[!NOTE]
>回顧持續時間代表 [查閱] 視窗檢查度量值的頻率，例如過去五分鐘內。  

>[!NOTE]
>[頻率] 代表計量警示檢查條件是否符合的頻率，例如每分鐘一次。  這是執行健康情況準則的速率，而回顧是評估健康情況準則的持續時間。 例如，健康情況準則會評估條件**CPU 使用率**是否大於95百分比，且頻率為5分鐘，而維持大於 95% 15 分鐘（3個連續的評估週期），則狀態會更新為 [重大嚴重性] （如果尚未這麼做）。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

若要登入，請移至[Azure 入口網站](https://portal.azure.com)。

## <a name="introduction-to-azure-monitor-for-vms-health"></a>適用於 VM 的 Azure 監視器健全狀況簡介

將健康情況功能用於單一 VM 或 Vm 群組之前，請務必瞭解資訊的呈現方式，以及視覺效果所代表的內容。

### <a name="view-health-directly-from-a-vm"></a>直接從 VM 查看健全狀況

若要查看 Azure VM 的健全狀況，請在 VM 的左窗格中選取 [ **Insights （預覽）** ]。 在 [VM insights] 頁面上，預設會開啟 [**健康**情況] 索引標籤，並顯示 VM 的健全狀況。

![所選取 Azure 虛擬機器之適用於 VM 的 Azure 監視器健康情況概觀](./media/vminsights-health/vminsights-directvm-health-01.png)

在 [**來賓 VM 健康**情況] 區段中，表格會顯示由 VM 的健康情況準則監視的效能元件健全狀況匯總，以及由狀況不良元件所引發的 vm 健康情況警示總數。 這些元件包括**CPU**、**記憶體**、**磁片**和**網路**。 展開 [來賓 VM 健全狀況] 旁的箭號，以查看其元件的健全狀況。

![適用於 VM 的 Azure 監視器所選 Azure 虛擬機器的元件健康情況狀態](./media/vminsights-health/vminsights-directvm-health-02.png)

選取元件旁邊的狀態，將會在所選元件的內容中開啟健康情況診斷體驗。 它會顯示該元件的狀態組合，並說明用來計算其健康情況的健康情況準則。 如需詳細資訊，請參閱[健康情況診斷和使用健全狀況準則](#health-diagnostics)。 如需警示的詳細資訊，請參閱[警示](#alerts)。

為 VM 定義的健全狀態如下表所述：

|圖示 |健康情況狀態 |意義 |
|-----|-------------|---------------|
| |Healthy |VM 在定義的健康情況中。 此狀態表示未偵測到任何問題，且 VM 正常運作。 使用父匯總套件監視時，健全狀況會匯總並反映子系的最佳或最差案例狀態。|
| |重大 |狀態不在定義的健康情況中，表示偵測到一個或多個重大問題。 這些問題必須解決，才能還原正常的功能。 使用父匯總套件監視時，健全狀況狀態會匯總並反映子系的最佳或最差案例狀態。|
| |警告 |此狀態介於定義之健康情況的兩個閾值之間，其中一個表示警告狀態，另一個表示「重大」狀態（可以設定三個健全狀況狀態臨界值），或當非嚴重的問題可能造成嚴重問題時，衝突. 使用父系匯總套件監視時，如果有一或多個子系處於警告狀態，則父代會反映警告狀態。 如果一個子系處於重大狀態，而另一個子系處於警告狀態，則父匯總會將健全狀況狀態顯示為 [重大]。|
| |Unknown |因為有數個原因，所以無法計算狀態。 下一節提供其他詳細資料和可能的解決方案。 |

未知的健全狀況狀態可能是由下列問題所造成：

- 已重新設定代理程式，且不再向啟用適用於 VM 的 Azure 監視器時所指定的工作區報告。 若要將代理程式設定為向工作區報告，請參閱[新增或移除工作區](../platform/agent-manage.md#adding-or-removing-a-workspace)。
- 已刪除 VM。
- 已刪除與適用於 VM 的 Azure 監視器相關聯的工作區。 如果您有頂級支援權益，可以復原工作區。 前往[Premier](https://premier.microsoft.com/)並開啟支援要求。
- 已刪除解決方案相依性。 若要在 Log Analytics 工作區中重新啟用 ServiceMap 和 InfrastructureInsights 解決方案，請使用[Azure Resource Manager 範本](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution)重新安裝 ServiceMap 解決方案。 若要重新安裝 InfastructureInsights 解決方案，請傳送電子郵件 vminsights@microsoft.com。 
- VM 已關閉。
- Azure VM 服務無法使用，或正在執行維護。
- 已符合工作區[每日資料或保留期限制](../platform/manage-cost-storage.md)。

選取 [**查看健康情況診斷**] 以開啟頁面，其中顯示 vm 的所有元件、相關聯的健康情況準則、狀態變更，以及監視與 VM 相關的元件偵測到的其他問題。

如需詳細資訊，請參閱[健康情況診斷](#health-diagnostics)。

在 [**健全狀況**] 區段中，資料表會顯示健全狀況準則監視之效能元件的健全狀況匯總套件。 這些元件包括**CPU**、**記憶體**、**磁片**和**網路**。 選取元件會開啟一個頁面，其中列出該元件的所有監視條件和健全狀況狀態。

當您從執行 Windows 的 Azure VM 存取健全狀況時，前五個核心 Windows 服務的健全狀況狀態會顯示在 [**核心服務健全狀況**] 底下。 選取任何服務時，會開啟一個頁面，其中列出該元件的健全狀況準則監視及其健全狀況狀態。

選取健全準則的名稱會開啟 [屬性] 窗格。 在此窗格中，您可以檢查設定詳細資料，包括健康情況準則是否有對應的 Azure 監視器警示。

如需詳細資訊，請參閱[健康情況診斷和使用健全狀況準則](#health-diagnostics)。

### <a name="aggregate-vm-perspective"></a>匯總 VM 的觀點

若要在資源群組中查看所有 Vm 的健康情況集合，請從入口網站中的導覽清單選取 [ **Azure 監視器**]，然後選取 [**虛擬機器（預覽）** ]。

![Azure 監視器中的 VM Insights 監視檢視](./media/vminsights-health/vminsights-aggregate-health.png)

在 [**訂**用帳戶] 和 [**資源群組**] 下拉式清單中，選取適當的資源群組，其中包含與群組相關的 vm，以查看其回報的健全狀況狀態。 您的選擇僅適用于健全狀況功能，並不會繼續執行 [**效能**] 或 [**對應**] 索引標籤。

[**健全狀況**] 索引標籤提供下列資訊：

* 有多少部 Vm 處於「重大」或「狀況不良」狀態，以及有多少個狀況良好或未提交資料（稱為「不明」狀態）。
* 作業系統回報狀況不良狀態的 Vm 和數目。
* 因為偵測到處理器、磁片、記憶體或網路介面卡的問題，以健全狀況狀態分類，所以有多少部 Vm 狀況不良。
* 由於使用核心 OS 服務偵測到的問題（依健全狀況狀態分類），有多少部 Vm 狀況不良。

在 [**健康**情況] 索引標籤上，您可以識別監視 VM 的健康情況準則所偵測到的重大問題，並查看警示詳細資料和相關的知識文章。 這些文章可以協助診斷和修復問題。 選取任何嚴重性以開啟依照該嚴重性篩選的 [所有警示](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 頁面。

[依作業系統的 VM 分佈] 清單會顯示依 Windows 版本或 Linux 發行版本列出的 VM 及其版本。 在每個 OS 類別中，vm 會根據 VM 的健康情況進一步細分。

![VM Insights 虛擬機器分佈檢視方塊](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

選取任何資料行，包括**VM 計數**、**重大**、**警告**、**狀況良好**或**未知**。 在 [**虛擬機器**] 頁面中，查看符合所選資料行的已篩選結果清單。

例如，若要檢查執行 Red Hat Enterprise Linux 7.5 版的所有 Vm，請選取該 OS 的 [ **VM 計數**] 值，它會列出符合該篩選準則的 vm 及其目前的健全狀況狀態。

![Red Hat Linux VM 的範例彙總](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

您按一下 [**顯示健全狀況**] 核取方塊，就會傳回資料表中篩選結果的健全狀況狀態。  

![Red Hat Linux Vm 的範例健全狀況狀態](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

針對清單中的任何一個專案，您可以按一下對應的健全狀況狀態以啟動 [健康情況診斷]，其中會顯示針對所選 VM 評估健康情況的方式。 

在 [**虛擬機器**] 頁面中，如果您在 [ **vm 名稱**] 資料行下選取 vm 的名稱，系統會將您導向至 [ **vm 實例**] 頁面。 此頁面提供會影響所選 VM 的警示和健全狀況準則問題的詳細資料。 藉由選取頁面左上角的**健全狀況狀態**圖示來篩選健全狀況狀態詳細資料，以查看哪些元件狀況不良。 您也可以查看由警示嚴重性分類且狀況不良之元件所引發的 VM 健康情況警示。

從**vm 清單**視圖中，選取 vm 的名稱以開啟該 Vm 的**健康**情況頁面，如同您直接從 VM 選取 [ **Insights （預覽）** ] 一樣。

![所選取 Azure 虛擬機器的 VM Insights](./media/vminsights-health/vminsights-directvm-health.png)

[Azure 監視器] 頁面中的 [**虛擬機器（預覽）** ] 會顯示 VM 和警示的匯總健康情況狀態。 此健全狀況狀態是依嚴重性分類，其代表當健全狀況狀態根據條件從狀況良好變更為狀況不良時，所引發的 VM 健康情況警示。 選取 [**重大] 條件中**的 [vm] 會開啟一個頁面，其中包含一或多個處於重大健全狀況狀態的 vm 清單。

選取其中一個 Vm 的健全狀況狀態會顯示 VM 的**健康情況診斷**視圖。 在此視圖中，您可以判斷哪些健全準則會反映健康狀態問題。 [**健康情況診斷**] 頁面開啟時，會顯示所有 VM 元件及其相關聯的健康情況準則，以及目前的健全狀況狀態。

如需詳細資訊，請參閱[健康情況診斷](#health-diagnostics)。

選取 [檢視所有健康情況準則]，即會開啟一個頁面，顯示適用於此功能的所有健康情況準則清單。 您可以根據下列選項進一步篩選資訊：

* **類型**。 有三種類型的健康情況準則可評估條件，並匯總受監視 VM 的整體健全狀況狀態：
    - **單位**。 測量 VM 的某些層面。 此健康情況準則類型可能會檢查效能計數器來判斷元件的效能、執行腳本以執行綜合交易，或是監看表示發生錯誤的事件。 篩選預設會設定為 [單位]。
    - 相依**性。** 提供不同實體之間的健全狀況匯總套件。 此健康情況準則可讓實體的健康情況取決於其所依賴之另一種實體類型的健康情況，以便成功運作。
    - **匯總**。 提供類似健全狀況準則的結合健全狀況狀態。 單位和相依性健康情況準則通常是在匯總健康情況準則之下設定。 除了可針對目標設為實體的許多不同健康情況準則提供更好的一般組織方式，彙總健康情況準則還能針對不同的實體分類提供唯一的健康狀態。

* **類別**。 用來針對報表用途將類似準則分組的健全準則類型。 這些類別是**可用性**和**效能**。

若要查看哪些實例狀況不良，請選取 [**狀況不良元件**] 資料行底下的值。 在此頁面中，資料表會列出處於重大健全狀況狀態的元件。

## <a name="health-diagnostics"></a>健康情況診斷

[**健康情況診斷**] 頁面可讓您以視覺化方式呈現 VM 的健康情況模型。 此頁面會列出所有 VM 元件、相關聯的健康情況準則、狀態變更，以及與 VM 相關之受監視元件所識別的其他重大問題。

![適用於 VM 的健康情況診斷頁面範例](./media/vminsights-health/health-diagnostics-page-01.png)

使用下列方法啟動健康狀態診斷：

* 根據匯總 VM 在 Azure 監視器中的所有 Vm 的匯總健康情況狀態：

    1. 在 [**健全狀況**] 頁面上，選取 [**來賓 VM 健全狀況**] 區段下 [**重大**]、[**警告**]、[狀況**良好**] 或 [**未知**] 健康狀態的圖示
    2. 移至列出符合該篩選類別目錄之所有 Vm 的頁面。
    3. 選取 [**健全狀況狀態**] 欄位中的值，以開啟該 VM 的「健康情況診斷」範圍。

* 從匯總 VM 觀點來看，Azure 監視器中的 OS。 在 [VM 分佈] 下方，選取任一個資料行值將會開啟 [虛擬機器] 頁面，並在表格中傳回符合已篩選分類的清單。 選取 [**健全狀況狀態**] 資料行下的值，會開啟所選取 VM 的健康情況診斷。
 
* 從 [適用於 VM 的 Azure 監視器**健全狀況**] 索引標籤上的 [來賓] VM，選取 [**查看健康情況診斷**]。

健康情況診斷會將健康情況資訊組織成兩個類別：可用性和效能。
 
針對元件定義的所有健全準則（例如邏輯磁片、CPU 等等）都可以在不篩選這兩個類別的情況下查看。 當您選取 [**可用性**] 或 [**效能**] 時，這些視圖可以是所有準則的「全部」觀點，或透過 [類別] 篩選結果。

此外，[條件] 分類也可以在 [**健全準則**] 資料行旁看到。 如果準則不符合選取的類別，[**健全準則**] 資料行中就會出現訊息，指出**選取的類別沒有可用的健康情況準則**。

健康情況準則的狀態是由四種類型的其中一種來定義：「**重大**」、「**警告**」、「**良好**」和「**未知**」。 前三個是可設定的，這表示您可以直接在 [**健全準則**設定] 窗格中修改監視的臨界值。 您也可以使用 Azure 監視器 REST API[更新監視器](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)作業來進行這項操作。 [**未知**] 無法設定，而且會保留給特定案例。

[**健康情況診斷**] 頁面包含三個主要區段：

* 元件模型
* 健康情況準則
* 狀態變更

![[健康情況診斷] 頁面的區段](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>元件模型

[**健康情況診斷**] 頁面中最左邊的資料行是 [**元件模型**]。 與 VM 相關聯的所有元件都會顯示在此資料行中，以及其目前的健全狀況狀態。

在下列範例中，探索到的元件是**磁片**、**邏輯磁片**、**處理器**、**記憶體**和**作業系統**。 將在此資料行中探索及顯示這些元件的多個執行個體。

例如，下圖顯示 VM 有兩個邏輯磁片（ **C：** 和**D：** ）實例處於狀況良好的狀態：

![[健康情況診斷] 中所呈現的範例元件模型](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>健康情況準則

[健康情況診斷] 頁面中的 [中央] 資料行是 [**健康情況準則**]。 針對 VM 定義的健康情況模型會以階層樹狀結構來顯示。 VM 的健康情況模型會由單位和彙總健全準則所組成。

![[健康情況診斷] 中所呈現的範例健康情況準則](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

健康情況準則會測量受監視實例的健康情況，可能是臨界值、實體狀態等。 如先前所述，健康情況準則具有二或三個可設定的健全狀況狀態閾值。 在任何時間點，健康情況準則只能處於其中一種可能的狀態。

健全狀況模型會定義準則，以判斷目標的整體目標和元件的健全狀況。 準則的階層會顯示在 [**健康情況診斷**] 頁面上的 [**健康情況準則**] 區段中。

健康情況匯總原則是匯總健康情況準則設定的一部分（預設值為 [**最差**]）。 在本文的[監視設定詳細資料](#monitoring-configuration-details)一節中，您可以找到一組執行為此功能一部分的預設健康情況準則。

您也可以使用 Azure 監視器 REST API[依資源監視實例清單](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)來取得所有健全準則的清單。 此準則包括針對 Azure VM 資源執行的設定詳細資料。

選取右側的省略號連結，即可修改 [**單位**健全狀況] 準則類型的設定。 選取 [**顯示詳細資料**] 以開啟 [設定] 窗格。

![設定健康情況準則範例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

在所選健康情況準則的 [設定] 窗格中，如果您使用範例 [**每次磁片寫入的平均秒數**]，則可以使用不同的數值來設定臨界值。 這是兩個狀態的監視，這表示它只能從**狀況良好**變更為**警告**。

其他健康情況準則有時候會使用三種狀態，您可以在其中設定警告和重大健全狀況狀態閾值的值。 您也可以使用 Azure 監視器 REST API[監視器](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)設定來修改閾值。

>[!NOTE]
>將健全狀況準則設定變更套用到一個實例，會將它們套用到所有受監視的實例。 例如，如果您選取**磁片-1 D：** ，然後修改**每個寫入的平均磁片秒數**閾值，此變更會套用至 VM 上探索到和監視的所有實例。


![設定單位監視器範例的健康情況準則](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

如果您想要深入瞭解健康情況準則，我們包含了知識文章，可協助您找出問題、原因和解決方法。 選取頁面上的 [**查看資訊**]，以查看相關的知識文章。

若要查看適用於 VM 的 Azure 監視器健康狀態隨附的所有知識文章，請參閱[Azure 監視器健全狀況知識檔](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)。

### <a name="state-changes"></a>狀態變更

[**健康情況診斷**] 頁面的最右側資料行是 [**狀態變更**]。 此資料行會列出與 [**健康情況準則**] 區段中所選健康情況準則相關聯的所有狀態變更，或如果已從資料表的 [**元件模型**] 或 [**健康情況準則**] 資料行選取 vm，則為 vm 的狀態變更。

![[健康情況診斷] 中所呈現的範例狀態變更](./media/vminsights-health/health-diagnostics-page-statechanges.png)

下一節顯示健全狀況準則狀態和相關聯的時間。 這項資訊會顯示資料行頂端的最新狀態。

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>元件模型、健全狀況準則和狀態變更資料行的關聯

這三個資料行彼此交互連結。 當您選取 [**元件模型**] 資料行中的實例時，會將 [**健康情況準則**] 資料行篩選到該元件視圖。 同樣地，[**狀態變更**] 資料行會根據選取的健康情況準則進行更新。

![選取受監視的執行個體和結果範例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

例如，如果您從 [**元件型號**] 底下的清單中選取 [*磁片-1 D：* ]，**健康情況準則**會篩選為 [*磁片-1D：* ]，而 [**狀態變更**] 則會根據*磁片-1 D：* 的可用性顯示狀態變更。

若要查看更新的健全狀況狀態，您可以選取 [重新整理 **] 連結來**重新整理 [健康情況診斷] 頁面。 如果要基於預先定義的輪詢間隔來更新健康情況準則的健康狀態，此工作可讓您避免等候，並反映最新的健康狀態。 **健全狀況準則狀態**是一個篩選準則，可讓您根據所選健全狀況狀態來界定結果的範圍： 狀況良好、警告、重大、不明 和 全部 右上角的**上次更新**時程表示上次重新整理 [健康情況診斷] 頁面的時間。

## <a name="alerts"></a>警示

適用於 VM 的 Azure 監視器健康狀態會與[Azure 警示](../../azure-monitor/platform/alerts-overview.md)整合。 當偵測到時，從狀況良好狀態變更為狀況不良狀態時，它會引發警示。 警示會依嚴重性分類，從嚴重性0到嚴重性4，嚴重性0是最高層級。

警示不會與動作群組建立關聯，以在警示觸發時通知您。 在訂用帳戶範圍具有擁有者角色的使用者，必須遵循[設定警示](#configure-alerts)一節中的步驟來設定通知。

[**警示**] 區段下的 [**健全狀況**儀表板] 提供依嚴重性分類的 VM 健康情況警示總數。 當您選取警示總數或對應到嚴重性層級的數字時，[警示] 頁面隨即開啟並列出所有符合您選取項目的警示。

例如，如果您選取對應至**嚴重性層級 1**的資料列，您會看到下列視圖：

![所有嚴重性層級 1 的警示範例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

[**所有警示**] 頁面的範圍不是只顯示符合您選取範圍的警示。 它也會依**資源類型**進行篩選，只顯示 VM 資源所引發的健康狀態警示。 此格式會反映在警示清單中的 [**目標資源**] 資料行底下，其中會顯示在符合狀況不良條件時引發警示的 Azure VM。

其他資源類型或服務的警示不適合包含在此視圖中。 這些警示包括記錄警示，其依據是您通常會從預設 Azure 監視器 [[所有警示](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)] 頁面中看到的記錄查詢或計量警示。

您可以在頁面頂端的下拉式功能表中選取值，以篩選此視圖。

|資料行 |描述 |
|-------|------------|
|訂用帳戶 |選取 Azure 訂用帳戶。 檢視僅會包含所選訂用帳戶中出現的警示。 |
|資源群組 |選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 |
|資源類型 |選取一個或多個資源類型。 依預設只會選取目標**虛擬機器**的警示，並將其包含在此檢視中。 指定資源群組之後，才可使用此欄。 |
|資源 |選取資源。 只有以該資源作為目標的警示才會包含在檢視中。 只有在指定資源類型之後，才可以使用此資料行。 |
|Severity |選取警示嚴重性，或選取 [所有] 以包含所有嚴重性的警示。 |
|監視條件 |如果條件已不再作用中，請選取監視條件來篩選警示（如果已由系統引發或解決）。 或者，選取 [**全部**] 以包含所有條件的警示。 |
|警示狀態 |選取警示狀態 [**新增**]、[已**認可**]、[**已關閉**] 或 [**全部**]，以包含所有狀態的警示。 |
|監視器服務 |選取服務，或選取 [所有] 以包含所有服務。 此功能僅支援來自 VM Insights 的警示。|
|時間範圍| 只有在所選時間範圍內引發的警示才會包含在檢視中。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 |

當您選取警示時，會顯示 [**警示詳細資料**] 頁面。 此頁面提供警示的詳細資料，並可讓您變更其狀態。

若要深入了解如何管理警示，請參閱[使用 Azure 監視器來建立、檢視及管理警示](../../azure-monitor/platform/alerts-metric.md)。

>[!NOTE]
>目前不支援根據健康情況準則建立新的警示，或從入口網站修改 Azure 監視器中現有的健康情況警示規則。


![所選取警示的 [警示詳細資料] 窗格](./media/vminsights-health/alert-details-pane-01.png)

您可以藉由選取一個或多個警示來變更警示狀態，然後從左上角的 [**所有警示**] 頁面中選取 [**變更狀態**]。 在 [**變更警示狀態**] 窗格中選取其中一個狀態，在 [**批註**] 欄位中新增變更的描述，然後選取 **[確定]** 以認可您的變更。 當資訊經過驗證並套用變更時，請在功能表的 [**通知**] 底下追蹤進度。

### <a name="configure-alerts"></a>設定警示
您無法從 Azure 入口網站管理特定的警示管理工作。 這些工作必須使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)來執行。 具體來說：

- 啟用或停用健康情況準則的警示
- 設定健康情況準則警示的通知

每個範例都會在您的 Windows 電腦上使用[ARMClient](https://github.com/projectkudu/armclient) 。 如果您不熟悉此方法，請參閱[使用 ARMClient](../platform/rest-api-walkthrough.md#use-armclient)。

#### <a name="enable-or-disable-an-alert-rule"></a>啟用或停用警示規則

若要啟用或停用特定健康情況準則的警示，必須使用 [**已停**用] 或 [**已啟用**] 的值來修改屬性**alertGeneration** 。

為了識別特定健康情況準則的*monitorId* ，下列範例會示範如何針對**每次傳輸的準則 LogicalDisk\Avg Disk 秒**來查詢該值：

1. 在終端機視窗中，輸入 armclient.exe login。 這麼做會提示您登入 Azure。

2. 輸入下列命令來抓取特定 VM 上作用中的所有健康情況準則，並識別*monitorId*屬性的值：

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    下列範例顯示*ARMCLIENT GET*命令的輸出。 記下*MonitorId*的值。 下一個步驟需要此值，我們必須在此指定健康情況準則的識別碼，並修改其屬性以建立警示。

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. 輸入下列命令來修改*alertGeneration*屬性：

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 輸入在步驟2中使用的 GET 命令，以確認屬性值已設定為 [**已停用**]。

#### <a name="associate-an-action-group-with-health-criteria"></a>將動作群組與健康情況準則產生關聯

適用於 VM 的 Azure 監視器健全狀況會在從狀況不良的健康情況準則產生警示時，支援 SMS 和電子郵件通知。 若要設定通知，請注意傳送 SMS 或電子郵件通知的已設定動作群組的名稱。

>[!NOTE]
>您必須針對想要接收通知的每個受監視 VM 執行此動作。 它不適用於資源群組中的所有 Vm。

1. 在終端機視窗中，輸入*armclient 登*入。 這麼做會提示您登入 Azure。

2. 輸入下列命令，讓動作群組與警示規則產生關聯：
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. 若要確認已成功更新屬性**actionGroupResourceIds**的值，請輸入下列命令：

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    輸出應該看起來像下列準則：
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>後續步驟

- 若要找出限制和整體 VM 效能，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。

- 若要瞭解探索到的應用程式相依性，請參閱[View 適用於 VM 的 Azure 監視器 Map](vminsights-maps.md)。
