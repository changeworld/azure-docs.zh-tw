---
title: 使用監視器 Windows 虛擬桌面監視器預覽-Azure
description: 如何使用適用于 Windows 虛擬桌面的 Azure 監視器。
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e0be6decf28fcbb2edacd5019f567d26403b1f31
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466355"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>使用適用于 Windows 虛擬桌面的 Azure 監視器來監視您的部署 (預覽) 

>[!IMPORTANT]
>適用于 Windows 虛擬桌面的 Azure 監視器目前處於公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

適用于 Windows 虛擬桌面 (preview 的 Azure 監視器) 是以 Azure 監視器活頁簿為基礎的儀表板，可協助 IT 專業人員瞭解其 Windows 虛擬桌面環境。 本主題將逐步引導您設定 Windows 虛擬桌面的 Azure 監視器，以監視您的 Windows 虛擬桌面環境。

## <a name="requirements"></a>規格需求

開始使用適用于 Windows 虛擬桌面的 Azure 監視器之前，您必須先設定下列專案：

- 您監視的所有 Windows 虛擬桌面環境都必須以與 Azure Resource Manager 相容的最新 Windows 虛擬桌面版本為基礎。

- 至少一個已設定的 Log Analytics 工作區。

- 在您的 Log Analytics 工作區中啟用下列專案的資料收集：
    - 任何必要的效能計數器
    - 適用于 Windows 虛擬桌面的 Azure 監視器中使用的任何效能計數器或事件
    - 針對您要監視的環境中的所有物件，從診斷工具取得的資料。
    - 虛擬機器 (Vm) 在您要監視的環境中。

針對您的環境監視 Windows 虛擬桌面的 Azure 監視器的任何人，也需要下列讀取權限：

- 環境資源所在之資源群組的讀取權限。

- 資源群組的讀取權限 (s) 環境的工作階段主機所在的位置

>[!NOTE]
> [讀取] 存取權只會讓管理員查看資料。 他們需要不同的許可權，才能在 Windows 虛擬桌面入口網站中管理資源。

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>開啟適用于 Windows 虛擬桌面的 Azure 監視器

您可以使用下列其中一種方法開啟適用于 Windows 虛擬桌面的 Azure 監視器：

- 移至 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)。

- 從 Azure 入口網站中搜尋並選取 [ **Windows 虛擬桌面** ]，然後選取 [ **見解**]。

- 從 Azure 入口網站搜尋並選取 [ **Azure 監視器** ]。 選取 [**見解**] 下的 [ **insights 中樞**]，然後在 [**其他**] 選取 [ **Windows 虛擬桌面**] 下，開啟 Azure 監視器頁面中的儀表板。

當您針對 Windows 虛擬桌面開啟 Azure 監視器，請選取一或多個標示為 [ **訂** 用帳戶]、[ **資源群組**]、[ **主機集** 區] 和 [ **時間範圍** ] 的核取方塊（以您要監視的環境為基礎）。

>[!NOTE]
>Windows 虛擬桌面目前只支援監視一次訂用帳戶、資源群組和主機集區。 如果您找不到想要監視的環境，請參閱 [我們的疑難排解檔](troubleshoot-azure-monitor.md) 以取得已知的功能要求和問題。

## <a name="set-up-with-the-configuration-workbook"></a>使用設定活頁簿進行設定

如果這是您第一次開啟 Windows 虛擬桌面的 Azure 監視器，您必須為 Windows 虛擬桌面資源設定 Azure 監視器。 若要設定您的資源：

1. 在 Azure 入口網站中開啟活頁簿。
2. 選取 **[開啟** 設定活頁簿]。

設定活頁簿會設定您的監視環境，並可讓您在完成設定程式之後檢查設定。 如果儀表板中的專案未正確顯示，或當產品群組發佈需要額外資料點的更新時，請務必檢查您的設定。

## <a name="host-pool-diagnostic-settings"></a>主機集區診斷設定

您必須在支援這項功能的 Windows 虛擬桌面環境中，啟用所有物件的 Azure 監視器診斷設定。

