---
title: 使用 Backup Explorer 監視您的備份
description: 本文說明如何使用 Backup Explorer 來執行跨保存庫、訂用帳戶、區域和租使用者的備份即時監視。
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: b65f68e33b53dff341ee72f6b9e9f42e344c49b1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149570"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>使用 Backup Explorer 監視您的備份

當組織將越來越多的機器備份到雲端時，有效率地監視這些備份會變得越來越重要。 開始的最佳方式是使用中央位置來跨大型資產查看操作資訊。

Backup Explorer 是內建的 Azure 監視器活頁簿，可為 Azure 備份客戶提供這個單一的集中位置。 Backup Explorer 可協助您監視 Azure 上整個備份資產的作業活動、跨租使用者、位置、訂用帳戶、資源群組和保存庫。 備份瀏覽器廣泛地提供下列功能：

* **大規模的觀點**：取得備份專案、作業、警示、原則和資源的匯總視圖，這些工作尚未設定為跨整個資產進行備份。 
* **向下切入分析**：顯示每個作業、警示、原則和備份專案的詳細資訊，全都放在同一個位置。
* 可**操作的介面**：在您找出問題之後，您可以順暢地前往相關的備份專案或 Azure 資源來解決問題。

這些功能預設是由 Azure Resource Graph 和 Azure 監視器活頁簿的原生整合提供。

> [!NOTE]
> * Backup Explorer 目前僅適用于 Azure 虛擬機器（Vm）資料。
> * Backup Explorer 是一種作業儀表板，可用於在過去7天（最大）上查看備份的相關資訊。
> * 目前不支援自訂 Backup Explorer 範本。 
> * 我們不建議您撰寫 Azure Resource Graph 資料的自訂自動化。

## <a name="get-started"></a>開始使用

您可以前往任何復原服務保存庫，然後選取 [**總覽**] 窗格中的 [**備份瀏覽器**] 連結，以存取備份瀏覽器。

![保存庫快速連結](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

選取此連結會開啟 [備份瀏覽器]，這會在您擁有存取權的所有保存庫和訂用帳戶中提供匯總的視圖。 如果您使用 Azure 燈塔帳戶，您可以在您擁有存取權的所有租使用者之間查看資料。 如需詳細資訊，請參閱本文結尾的「跨租使用者的觀點」一節。

![Backup Explorer 登陸頁面](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>備份瀏覽器使用案例

Backup Explorer 會顯示多個索引標籤，每個索引標籤會提供特定備份成品的詳細資訊（例如，備份專案、作業或原則）。 本節提供每個索引標籤的簡短總覽。 這些影片提供每個備份成品的範例使用案例，以及可用控制項的描述。

### <a name="the-summary-tab"></a>[摘要] 索引標籤

[**摘要**] 索引標籤可讓您快速概覽備份資產的整體狀況。 例如，您可以查看受保護的專案數、尚未啟用保護的專案數，或過去24小時內成功的工作數目。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>[備份專案] 索引標籤

您可以依訂用帳戶、保存庫和其他特性來篩選和查看每個備份專案。 藉由選取備份專案的名稱，您可以開啟該專案的 [Azure] 窗格。 例如，在資料表中，您可能會發現專案*X*的上次備份失敗。藉由選取 [ *X*]，您可以開啟專案的 [**備份**] 窗格，您可以在其中觸發隨選備份作業。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>[工作] 索引標籤

選取 [**工作**] 索引標籤，以查看過去7天觸發的所有工作的詳細資料。 在這裡，您可以依*工作作業*、*作業狀態*和*錯誤碼*進行篩選（適用于失敗的工作）。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>[警示] 索引標籤

選取 [**警示**] 索引標籤，以查看您過去7天在保存庫上產生之所有警示的詳細資料。 您可以依類型（*備份失敗*或*還原失敗*）、目前狀態（作用中*或* *已解決*）和嚴重性（*重大*、*警告*或*資訊*）來篩選警示。 您也可以選取連結來移至 Azure VM，並採取任何必要的動作。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>[原則] 索引標籤

您可以選取 [**原則**] 索引標籤，以查看已在備份資產中建立之所有備份原則的重要資訊。 您可以查看與每個原則相關聯的專案數，以及原則所指定的保留範圍和備份頻率。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>[未啟用備份] 索引標籤

所有需要保護的電腦都應該啟用備份。 使用 Backup Explorer，備份管理員可以快速識別組織中的哪些機器尚未受到備份保護。 若要查看此資訊，請選取 [**未啟用備份**] 索引標籤。

[**未啟用備份**] 窗格會顯示一個資料表，其中包含未受保護的機器清單。 您的組織可能會將不同的標籤指派給生產機器和測試機器，或是提供給服務各種功能的機器。 因為每個類別的電腦都需要個別的備份原則，所以依標籤篩選可協助您查看每一個的特定資訊。 選取任何機器的名稱，就會將您重新導向至該機器的 [**設定備份**] 窗格，您可以在其中選擇套用適當的備份原則。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>匯出至 Excel

您可以將任何資料表或圖表的內容匯出為 Excel 試算表。 內容會依照自己的方式匯出，並套用現有的篩選器。 若要匯出其他資料表資料列，您可以使用每個索引標籤頂端的 [**每頁**的資料列] 下拉式清單，增加要在頁面上顯示的資料列數目。

## <a name="pin-to-the-dashboard"></a>釘選到儀表板

您可以選取每個資料表或圖表頂端的 [釘選] 圖示，將它釘選到您的 Azure 入口網站儀表板。 釘選這項資訊可協助您建立自訂的儀表板，以顯示對您而言最重要的資訊。

## <a name="cross-tenant-views"></a>跨租使用者的視圖

如果您是具有跨多個租使用者環境之訂用帳戶的委派存取權的 Azure 燈塔使用者，您可以使用預設的訂用帳戶篩選。 您可以選取 Azure 入口網站右上方的 [篩選] 圖示，以顯示您想要查看資料的訂用帳戶。 當您使用這項功能時，Backup Explorer 會匯總所選訂用帳戶中所有保存庫的相關資訊。 若要深入瞭解，請參閱[什麼是 Azure 燈塔？](https://docs.microsoft.com/azure/lighthouse/overview)。

## <a name="next-steps"></a>後續步驟

[瞭解如何使用 Azure 監視器來取得備份資料的深入解析](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
