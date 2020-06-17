---
title: 使用備份總管監視您的備份
description: 本文說明如何使用備份總管執行跨保存庫、訂用帳戶、區域和租用戶的即時備份監視。
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e891ee1ccfbe929aaa8ac35518b40f5514da714f
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715200"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>使用備份總管監視您的備份

當組織將越來越多的機器備份到雲端時，有效監視這些備份的重要性也會隨之提高。 為此，最適當的首要步驟是使用集中位置來檢視整個大型資產的作業資訊。

備份總管是內建的 Azure 監視器活頁簿，可為 Azure 備份客戶提供這種單一的集中位置。 備份總管可協助您跨租用戶、位置、訂用帳戶、資源群組和保存庫，監視整個備份資產在 Azure 上的作業活動。 大致來說，備份總管提供下列功能：

* **大規模的觀點**：對尚未針對整個資產設定備份的備份項目、作業、警示、原則和資源取得彙總檢視。
* **向下切入分析**：在同一個位置顯示每個作業、警示、原則和備份項目的詳細資訊。
* **可操作的介面**：找出問題後，您可以順暢地移至相關的備份項目或 Azure 資源來解決問題。

這些功能是由 Azure Resource Graph 和 Azure 監視器活頁簿的原生整合所提供的預設功能。

> [!NOTE]
>
> * 備份總管目前僅適用於 Azure 虛擬機器 (VM) 資料。
> * 備份總管可作為作業儀表板，用來檢視過去 7 天 (最大值) 內的備份相關資訊。
> * 國家雲端目前不支援備份總管。
> * 目前不支援自訂備份總管範本。
> * 我們不建議您撰寫 Azure Resource Graph 資料的自訂自動化。
> * 目前，備份總管可讓您監視最多 1000 個訂用帳戶 (跨租用戶) 的備份。

## <a name="get-started"></a>開始使用

您可以移至任何復原服務保存庫，並選取 [概觀] 窗格中的 [備份總管] 連結，以存取備份總管。

![保存庫快速連結](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

選取此連結會開啟備份總管，讓您以彙總的方式檢視您有權存取的所有保存庫和訂用帳戶。 如果您使用 Azure Lighthouse 帳戶，則可以在您有權存取的所有租用戶間檢視資料。 如需詳細資訊，請參閱本文最後的「跨租用戶檢視」一節。

![備份總管登陸頁面](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>備份總管使用案例

備份總管會顯示多個索引標籤，分別提供關於特定備份成品的詳細資訊 (例如備份項目、作業或原則)。 本節將提供每個索引標籤的簡短概觀。 這些影片會提供每個備份成品的範例使用案例，以及可用控制項的描述。

### <a name="the-summary-tab"></a>摘要索引標籤

[摘要] 索引標籤可讓您快速概覽備份資產的整體狀況。 例如，您可以檢視受保護的項目數、尚未啟用保護的項目數，或過去 24 小時內成功的作業數目。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>備份項目索引標籤

您可以依訂用帳戶、保存庫和其他特性來篩選和檢視每個備份項目。 選取備份項目的名稱，即可開啟該項目的 Azure 窗格。 例如，在資料表中，您可能會發現項目 *X* 上次的備份失敗。選取 *X* 即可開啟該項目的 [備份] 窗格，且您可以在其中觸發隨選備份作業。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>作業索引標籤

選取 [作業] 索引標籤，可檢視過去 7 天內觸發之所有作業的詳細資料。 在此處，您可以依 [作業操作]、[作業狀態] 和 [錯誤碼] (適用於失敗的作業) 進行篩選。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>警示索引標籤

選取 [警示] 索引標籤，可檢視過去 7 天內在您的保存庫上產生之所有警示的詳細資料。 您可以依類型 ([備份失敗] 或 [還原失敗])、目前的狀態 ([作用中] 或 [已解決]) 和嚴重性 ([重要]、[警告] 或 [資訊]) 來篩選警示。 您也可以選取連結以移至 Azure VM，並採取任何必要的動作。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>原則索引標籤

您可以選取 [原則] 索引標籤，以檢視已在備份資產中建立之所有備份原則的相關重要資訊。 您可以檢視與每個原則相關聯的項目數，以及原則所指定的保留範圍和備份頻率。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>未啟用備份索引標籤

所有需要保護的機器均應啟用備份。 透過備份總管，備份管理員可快速識別組織中有哪些機器尚未受到備份的保護。 若要檢視這項資訊，請選取 [未啟用備份] 索引標籤。

[未啟用備份] 窗格會顯示一個表格，其中列出未受保護的機器。 您的組織可能會為生產機器和測試機器指派不同的標籤，或是為提供各種功能的機器指派不同的標籤。 由於各種機器需要個別的備份原則，依標籤篩選將可協助您檢視各個機器特有的資訊。 選取任何機器的名稱，即會重新導向至該機器的 [設定備份] 窗格，您可以在其中選擇要套用適當的備份原則。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>匯出至 Excel

您可以將任何資料表或圖表的內容匯出為 Excel 試算表。 內容會依原狀匯出，並套用您現有的篩選器。 若要匯出其他資料表資料列，您可以使用每個索引標籤頂端的 [每頁的資料列數] 下拉式清單，增加要顯示在頁面上的資料列數目。

## <a name="pin-to-the-dashboard"></a>釘選到儀表板

您可以選取每個資料表或圖表頂端的「圖釘」圖示，將其釘選到您的 Azure 入口網站儀表板。 釘選這項資訊可協助您建立自訂的儀表板，以專門顯示對您而言最重要的資訊。

## <a name="cross-tenant-views"></a>跨租用戶檢視

如果您是 Azure Lighthouse 使用者，且具有委派的權限可存取多個租用戶環境間的訂用帳戶，您可以使用預設的訂用帳戶篩選器。 您可以選取 Azure 入口網站右上方的「篩選器」圖示，以顯示您要查看資料的訂用帳戶。 當您使用這項功能時，備份總管會彙總您所選訂用帳戶間所有保存庫的相關資訊。 若要深入了解，請參閱[什麼是 Azure Lighthouse？](https://docs.microsoft.com/azure/lighthouse/overview)。

## <a name="next-steps"></a>後續步驟

[了解如何使用 Azure 監視器取得備份資料的深入解析](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