1. 在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)開啟 Windows 虛擬桌面的 Azure 監視器，然後選取 [設定活頁 **簿**]。

2. 在 **訂** 用帳戶、 **資源群組** 和 **主機集** 區中，選取要監視的環境。

3. 在 [ **主機集區診斷設定**] 下，查看是否已啟用主機集區的 Windows 虛擬桌面診斷。 如果沒有，則會出現一則錯誤訊息，指出「找不到所選主機集區的現有診斷設定」。 
   
   應啟用下列資料表：

    - Checkpoint
    - 錯誤
    - 管理性
    - 連線
    - HostRegistration

    >[!NOTE]
    > 如果您沒有看到錯誤訊息，則不需要執行步驟4。

4. 選取 [ **設定主機集** 區]。

5. 選取 [部署]。

6. 重新整理活頁簿。

您可以深入瞭解如何在 Windows 虛擬桌面環境中的所有物件上啟用診斷，或在 [傳送 Windows 虛擬桌面診斷至 Log analytics](diagnostics-log-analytics.md)時存取 log analytics 工作區。

## <a name="configure-log-analytics"></a>設定 Log Analytics

若要開始使用適用于 Windows 虛擬桌面的 Azure 監視器，您也需要至少一個 Log Analytics 工作區，以從您打算監視的環境收集資料，並將其提供給活頁簿。 如果您已經有設定，請直接跳到 [設定效能計數器](#set-up-performance-counters)。 若要為包含您的 Windows 虛擬桌面環境的 Azure 訂用帳戶設定新的 Log Analytics 工作區，請參閱 [Azure 入口網站中的建立 Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

>[!NOTE]
>適用于 Log Analytics 的標準資料儲存體費用將會套用。 若要開始，我們建議您選擇隨用隨付模型，並隨著您的部署調整規模並採用更多資料。 若要深入瞭解，請參閱 [Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="set-up-performance-counters"></a>設定效能計數器

您必須在 Log Analytics 工作區的對應取樣間隔中，啟用集合的特定效能計數器。 這些效能計數器是您監視 Windows 虛擬桌面時所需的唯一計數器。 您可以停用所有其他專案以節省成本。

如果您已啟用效能計數器，而且想要將它移除，請依照設定 [性能](../azure-monitor/platform/data-sources-performance-counters.md) 計數器中的指示來重新設定效能計數器。 雖然本文描述如何新增計數器，但您也可以在相同的位置中將它們移除。

如果您尚未設定效能計數器，以下是如何針對 Windows 虛擬桌面的 Azure 監視器進行設定：

1. 移至 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)，然後選取視窗底部的設定活頁 **簿** 。

2. 在 [ **Log Analytics** 設定] 下，選取您為訂用帳戶設定的工作區。

3. 在 **工作區效能計數器** 中，您會看到監視所需的計數器清單。 在該清單右邊，檢查 **遺失的計數器** 清單中的專案，以啟用您開始監視工作區所需的計數器。

4. 選取 [ **設定效能計數器**]。

5. 選取 **[** 套用設定]。

6. 重新整理設定活頁簿，然後繼續設定您的環境。

您也可以在初始設定之後，在服務更新時新增效能計數器，並需要新的監視工具。 您可以在 **遺漏的計數器** 清單中選取所有必要的計數器，藉以確認它們已啟用。

>[!NOTE]
>輸入延遲效能計數器只與 Windows 10 RS5 和更新版本或 Windows Server 2019 和更新版本相容。

若要深入瞭解如何手動新增尚未針對集合啟用的效能計數器，請參閱設定 [效能計數器](../azure-monitor/platform/data-sources-performance-counters.md)。

### <a name="set-up-windows-events"></a>設定 Windows 事件

接下來，您必須在 Log Analytics 工作區中啟用要收集的特定 Windows 事件。 本節所述的事件是 Windows 虛擬桌面需求的唯一 Azure 監視器。 您可以停用所有其他專案以節省成本。

若要設定 Windows 事件：

1. 如果您已經啟用 Windows 事件並想要將它們移除，請先移除您不想要的事件，再使用設定活頁簿來啟用監視所需的設定。

2. 在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)中，移至 [Windows 虛擬桌面的 Azure 監視器]，然後選取視窗底部的 [設定活頁 **簿** ]。

