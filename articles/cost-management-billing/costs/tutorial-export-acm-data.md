---
title: 教學課程 - 建立及管理 Azure 成本管理的匯出資料
description: 本文示範如何建立及管理匯出的 Azure 成本管理資料，以將其用於外部系統中。
author: bandersmsft
ms.author: banders
ms.date: 05/27/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 90334d29ed2f649854863f9ad86f03811728a945
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142298"
---
# <a name="tutorial-create-and-manage-exported-data"></a>教學課程：建立和管理匯出的資料

若您已閱讀過成本分析教學課程，您便已對手動下載成本管理資料相當熟悉。 但是，您可以建立週期性工作，每天、每週或每個月自動將您的成本管理資料匯出至 Azure 儲存體。 匯出的資料格式為 CSV，其中包含所有由成本管理收集的資訊。 您接著可以在 Azure 儲存體中，搭配外部系統使用匯出的資料，並與您擁有的自訂資料合併。 您也可以在外部系統 (例如儀表板或其他財務系統) 中使用您的匯出資料。

觀看影片：[如何使用 Azure 成本管理制定匯出至儲存體的排程](https://www.youtube.com/watch?v=rWa_xI1aRzo)，為您的 Azure 成本資料建立匯出至 Azure 儲存體的排程。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

本教學課程中的範例會引導您匯出成本管理資料，並驗證該資料已成功匯出。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立每日匯出
> * 驗證已收集資料

## <a name="prerequisites"></a>必要條件
資料匯出適用於各種不同的 Azure 帳戶類型，包括 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 客戶和 [Microsoft 客戶合約](get-started-partners.md)客戶。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 針對依使用者和群組的資料匯出，每個訂用帳戶皆支援下列 Azure 權限或範圍。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 擁有者 - 可以建立、修改或刪除已排程的訂用帳戶匯出。
- 參與者 - 可以建立、修改或刪除他們自己的排程匯出。 可以修改其他人所建立的排程匯出名稱。
- 讀者 - 可以排程擁有其權限的匯出。

針對 Azure 儲存體帳戶：
- 需要寫入權限，才能變更設定的儲存體帳戶，無論匯出的權限為何。
- 您的 Azure 儲存體帳戶必須針對 Blob 或檔案儲存體進行設定。

如果您有新的訂用帳戶，您無法立即使用成本管理功能。 最多可能需要48小時的時間，才能使用所有的成本管理功能。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。

## <a name="create-a-daily-export"></a>建立每日匯出

若要建立或檢視資料匯出，或是排程匯出，請在 Azure 入口網站開啟所需的範圍，然後在功能表中選取 [成本分析]。 例如，瀏覽至 [訂用帳戶]、從清單中選取訂用帳戶，然後在功能表中選取 [成本分析]。 在 [成本分析] 頁面頂端，選取 [設定]，選取 [匯出]，然後選擇匯出選項。

> [!NOTE]
> - 除了訂用帳戶，您也可以在資源群組、帳戶、部門和註冊上建立匯出。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。
>- 當您在計費帳戶範圍或客戶的租用戶上以合作夥伴身分登入時，您可以將資料匯出至與合作夥伴儲存體帳戶連結的 Azure 儲存體帳戶。 不過，您必須在 CSP 租用戶中擁有有效的訂用帳戶。

選取 [新增]、輸入匯出的名稱，然後選取 [當月費用的每日匯出] 選項。 選取 [下一步] 。

[![顯示匯出類型的新匯出範例](./media/tutorial-export-acm-data/basics_exports.png)](./media/tutorial-export-acm-data/basics_exports.png#lightbox)

為您的 Azure 儲存體帳戶名稱指定訂用帳戶，然後選取您的儲存體帳戶。  指定您希望匯出檔案移至的儲存體容器和目錄路徑。 選取 [下一步] 。

![顯示儲存體帳戶詳細資料的新匯出範例](./media/tutorial-export-acm-data/storage_exports.png)

檢閱匯出詳細資料，然後選取 [建立]。

您新增的匯出會出現在匯出清單中。 根據預設會啟用新的匯出。 若您想要停用或刪除已排程的匯出，請選取清單中的任何項目，然後選取 [停用] 或 [刪除]。

剛開始可能會需要一到兩個小時，匯出才會開始執行。 但是，資料可能需要最多四個小時，才會出現在匯出檔案中。

### <a name="export-schedule"></a>匯出排程

排程的匯出會受您最初建立匯出時的時間和星期幾所影響。 當您建立匯出的排程時，後續的每次匯出都會以同樣的頻率執行。 例如，頻率設定為每日的月初迄今匯出，就會在每一天執行匯出。 同樣地，如果是每週匯出，就會在每週已排定的同一天執行匯出。 匯出的確切發生時間無法保證，但匯出的資料可在執行匯出後四小時內取得。
每次匯出都會建立一個新檔案，因此不會覆寫較舊的匯出。

匯出類型有兩種：

**每日匯出當月成本**– 初始匯出會立即執行。 後續匯出會在隔天與初始匯出相同的時間執行。 系統會從先前的每日匯出彙總最新資料。

**自訂** – 可讓您使用當週和當月選項來排程每週和每月匯出。 *初始匯出會立即執行。*

如果您有隨用隨付、MSDN 或 Visual Studio 訂用帳戶，則您發票的計費期間可能與行事曆月份不同。 對於這些類型的訂用帳戶和資源群組，您可以建立遵循發票期間或行事曆月份的匯出項目。 若要建立遵循發票月份的匯出項目，請瀏覽至 [自訂]，然後選取 [迄今為止的計費期間]。  若要建立遵循行事曆月份的匯出項目，請選取 [月初至今]。

![[新匯出 - 基本資料] 索引標籤，其中顯示自訂每週的當週選取項目](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

#### <a name="create-an-export-for-multiple-subscriptions"></a>建立多個訂用帳戶的匯出

如果您有 Enterprise 合約，則可以使用管理群組，將訂用帳戶成本資訊彙總在單一容器中。 接著，您可以匯出管理群組的成本管理資料。

不支援其他訂用帳戶類型的管理群組匯出。

1. 建立管理群組並將訂用帳戶指派給它。
1. 在 [匯出] 中，選取 [範圍]。
1. 選取 [選取此管理群組]。
1. 在範圍建立匯出，以取得管理群組中訂用帳戶的成本管理資料。

## <a name="verify-that-data-is-collected"></a>驗證已收集資料

您可以輕鬆驗證您的成本管理資料已收集完成，並使用 Azure 儲存體總管檢視匯出的 CSV 檔案。

在匯出清單中，選取儲存體帳戶名稱。 在儲存體帳戶頁面上，選取 [在總管中開啟]。 若您看見確認方塊，請選取 [是] 以在 Azure 儲存體總管中開啟檔案。

![顯示範例資訊和 [在總管中開啟] 連結的儲存體帳戶頁面](./media/tutorial-export-acm-data/storage-account-page.png)

在 [儲存體總管] 中，巡覽至您想要開啟的容器，然後選取對應到目前月份的資料夾。 隨即顯示 CSV 檔案清單。 選取其中一個，然後選取 [開啟]。

![[儲存體總管] 中顯示的範例訊息](./media/tutorial-export-acm-data/storage-explorer.png)

檔案隨即便會使用設為開啟 CSV 檔案副檔名的程式或應用程式開啟。 以下是 Excel 中的範例。

![Excel 中顯示的範例匯出 CSV 資料](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>下載匯出的 CSV 資料檔案

您也可以在 Azure 入口網站中下載匯出的 CSV 檔案。 下列步驟說明如何從成本分析尋找它。

1. 在 [成本分析] 中，選取 [設定]，然後選取 [匯出]。
1. 在匯出清單中，選取用於匯出的儲存體帳戶。
1. 在儲存體帳戶中，按一下 [容器]。
1. 在容器清單中，選取容器。
1. 瀏覽目錄和儲存體 Blob，前往您想要的日期。
1. 選取 CSV 檔案，然後選取 [下載]。

[![匯出下載範例](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="access-exported-data-from-other-systems"></a>從其他系統存取匯出資料

匯出成本管理資料的其中一個目的是從外部系統存取資料。 您可能會使用儀表板系統或其他財務系統。 這類系統之間的差異相當大，因此顯示範例並不實用。  但是，您可以從 [Azure 儲存體簡介](../../storage/common/storage-introduction.md)中應用程式存取您的資料開始。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立每日匯出
> * 驗證已收集資料

前進到下一個教學課程，透過識別閒置及使用量過低的資源來最佳化和改善效率。

> [!div class="nextstepaction"]
> [檢閱並針對建議採取動作](tutorial-acm-opt-recommendations.md)
