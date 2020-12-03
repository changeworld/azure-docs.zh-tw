---
title: 'Azure 範疇中的資料歷程 (預覽版) '
description: 描述資料歷程的概念。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: ceb30cee194994e5b31db312f191194932c937b4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552036"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Azure 範疇資料目錄用戶端中的資料歷程

本文概要說明 Azure 範疇資料目錄中的資料歷程。 此外，它也會詳細說明資料系統如何與目錄整合，以抓取資料歷程。 範疇可以針對組織資料資產的不同部分以及不同的準備層級，取得資料的歷程記錄，包括：

- 從各種平臺暫存的完全原始資料
- 轉換和準備的資料
- 視覺效果平臺所使用的資料。

## <a name="use-cases"></a>使用案例

資料歷程可廣泛理解為跨越資料來源的生命週期，以及跨資料資產移動一段時間的位置。 它適用于不同類型的回溯搜尋案例，例如疑難排解、追蹤資料管線中的根本原因和偵錯工具。 歷程也可用於資料品質分析、合規性和「假設」案例通常稱為「影響分析」。 歷程會以視覺化方式呈現，以顯示從來源移至目的地的資料，包括資料轉換的方式。 由於大部分的企業資料環境都有複雜的情況，因此，不需要對周邊資料點進行某些合併或遮罩，就很難瞭解這些觀點。

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Azure 範疇資料目錄中的歷程體驗

範疇資料目錄會與其他資料處理、儲存體和分析系統連線，以解壓縮歷程資訊。 這項資訊會結合以表示目錄中的一般案例特定歷程體驗。

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="結束歷程顯示從 blob 存放區複製的資料，一直到 Power BI 的儀表板":::

您的資料資產可能包含執行資料解壓縮的系統、轉換 (ETL/ELT 系統) 、分析和視覺化系統。 每個系統都會捕獲豐富的靜態和操作中繼資料，以描述系統界限內的資料狀態和品質。 資料目錄中的歷程的目標是要盡可能以最低的速度從每個資料系統中提取移動、轉換和操作中繼資料。

下列範例是在多個系統之間移動資料的一般使用案例，其中資料目錄會連接到每個系統進行歷程。

- Data Factory 會將資料從內部內部部署/原始區域複製到雲端中的登陸區域。 
- 資料處理系統（例如 Synapse、Databricks）會使用筆記本處理和轉換來自登陸區域到策劃區域的資料。
- 進一步處理分析模型中的資料，以獲得最佳查詢效能和匯總。 
- 資料視覺效果系統會透過其中繼模型取用資料集和處理常式，以建立 BI 儀表板、ML 實驗等等。

## <a name="lineage-granularity"></a>歷程資料細微性

本節涵蓋資料目錄收集歷程資訊的資料細微性詳細資料。 這種資料細微性可能會根據目前的資料系統而有所不同。

### <a name="entity-level-lineage-sources--process--targets"></a>實體層級歷程：來源 (s) > 進程 > 目標 (s)  

- 歷程會以圖形表示，通常它會在由計算系統叫用的進程所連接的資料儲存系統中包含來源和目標實體。 
- 資料系統會連接到資料目錄，以產生並報告參考基礎資料系統實體物件的唯一物件，例如： SQL 預存程式、筆記本等等。
- 使用額外的中繼資料（例如擁有權）進行高精確度歷程，以針對來源 & 目標實體以人類可讀取的格式顯示歷程。 例如：在 hive 資料表層級（而非資料分割或檔案層級）的歷程。

### <a name="column-or-attribute-level-lineage"></a>資料行或屬性層級歷程

識別來源實體的屬性 (s) ，此實體是用來建立或衍生目標實體中) 的屬性 (s。 來源屬性的名稱可以在目標中保留或重新命名。 ADF 這類系統可從內部部署環境對雲端進行一對一複製。 例如：`Table1/ColumnA -> Table2/ColumnA`。

### <a name="process-execution-status"></a>處理常式執行狀態

為了支援根本原因分析和資料品質案例，我們會在資料處理系統中捕捉作業的執行狀態。 這項需求與取代其他資料處理系統的監視功能沒有任何關係，目標是取代它們。 

## <a name="summary"></a>摘要

歷程是範疇資料目錄的重要功能，可支援品質、信任和審核案例。 資料目錄的目標是要建立一個強大的架構，讓環境中的所有資料系統都能自然地連接和報告歷程。 中繼資料可供使用之後，資料目錄可以將資料系統所提供的中繼資料整合在一起，以支援資料治理使用案例。

## <a name="next-steps"></a>後續步驟

* [快速入門：在 Azure 入口網站中建立 Azure 範疇帳戶](create-catalog-portal.md)
* [快速入門：使用 Azure PowerShell/Azure CLI 建立 Azure 範疇帳戶](create-catalog-powershell.md)
* [快速入門：使用範疇 Studio](use-purview-studio.md)