---
title: 使用 Backup Explorer 監視您的備份
description: 這篇文章說明如何使用 Backup Explorer 來執行跨保存庫、訂用帳戶、區域和租使用者的備份即時監視
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992179"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>使用 Backup Explorer 監視您的備份

當組織將越來越多的機器備份到雲端時，必須要有一個中央位置來查看有關整個大型資產的備份操作資訊，以有效率地監視備份。

Backup Explorer 活頁簿是內建的 Azure 監視器活頁簿，可讓備份客戶擁有單一窗格，以執行與 Azure 上整個備份資產（跨租使用者、位置、訂用帳戶、資源群組和保存庫，全都來自中央位置。 大致上，它提供下列功能：

* **大規模的觀點**-匯總的備份專案、作業、警示、原則，以及未設定在整個備份資產進行備份的資源。 
* **向下切入分析**–您可以選擇從單一位置取得每個實體的詳細資訊–作業、警示、原則和備份專案。
* 可**操作的介面**–識別問題之後，您可以選擇順暢地流覽至備份專案或 Azure 資源來執行動作。

上述功能是現成的，與 Azure Resource Graph 和 Azure 監視器活頁簿的原生整合提供。

> [!NOTE]
> * 備份瀏覽器目前僅適用于 Azure VM 資料。
> * Backup Explorer 是一種作業儀表板，可用於在過去7天（最大）上查看備份的相關資訊。
> * 目前不支援自訂備份瀏覽器範本。 此外，我們目前不建議您撰寫 Azure Resource Graph 資料的自訂自動化。

## <a name="getting-started"></a>開始使用

您可以流覽至任何復原服務保存庫，然後按一下 [**總覽**] 頁面中的 [**備份瀏覽器**] 連結，以存取備份瀏覽器。

![保存庫快速連結](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

按一下此連結會開啟 [備份瀏覽器]，它會在您擁有存取權的所有保存庫和訂用帳戶中提供匯總的視圖。 如果您使用 Azure 燈塔帳戶，您可以在您可存取的所有租使用者上查看資料（如需詳細資訊，請參閱下一節的跨租使用者視圖）。

![Explorer 登陸頁面](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>備份瀏覽器使用案例

Backup Explorer 是由多個索引標籤所組成，每個索引標籤提供特定類型備份成品的詳細資訊，例如備份專案、作業、原則等。本節提供每個索引標籤的簡短總覽。 這些影片提供每個索引標籤的範例使用案例，以及可供使用者使用的各種控制項的描述。

### <a name="summary"></a>摘要

[摘要] 索引標籤可讓您快速概覽備份資產的整體狀況。 您可以查看如受保護的專案數、尚未啟用保護的專案數、過去24小時內成功的工作數目等等的資訊。 

每個其他索引標籤都代表不同的實體–例如：備份專案、備份作業、備份警示和備份原則。 您也可以查看尚未設定備份之電腦的相關資訊。 按一下任何一個索引標籤，就會顯示該實體的特定資訊。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>備份項目

您可以依訂用帳戶、保存庫和其他參數來查看您所篩選的每個備份專案。 按一下備份專案的名稱，可讓您開啟該備份專案的 Azure 分頁。 例如，在資料表中，您可能會發現專案 ' X ' 的上次備份失敗。 按一下 [X] 就會開啟此專案的 [備份] 分頁，您可以在其中觸發隨選備份作業。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>工作

您可以藉由流覽至 [工作] 索引標籤，來查看過去七天所觸發之所有工作的詳細資料。在這裡，您可以依工作作業、作業狀態和錯誤碼（如果是失敗的作業）進行篩選。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>警示

您可以按一下 [警示] 索引標籤，以查看您的保存庫在過去七天內引發之所有警示的詳細資料。在這裡，您可以依警示類型（例如，備份失敗、還原失敗）、警示的目前狀態（例如，[作用中] 或 [已解決]）和警示嚴重性（例如，[重大]、[警告]、[資訊]）來篩選記錄。 您也可以按一下 VM 的連結以流覽至 Azure VM，並採取必要的動作。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>原則

按一下 [原則] 索引標籤可讓您在備份資產中，查看所有已建立之備份原則的重要資訊。 您可以查看每個原則有多少個相關聯的專案，以及每個原則所指定的保留範圍和備份頻率。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>未啟用備份

組織的備份管理員必須快速識別組織中的哪些機器尚未啟用備份，才能針對需要保護的所有電腦啟用備份。」是很重要的。 按一下 [**未啟用的備份**] 索引標籤可協助您進行這項工作。

在此索引標籤中，您會看到一個資料表，其中包含未受保護的專案清單。 如果您的組織遵循將不同標籤指派給生產機器和測試電腦的作法，或是服務不同功能的機器，而每個都可能需要個別的備份原則，則依標籤篩選可協助您查看特定的資訊特定類別的機器。 按一下任何專案的名稱，就會將您重新導向至該專案的 [**設定備份**] 分頁，您可以在其中選擇使用適當的備份原則來備份該專案。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>匯出至 Excel

按一下任何 widget （資料表/圖表）右上方的向下箭號按鈕，就會將該 widget 的內容匯出為 Excel 工作表，如同已套用的現有篩選。 若要將資料表的更多資料列匯出至 Excel，您可以使用每個索引標籤頂端的 [**每頁**的資料列] 下拉式清單，增加頁面上顯示的資料列數目。

## <a name="pinning-to-dashboard"></a>釘選到儀表板

您可以按一下每個 widget 頂端的釘選圖示，將該 widget 釘選到您的 Azure 入口網站儀表板。 這可協助您建立量身打造的自訂儀表板，以顯示您所需的最重要資訊。

## <a name="cross-tenant-views"></a>跨租使用者的視圖

如果您是具有跨多個租使用者環境之訂用帳戶的委派存取權的 Azure 燈塔使用者，您可以使用預設的訂用帳戶篩選（按一下 Azure 入口網站右上方的篩選圖示）來選取您想要的所有訂閱請參閱的資料。 這麼做可讓 Backup Explorer 匯總這些訂用帳戶中所有保存庫的資訊。 [在這裡](https://docs.microsoft.com/azure/lighthouse/overview)深入瞭解 Azure 燈塔。

## <a name="next-steps"></a>後續步驟

[瞭解如何使用 Azure 監視器來取得備份資料的深入解析](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)