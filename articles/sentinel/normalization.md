---
title: Azure Sentinel 中的資料正規化 |Microsoft Docs
description: 本文說明 Azure Sentinel 如何從許多不同的來源標準化資料，並詳細說明正規化架構。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 0c6129a24e6ed083114971df5f254eca54924400
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934582"
---
# <a name="normalization-in-azure-sentinel"></a>Azure Sentinel 中的正規化

本文說明 Azure Sentinel 中的資料架構正規化，以及包含連結的網路連接和會話架構。

## <a name="what-is-normalization"></a>什麼是正規化

一起使用各種資料類型和資料表會帶來挑戰。 您必須熟悉許多不同的資料類型和架構，而必須針對每個資料類型和架構撰寫和使用一組唯一的分析規則、活頁簿和搜尋查詢，甚至是共用共通性 (例如，與防火牆裝置) 相關。 調查和搜尋所需的不同資料類型之間的相互關聯也很困難。 Azure Sentinel 可提供順暢的體驗，讓您在統一的正規化視圖中處理來自各種來源的資料。

Azure Sentinel 的 **通用訊息模型** 包含三個層面：

- **正規化架構** 涵蓋了一組可預測的可預測事件種類 (資料表) 很容易使用，並可在 (上建立整合的功能，例如網路資料表) 。 此架構也包含正規化資料行慣例，以及值和格式標準化的定義 (標準一致的資料標記法，例如 IP 位址) 。

