---
title: 使用備份資源管理器監視備份
description: 本文介紹如何使用備份資源管理器對跨保存庫、訂閱、區域和租戶的備份執行即時監視。
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131799"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>使用備份資源管理器監視備份

隨著組織將越來越多的電腦備份到雲中，高效監視這些備份變得越來越重要。 最好的開始方法是使用中心位置查看大型莊園中的操作資訊。

備份資源管理器是一個內置的 Azure 監視器活頁簿，它為 Azure 備份客戶提供此單個中心位置。 備份資源管理器可説明您監視 Azure 上整個備份莊園的操作活動，包括租戶、位置、訂閱、資源組和保存庫。 廣義而言，備份資源管理器提供以下功能：

* **規模透視**：獲取尚未為整個莊園的備份項、作業、警報、策略和資源配置的備份項、作業、警報、策略和資源的彙總檢視。 
* **向下切入分析**：在一個位置顯示有關每個作業、警報、策略和備份項的詳細資訊。
* **可操作介面**：確定問題後，可以通過無縫地訪問相關的備份項或 Azure 資源來解決它。

這些功能通過與 Azure 資源圖和 Azure 監視器活頁簿的本機集成提供開箱即用。

> [!NOTE]
> * 備份資源管理器當前僅適用于 Azure 虛擬機器 （VM） 資料。
> * 備份資源管理器旨在作為一個操作儀表板，用於查看過去 7 天（最大）備份的資訊。
> * 備份資源管理器當前不受國家雲的支援。
> * 目前，不支援自訂備份資源管理器範本。 
> * 我們不建議在 Azure 資源圖資料上編寫自訂自動化。

## <a name="get-started"></a>開始使用

您可以通過訪問任何恢復服務保存庫並在 **"概述"** 窗格中選擇 **"備份資源管理器"** 連結來訪問備份資源管理器。

![保險庫快速連結](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

選擇連結將打開"備份資源管理器"，該資源管理器可在您有權訪問的所有保存庫和訂閱之間提供彙總檢視。 如果使用 Azure 燈塔帳戶，則可以查看有權訪問的所有租戶的資料。 有關詳細資訊，請參閱本文末尾的"交叉租戶視圖"部分。

![備份資源管理器著陸頁](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>備份資源管理器用例

備份資源管理器顯示多個選項卡，每個選項卡提供有關特定備份專案的詳細資訊（例如，備份項、作業或策略）。 本節簡要概述了每個選項卡。 這些視頻為每個備份工件提供了示例用例，以及可用控制項的說明。

### <a name="the-summary-tab"></a>"摘要"選項卡

"**摘要"** 選項卡可快速流覽備份區的總體狀況。 例如，您可以查看受保護的項數、尚未啟用保護的專案數或過去 24 小時內成功訪問的作業數。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>"備份專案"選項卡

您可以按訂閱、保存庫和其他特徵篩選和查看每個備份專案。 通過選擇備份項的名稱，可以打開該項的 Azure 窗格。 例如，在表中，您可能會注意到專案*X*的最後一次備份失敗。通過選擇*X*，可以打開專案的 **"備份"** 窗格，您可以在其中觸發按需備份操作。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>"作業"選項卡

選擇 **"作業"** 選項卡以查看過去 7 天內觸發的所有作業的詳細資訊。 在這裡，您可以按*作業操作*、*作業狀態*和*錯誤代碼*（對於失敗的作業）進行篩選。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>"警報"選項卡

選擇 **"警報"** 選項卡可查看過去 7 天內在保存庫上生成的所有警報的詳細資訊。 您可以按類型（*備份失敗*或*還原失敗*）、目前狀態（*活動*或*已解決*）和嚴重性（*嚴重*、*警告*或*資訊*）篩選警報。 您還可以選擇一個連結以轉到 Azure VM 並採取任何必要的操作。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>"策略"選項卡

您可以選擇 **"策略"** 選項卡以查看有關在備份區中創建的所有備份策略的關鍵資訊。 您可以查看與每個策略關聯的項數，以及策略指定的保留範圍和備份頻率。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>"未啟用備份"選項卡

應為所有需要保護的電腦啟用備份。 使用備份資源管理器，備份管理員可以快速識別組織中哪些電腦尚未受到備份的保護。 要查看此資訊，請選擇"**未啟用備份**"選項卡。

"**未啟用備份"** 窗格顯示一個表，其中列出了未受保護的電腦。 您的組織可能會為生產電腦和測試電腦或服務各種功能的電腦分配不同的標記。 由於每類電腦都需要單獨的備份策略，因此按標記進行篩選可説明您查看特定于每個類別的資訊。 選擇任何電腦的名稱會將您重定向到該電腦的 **"配置備份"** 窗格，您可以選擇應用適當的備份策略。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>匯出至 Excel

您可以將任何表或圖表的內容匯出為 Excel 試算表。 內容按現在匯出，並應用現有篩選器。 要匯出其他表行，可以使用每個選項卡頂部的 **"每頁行**"下拉清單增加要在頁面上顯示的行數。

## <a name="pin-to-the-dashboard"></a>固定到儀表板

您可以選擇每個表或圖表頂部的"pin"圖示，將其固定到 Azure 門戶儀表板。 固定此資訊可説明您創建自訂儀表板，該儀表板可定制以顯示對您最重要的資訊。

## <a name="cross-tenant-views"></a>交叉租戶視圖

如果您是 Azure 燈塔使用者，具有對跨多個租戶環境的訂閱的委派存取權限，則可以使用預設訂閱篩選器。 通過選擇 Azure 門戶右上角的"篩選器"圖示，顯示要查看資料的訂閱。 使用此功能時，備份資源管理器會聚合有關所選訂閱中所有保存庫的資訊。 要瞭解更多資訊，請參閱[什麼是 Azure 燈塔？](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="next-steps"></a>後續步驟

[瞭解如何使用 Azure 監視器獲取備份資料的見解](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
