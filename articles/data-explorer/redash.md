---
title: 使用重劃線視覺化 Azure 資料資源管理器
description: 在本文中，您將瞭解如何使用 Redash 本機連接器在 Azure 資料資源管理器中視覺化資料。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773948"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>在 Redash 中視覺化來自 Azure 資料資源管理器的資料

[Redash](https://redash.io/)連接和查詢資料來源，構建儀表板以視覺化資料並與對等方共用資料。 在本文中，您將瞭解如何將 Azure 資料資源管理器設置為 Redash 的資料來源，然後視覺化資料。

## <a name="prerequisites"></a>Prerequisites

1. [創建群集和資料庫](create-cluster-database-portal.md)。
1. 在[將示例資料引入 Azure 資料資源管理器中](ingest-sample-data.md)所述的引入資料。 有關更多引入選項，請參閱[攝入概述](ingest-data-overview.md)。

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>在 Redash 中創建 Azure 資料資源管理器連接器 

1. 登錄到[Redash。](https://www.redash.io/) 選擇 **"開始"** 以創建帳戶。
1. 在 **"讓我們開始**"下，選擇"**連接資料來源**"。

    ![連線資料來源](media/redash/connect-data-source.png)

1. 在 **"創建新資料來源"** 視窗中，選擇**Azure 資料資源管理器（庫托），** 然後選擇 **"創建**"。 

    ![選擇 Azure 資料資源管理器資料來源](media/redash/select-adx-data-source.png)

1. 在**Azure 資料資源管理器（Kusto）** 視窗中，完成以下表單並選擇 **"創建**"。

    ![Azure 資料資源管理器（庫托）設置視窗](media/redash/adx-settings-window.png)

1. 在 **"設置"** 視窗中，選擇 **"保存**"和"**測試連接**"以測試**Azure 資料資源管理器 （Kusto）** 資料來源連接。

## <a name="create-queries-in-redash"></a>在 Redash 中創建查詢

1. 在 Redash 的左上角，選擇 **"創建** > **查詢**"。 按一下 **"新建查詢**"並重命名查詢。

    ![建立查詢](media/redash/create-query.png)

1. 在頂部編輯窗格中鍵入查詢，然後選擇 **"保存****和執行**"。 選擇 **"發佈**"以發佈查詢以供將來使用。

    ![保存和執行查詢](media/redash/save-and-execute-query.png)

    在左側窗格中，您可以在下拉式功能表中看到資料來源連接名稱（流中的**Github 連接器**）和所選資料庫中的表。 

1. 在底部中心窗格中查看查詢結果。 通過選擇 **"新建視覺化"** 按鈕，創建與查詢配合使用的視覺化效果。

    ![新視覺化](media/redash/new-visualization.png)

1. 在視覺化螢幕中，選擇**視覺化類型**和相關欄位，如 X**列**和**Y 列**。 **保存**視覺化效果。

    ![配置和保存視覺化效果](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>使用參數創建查詢

1. **創建** > **查詢**以創建新查詢。 使用 [ ]{}捲曲括弧向其添加參數。 選擇**{}[ ]** 以打開 **"添加參數"** 視窗。 您還可以選擇*設置圖示*來修改現有參數的屬性，並打開 **<parameter_name>** 視窗。 

    ![插入參數](media/redash/insert-parameter.png)

1. 具名引數。 選擇 **"類型**：**從下拉式功能表中選擇基於查詢的下拉清單**"。 選取 [確定]****

    ![基於查詢的下拉清單](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > 查詢使用多個值，因此必須包含以下語法`| where Type in ((split('{{Type}}', ',')))`。 有關詳細資訊，請參閱[運算子](/azure/kusto/query/inoperator)。 這將導致[重劃線應用中有多個查詢參數選項](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>在 Redash 中創建儀表板

1. 要創建儀表板，**請創建** > **儀表板**。 或者，選擇現有儀表板，**儀表板**>從清單中選擇儀表板。

    ![建立儀表板](media/redash/create-dashboard.png)

1. 在 **"新建儀表板"** 視窗中，命名儀表板並選擇"**保存**"。 在 **<Dashboard_name>** 視窗中，選擇 **"添加小部件**"以創建新的小部件。 

1. 在 **"添加小部件"** 視窗中，選取查詢名稱、**選擇視覺化**和**參數**。 選擇 **"添加到儀表板"**

   ![選擇視覺化效果並添加到儀表板](media/redash/add-widget-window.png)

1. 選擇 **"完成編輯**"以完成儀表板創建。

1.  在儀表板編輯模式下，選擇 **"使用儀表板級別篩選器**"以使用以前定義的 **"類型"** 參數。

    ![完成儀表板創建](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>後續步驟

* [為 Azure 資料資源管理器編寫查詢](write-queries.md)