- 剖析器會將不同類型的現有**資料對應至**正規化架構。 根據模型，您可以使用) 或內嵌時間的函式，將資料剖析為查詢時間中的正規化架構 (。 目前只支援查詢階段剖析。

- **每個正規化架構的內容** 包含分析規則、互動式活頁簿、搜尋查詢和其他內容。

### <a name="current-release"></a>目前的版本

在此版本中， [標準化的網路連接和會話架構](./normalization-schema.md) (1.0.0) 可供公開預覽。 架構會描述網路連線或會話，例如防火牆、網路資料、NSG、Netflow、proxy 系統和 web 安全性閘道所記錄的會話。  選取的查詢時間剖析器和分析規則可與架構搭配使用，並利用它。

架構目前只能透過查詢時間剖析器使用 (請參閱剖析器區段) 。

您可以將資料剖析為其他表示，並使用 [OSSEM 實體命名模型](https://ossemproject.com/cdm/entities/intro.html#) 來建立將與現有和未來正規化資料表一致的資料行。

## <a name="normalized-schema-and-parsing"></a>正規化架構和剖析

### <a name="how-our-normalized-schemas-are-defined"></a>如何定義正規化架構

Azure Sentinel 與 [開放原始碼安全性事件中繼資料 (OSSEM) ](https://ossemproject.com/intro.html) 的通用訊息模型一致，可讓您跨標準化資料表進行可預測的實體相互關聯。 OSSEM 是一個以技術為導向的專案，主要著重于從不同資料來源和作業系統進行安全性事件記錄檔的檔和標準化。 此外，專案還提供 (CIM) 的通用訊息模型，可在資料正規化程式期間用於資料工程師，讓安全性分析師跨不同的資料來源查詢及分析資料。

[OSSEM CIM](https://ossemproject.com/cdm/intro.html)會定義一組實體 (例如：檔案、主機、IP、進程) ，並定義每個這類實體的一組屬性。 此外，CIM 會定義一組資料表 (例如，使用這些實體中相關屬性的 [網路會話](https://ossemproject.com/cdm/tables/network_session.html) 資料表) ，允許無縫且可預測的相互關聯。 請注意，實體可以是嵌套的 (例如，來源實體可包含的檔案實體會有名稱屬性) 。

若要深入瞭解 OSSEM 實體結構，請造訪 [官方 OSSEM 參考](https://ossemproject.com/cdm/guidelines/entity_structure.html)。

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>正規化架構在 Azure Sentinel 中的執行方式

在 Azure Sentinel 的 OSSEM CIM 的執行中，我們會將 OSSEM 標記法投影至 Log Analytics 表格式標記法、此標記法是內建資料表，或是使用查詢時間剖析器或可將現有資料對應到此標記法的函式所建立。 針對表格式標記法，我們會串連 OSSEM 機構名稱和屬性名稱，並將它們統稱為單一資料行名稱。 例如，包含包含 md5 屬性之雜湊實體之檔案實體的來源實體將會實作為下列 Log Analytics 資料行： SrcFileHashMd5。  (OSSEM 預設使用 *snake_case* ，而 Azure Sentinel 與 Log Analytics 則使用 *PascalCase*。 在 OSSEM 中，這類資料行會 src_file_hash_md5。 ) 

其他自訂欄位可能存在於 Azure Sentinel 的執行中，因為 Log Analytics 平臺需求和 Azure Sentinel 客戶專用的使用案例。

### <a name="schema-reference"></a>結構描述參考

若要深入瞭解，請參閱 [架構參考檔](./normalization-schema.md)，以及官方 [OSSEM 專案檔案](https://ossemproject.com/cdm/intro.html)。

架構參考也包含值和格式標準化。 來源欄位（原始或剖析）可能不是標準格式，也可能使用值的架構標準清單，因此必須轉換成架構標準標記法，才能完全正規化。

## <a name="parsers"></a>解析 器

### <a name="what-is-parsing"></a>什麼是剖析

有一組基本的已定義正規化資料表可供使用之後，您必須將 (parse/map) 資料轉換成這些資料表。 也就是說，您會將特定資料從其原始格式解壓縮到標準化架構中已知的資料行。 在 Azure Sentinel 中進行剖析時，會在 **查詢時** 使用剖析器， (使用 Kusto 查詢語言-KQL) 將現有資料表中的資料轉換 (例如 CommonSecurityLog、自訂記錄資料表、syslog) 轉換成正規化資料表架構。

另一種剖析（Azure Sentinel 尚不支援）在內嵌 **時間** ，可讓您將資料直接收集到正規化資料表中， (s) 因為它是從資料來源內嵌。 內嵌時間剖析可提供更佳的效能，因為資料模型會直接查詢，而不需要使用函數。

### <a name="using-query-time-parsers"></a>使用查詢時間剖析器

#### <a name="installing-a-parser"></a>安裝剖析器

可用的查詢時間剖析器可在 Azure Sentinel [官方 GitHub 存放庫](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))中取得。 每個剖析器都有版本設定，可讓客戶輕鬆地使用並監視未來的更新。 若要安裝剖析器：

1. 從上述 GitHub 連結中的每個相關 KQL 檔案，將相關的剖析器內容複寫到剪貼簿

1. 在 Azure Sentinel 入口網站中，開啟 [記錄檔] 頁面，並將 KQL 檔案的內容貼到 [記錄] 畫面，然後按一下 [ **儲存**]。

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="安裝新的剖析器":::

1. 在 [儲存] 對話方塊中，填入欄位，如下所示：
    1. **名稱**：建議使用與上述範例中相同的函式 **別名** 欄位 (值， *CheckPoint_Network_NormalizedParser*) 
    
    1. **另存**新檔：選取 **函數**

    1. 函式**別名**：應以下列命名慣例命名- *PRODUCT_Network_NormalizedParser* (在上述範例中， *CheckPoint_Network_NormalizedParser*) 。

    1. **類別**：您可以選取現有的類別，或建立新的類別 (例如 *NormalizedNetworkSessionsParsers*) 
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="安裝新的剖析器":::

若要正確地使用剖析器，您也必須安裝空的網路架構剖析器 (這會建立所有網路會話架構之欄位的空白表格式視圖) 和網路中繼剖析器 (將所有啟用的剖析器串連在一起，以建立網路架構) 中不同來源的單一資料檢視。 安裝這兩個剖析器時，會以類似上述步驟的方式來完成。

儲存查詢函式時，可能需要關閉查詢瀏覽器並重新開啟，以反映新的函式。

#### <a name="using-the-parsers"></a>使用剖析器

啟用之後，您就可以使用中繼剖析器，在所有目前啟用的剖析器上查詢統一的觀點。 若要這樣做，請移至 Sentinel 記錄頁面並查詢中繼剖析器：

:::image type="content" source="./media/normalization/query-parser.png" alt-text="安裝新的剖析器":::
 
您也可以在 [Sentinel 記錄] 頁面中，按一下 [查詢瀏覽器]，以存取中繼剖析器或個別的剖析器：

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="安裝新的剖析器":::

在右側窗格中，展開 [已儲存的查詢] 區段，並尋找 (的 ' NormalizedNetworkParsers ' 資料夾，或您在建立剖析器) 時所選擇的類別目錄名稱：

:::image type="content" source="./media/normalization/find-parser.png" alt-text="安裝新的剖析器":::

您可以按一下每個個別的剖析器，並查看它所使用的基礎函式， (或直接透過別名來存取它，如上述) 所述。 請注意，有些剖析器可以將原始欄位並存保留給正規化欄位，以方便使用。 您可以輕鬆地在剖析器的查詢中編輯這項功能。

#### <a name="customizing-parsers"></a>自訂剖析器

您可以重複上述步驟 (在 [查詢 explorer] 中尋找剖析器) 、按一下相關的剖析器，並查看其函數的執行。
例如，您可以決定編輯中繼剖析器，以新增/移除個別剖析器。

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="安裝新的剖析器":::
 
更改函數之後，請再按一下 [儲存]，然後使用相同的名稱、別名和類別。 將開啟覆寫對話方塊–請按 [確定]：

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="安裝新的剖析器":::

#### <a name="additional-information"></a>其他資訊

深入瞭解 Log Analytics 中的查詢時間剖析器執行)  ([儲存的查詢](../azure-monitor/log-query/saved-queries.md) 。


## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解 Azure Sentinel 的正規化架構。 如需參考架構本身，請參閱 [Azure Sentinel 資料正規化架構參考](./normalization-schema.md)。

* [Azure Sentinel 的 Blog](https://aka.ms/azuresentinelblog)。 尋找有關 Azure 安全性與相容性的部落格文章。
