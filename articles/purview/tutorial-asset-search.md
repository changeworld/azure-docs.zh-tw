---
title: 教學課程：瀏覽 Azure Purview 首頁並搜尋資產
description: 本教學課程說明如何使用 Azure Purview 首頁上的功能及如何在目錄中搜尋。
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 58a899d234488e8075764db9f722ff21e1d0a6f7
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549701"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>教學課程：瀏覽 Azure Purview (預覽) 首頁並搜尋資產

> [!IMPORTANT]
> Azure Purview 目前為預覽狀態。 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含適用於 Azure 功能 (搶鮮版 (Beta)、預覽版，或尚未發行的版本) 的其他法律條款。

在本教學課程中，您會藉由瀏覽首頁功能及在目錄中搜尋資產來熟悉 Azure Purview。

這篇文章是「五部分教學課程系列的第 2 部分」，您將了解使用 Azure Purview 來管理跨資料資產的資料治理基本概念。 本教學課程建基於您在[第 1 部分：使用 Azure Purview 掃描資料](tutorial-scan-data.md)

在本教學課程中，您將：

> [!div class="checklist"]
>
> * 瀏覽 Azure Purview 首頁。
> * 搜尋資產。
> * 新增資產擁有者。

## <a name="prerequisites"></a>必要條件

* 完成[教學課程：使用 Azure Purview 掃描資料](tutorial-scan-data.md)

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="navigate-the-azure-purview-home-page"></a>瀏覽 Azure Purview 首頁

下列步驟會逐步引導您瀏覽 Azure Purview 首頁。

1. 在 Azure 入口網站中瀏覽至您的 Azure Purview 資源，然後選取 [開啟 Purview Studio]。 您會自動進入 Purview Studio 的首頁。

   首頁頂端會顯示您目錄的名稱和一組目錄分析。 其中包括使用者、資料資產和詞彙的數目。 在摘要中，您可以看到共有 200 多個資產和 113 個詞彙。 當您的組織進行掃描並將更多詞彙新增至 Azure Purview 時，此數字也會隨之更新。

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="顯示 Azure Purview 首頁的螢幕擷取畫面。":::

1. 選取 [瀏覽資產] 以查看您所有的資產。

## <a name="search-for-an-asset"></a>搜尋資產

在開始之前，以下是一些基本術語的快速複習：

* **資產**：目錄中的單一物件，其簡明扼要且包含資料資產中任何實體的定義。 實體的範例包括 SQL 資料表、Power BI 報表，以及您的資料處理站活動。
  
* **結構描述**：也稱為資料行或屬性，結構描述代表資產或資源集的結構。

* **資源集**：目錄中的單一物件，代表儲存體中的許多實體物件。 這些物件通常會共用一個通用的結構描述，大部分情況下是命名慣例或資料夾結構。 例如，日期格式為 *yyyy/mm/dd*。 如需詳細資訊，請參閱[了解資源集](concept-resource-sets.md)。

* **資產類型**：邏輯名稱底下的資產和資源集群組，通常會對應至資料平台的名稱。

請遵循下列步驟來搜尋資產，並將自已標示為擁有者：

1. 在您首頁的 [搜尋目錄] 方塊中，輸入包含您資料資產的資源群組名稱 (您在上一個教學課程中建立的資源群組)。 建議清單會隨即出現。

1. 選取 [檢視搜尋結果]。 因為所有資產都是以您資源群組名稱開頭，所以全都會出現在搜尋結果中。 在本教學課程之外，您將會搜尋特定的資產名稱，而不是資源群組。

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="顯示目錄搜尋建議清單的螢幕擷取畫面。":::

1. 您可以使用左側導覽中的篩選，依據資產類型、分類、連絡人、標籤和詞彙來進行篩選。

1. 若要搜尋資源集，請開始輸入集合的名稱。 包含正確關鍵字的搜尋建議清單會隨即出現。 您也可以藉由將字詞放在引號中來搜尋完全相符的名稱。

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Azure Purview 關鍵字資產搜尋":::

## <a name="edit-an-asset"></a>編輯資產

1. 從您的搜尋結果中選取其中一個資產。 然後，選取 [編輯]

1. 在 [概觀] 索引標籤上，您可以新增描述。

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="顯示 [概觀] 索引標籤上 [描述] 欄位的螢幕擷取畫面。":::

1. 選取 [連絡人] 索引標籤。在 [擁有者] 旁邊的 [選取使用者或群組] 中，開始輸入您公司的電子郵件地址。

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="顯示已填入欄位的螢幕擷取畫面。":::

    系統會自動顯示名稱建議。

1. 在 [專家] 旁邊的 [選取使用者或群組] 中，輸入名稱 (例如，您經理的名稱)，然後選取 [儲存]。

    現在，[描述]、[擁有者名稱] 和 [專家名稱] 欄位都已填入。

## <a name="next-steps"></a>後續步驟

在您繼續進行本系列的下一個教學課程之前，請自行花更多時間來探索 Azure Purview 首頁。 在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 瀏覽 Azure Purview 首頁。
> * 搜尋資產。
> * 新增資產擁有者。

繼續進行下一個教學課程，以了解如何在 Azure Purview 中瀏覽資產，並探索資產譜系。

> [!div class="nextstepaction"]
> [瀏覽資產和檢視其譜系](tutorial-browse-and-view-lineage.md)