3. 在 [ **Windows 事件** 設定] 中，有一份監視所需的 windows 事件清單。 清單右邊的 [ **遺失事件** ] 清單中，您會在其中找到您的工作區目前未啟用的必要事件名稱和事件種類。 記錄每個名稱以供稍後之用。

4. 選取 [ **開啟工作區** 設定]。

5. 選取 [ **資料**]。

6. 選取 [ **Windows 事件記錄** 檔]。

7. 在步驟3中新增遺漏的事件名稱，並為每個專案新增必要的事件種類。

8. 重新整理設定活頁簿，然後繼續設定您的環境。

如果監視工具更新需要啟用新的事件，您可以在初始設定之後新增 Windows 事件。 若要確定您已啟用所有必要的事件，請返回遺失的 **事件** 清單，並啟用任何您在該處看到的遺漏事件。

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>在所有主機上安裝 Log Analytics 代理程式

最後，您必須在主機集區中的所有主機上安裝 Log Analytics 代理程式，以將資料從主機傳送到選取的工作區。

若要安裝 Log Analytics 代理程式：

1. 在 [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)中，移至 [Windows 虛擬桌面的 Azure 監視器]，然後選取視窗底部的 [設定活頁 **簿** ]。

2. 如果未針對主機集區中的所有主機設定 Log Analytics，您會在 Log Analytics 設定區段底部看到錯誤訊息：「主機集區中的某些主機未將資料傳送到選取的 Log Analytics 工作區」。 選取 [ **將主機新增至工作區** ] 以新增選取的主機。 如果您沒有看到錯誤訊息，請在這裡停止。

3. 重新整理設定活頁簿。

>[!NOTE]
>必須執行主機電腦才能安裝 Log Analytics 擴充功能。 如果主機上的自動部署失敗，您一律可以手動將擴充功能安裝在主機上。 若要瞭解如何手動安裝擴充功能，請參閱 [適用于 Windows 的 Log Analytics 虛擬機器擴充](../virtual-machines/extensions/oms-windows.md)功能。

## <a name="optional-configure-alerts"></a>選用：設定警示

您可以設定 Windows 虛擬桌面的 Azure 監視器，以在您選取的訂用帳戶中發生任何嚴重 Azure 監視器警示時通知您。 若要這樣做，請遵循 [使用 Azure 監視器警示來回應事件](../azure-monitor/learn/tutorial-response.md)的指示。

## <a name="diagnostic-and-usage-data"></a>診斷和使用量資料

當您使用 Azure 監視器服務時，Microsoft 會自動收集使用量和效能資料。 Microsoft 會使用此資料來改善服務的品質、安全性和完整性。

為了提供正確且有效率的疑難排解功能，收集的資料包含入口網站會話識別碼、Azure Active Directory 使用者識別碼，以及發生事件的入口網站索引標籤名稱。 Microsoft 不會收集姓名、地址或其他連絡資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱 [Microsoft 線上服務隱私權聲明](https://privacy.microsoft.com/privacystatement)。

>[!NOTE]
>若要瞭解如何查看或刪除服務所收集的個人資料，請參閱 [GDPR 的 Azure 資料主體要求](/microsoft-365/compliance/gdpr-dsr-azure)。 如需 GDPR 的詳細資訊，請參閱 [服務信任入口網站的 GDPR 一節](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)。

## <a name="next-steps"></a>後續步驟

既然您已設定 Windows 虛擬桌面 Azure 入口網站，以下是一些可能有助於您的資源：

- 請參閱我們的 [詞彙](azure-monitor-glossary.md) ，以深入瞭解 Windows 虛擬桌面 Azure 監視器的相關術語和概念。
- 如果您遇到問題，請參閱我們的 [疑難排解指南](troubleshoot-azure-monitor.md) 以取得協助。