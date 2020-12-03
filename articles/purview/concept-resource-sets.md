---
title: 瞭解資源集
description: 本文說明什麼是資源集，以及 Azure 範疇如何建立它們。
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552018"
---
# <a name="understanding-resource-sets"></a>瞭解資源集

本文可協助您瞭解 Azure 範疇如何使用資源集將資料資產對應至邏輯資源。

## <a name="background-info"></a>背景資訊

大規模資料處理系統通常會在磁片上將單一資料表儲存為多個檔案。 此概念是在 Azure 範疇中使用資源集來表示。 資源集是目錄中的單一物件，代表儲存體中的大量資產。

例如，假設您的 Spark 叢集已將資料框架保存到 ADLS Gen2 資料來源。 雖然在 Spark 中，資料表看起來像是單一邏輯資源，但在磁片上可能有數千個 Parquet 檔案，每個檔案都代表資料框架總內容的資料分割。 IoT 資料和 web 記錄資料具有相同的挑戰。 假設您有一個感應器，每秒輸出一次記錄檔。 除非您有數十萬個來自該單一感應器的記錄檔，否則不需要花費很長的時間。

為了解決將大量資料資產對應到單一邏輯資源的挑戰，Azure 範疇會使用資源集。

## <a name="how-azure-purview-detects-resource-sets"></a>Azure 範疇如何偵測資源集

Azure 範疇僅支援在 Azure Blob、ADLS Gen1 和 ADLS Gen2 中偵測資源集。

Azure 範疇會使用稱為「自動資源集探索」的功能，自動偵測資源集。 這項功能會查看透過掃描內嵌的所有資料，並將其與一組已定義的模式進行比較。

例如，假設您掃描其 URL 為的資料來源 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` 。 Azure 範疇會查看路徑區段，並判斷它們是否符合任何內建模式。 它具有適用于 Guid、數位、日期格式、當地語系化代碼 (的內建模式，例如 en-us) 等等。 在此情況下，數位模式會與 *23* 相符。 Azure 範疇會假設此檔案是名為之資源集的一部分 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` 。

或者，針對類似的 URL， `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` Azure 範疇會同時符合當地語系化模式和數位模式，並產生名為的資源集 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` 。

使用此策略時，Azure 範疇會將下列資源對應至相同的資源集 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` ：

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Azure Data Lake Storage Gen2 現已全面上市。 我們建議您現在就開始使用。 如需詳細資訊，請參閱[產品頁面](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)。

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure 範疇不會偵測為資源集的檔案類型

範疇刻意不會嘗試將大多數檔案類型（例如 Word、Excel 或 PDF）分類為資源集。 因為這是一種常見的資料分割檔案格式，所以會 Csv 例外狀況。

## <a name="how-azure-purview-scans-resource-sets"></a>Azure 範疇如何掃描資源集

當 Azure 範疇偵測到它認為是資源集一部分的資源時，它會從完整掃描切換至範例掃描。 在範例掃描中，它只會開啟它在資源集中所認為的部分檔案。 針對它開啟的每個檔案，它會使用其架構並執行其分類器。 Azure 範疇接著會在已開啟的資源之間尋找最新的資源，並在目錄中的整個資源集的專案中使用該資源的架構和分類。

## <a name="what-azure-purview-stores-about-resource-sets"></a>Azure 範疇儲存的資源集

除了單一架構和分類外，Azure 範疇還會儲存下列資源集的相關資訊：

- 從最新的資料分割資源進行深層掃描的資料。
- 匯總組成資源集之分割區資源的相關資訊。
- 分割區計數，顯示找到的資料分割資源數量。
- 此架構計數會顯示它在執行深層掃描的範例集中找到多少唯一的架構。 此值為介於1到5之間的數位，或大於5、5 + 的值。
- 當資源集內包含一個以上的資料分割類型時，資料分割類型的清單。 例如，IoT 感應器可能會輸出 XML 和 JSON 檔案，但兩者都是相同資源集的邏輯部分。

## <a name="built-in-resource-set-patterns"></a>內建的資源集模式

Azure 範疇支援下列資源集模式。 這些模式可以顯示為目錄中的名稱或作為檔案名的一部分。

| 模式名稱 | 顯示名稱 | 說明 |
|--------------|--------------|-------------|
| GUID         | GUID.EMPTY       | 全域唯一識別碼，如 [RFC 4122](https://tools.ietf.org/html/rfc4122)中所定義。 |
| 數字       | N-1          | 一或多個數位。 |
| 日期/時間格式 | N-1     | Azure 範疇支援不同類型的日期/時間格式，但全部都縮減為一系列的 {N} 秒。 |
| 4ByteHex     | 螺栓        | 四位數的十六進位數位。 |
| 當地語系化 | 當地語系化        | 語言標記，如 [BCP 47](https://tools.ietf.org/html/bcp47)中所定義。 Azure 範疇支援的標記包含連字號 ( ) 或 (_) 的底線。 例如，en_ca 和 en-us。 |

## <a name="issues-with-resource-sets"></a>資源集的問題

雖然資源集在大部分情況下都能順利運作，但您可能會遇到下列問題： Azure 範疇：

- 將資產錯誤地標示為資源集
- 將資產放入錯誤的資源集
- 將資產不正確地標示為不是資源集

## <a name="next-steps"></a>後續步驟

若要開始使用資料目錄，請參閱 [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)。
