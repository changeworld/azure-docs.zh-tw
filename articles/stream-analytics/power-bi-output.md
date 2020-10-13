---
title: Azure 串流分析的 Power BI 輸出
description: 本文說明如何將資料從 Azure 串流分析輸出至 Power BI。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: aee5cb077604e5fc95647eca0e6570ea3582a785
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823002"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Azure 串流分析的 Power BI 輸出

您可以使用 [Power BI](https://powerbi.microsoft.com/) 作為串流分析作業的輸出，來為分析結果提供豐富的視覺體驗。 您可以針對可運作的儀表板、產生報告，以及計量驅動的報告使用這項功能。

Azure China 21Vianet 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Power BI 輸出。

## <a name="output-configuration"></a>輸出設定

下表列出設定 Power BI 輸出的屬性名稱及其描述。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |提供易記名稱，用於在查詢中將查詢輸出指向這個 Power BI 輸出。 |
| 群組工作區 |若要與其他 Power BI 使用者共用資料，您可以選取 Power BI 帳戶內的群組，若您不希望寫入群組，請選擇 [我的工作區]。 更新現有的群組需要更新 Power BI 驗證。 |
| 資料集名稱 |提供您想要讓 Power BI 輸出使用的資料集名稱。 |
| 資料表名稱 |提供 Power BI 輸出資料集的資料表名稱。 目前，串流分析作業的 Power BI 輸出在資料集內只能有一個資料表。 |
| 授權連接 | 您必須對 Power BI 授權，才能進行輸出設定。 將這個輸出存取權授與 Power BI 儀表板之後，您可以藉由變更使用者帳戶密碼、刪除作業輸出，或刪除串流分析作業，來撤銷存取權。 | 

如需設定 Power BI 輸出和儀表板的逐步解說，請參閱 [Azure 串流分析與 Power BI](stream-analytics-power-bi-dashboard.md) 教學課程。

> [!NOTE]
> 請勿在 Power BI 儀表板中明確建立資料集和資料表。 當作業開始並將輸出提取至 Power BI 時，就會自動填入資料集和資料表。 如果作業查詢沒有產生任何結果，則不會建立資料集和資料表。 如果 Power BI 已經具有與串流分析作業中提供的名稱相同的資料集和資料表，則會覆寫現有的資料。
>

### <a name="create-a-schema"></a>建立結構描述

如果 Power BI 資料集和資料表結構描述尚不存在，則 Azure 串流分析會為使用者建立。 在其他情況下，則會以新的值更新資料表。 目前，資料集內只能有一個資料表存在。 

Power BI 是使用先進先出(FIFO) 保留原則。 將會在資料表中收集資料，直到達到 200,000 個資料列為止。

> [!NOTE]
> 我們不建議使用多個輸出來寫入相同的資料集，因為這可能會導致數個問題。 每個輸出都會嘗試獨立建立 Power BI 資料集，這可能會導致多個具有相同名稱的資料集。 此外，如果輸出的架構不一致，則資料集會在每次寫入時變更架構，導致太多架構變更要求。 即使可以避免這些問題，但多個輸出的效能會比單一合併輸出的效能低。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>將資料類型從串流分析轉換至 Power BI

如果輸出結構描述變更，則 Azure 串流分析會在執行階段動態更新資料模型。 所有資料行名稱變更、資料行類型變更以及資料行新增或移除都會加以追蹤。

如果 Power BI 資料集和資料表不存在，此資料表包含從[串流分析資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)至 Power BI [實體資料模型 (EDM) 類型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)的資料類型轉換。

從串流分析 | 至 Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | String
Datetime | Datetime
FLOAT | Double
記錄陣列 | 字串類型、常數值 "IRecord" 或 "IArray"

### <a name="update-the-schema"></a>更新結構描述

串流分析會根據輸出中的第一組事件來推斷資料模型結構描述。 之後會視需要更新資料模型結構描述，以容納原始結構描述放不下的連入事件。

避免 `SELECT *` 查詢，以防止跨越資料列的動態結構描述更新。 除了潛在的效能影響以外，可能導致結果所花費的時間不定。 選取必須顯示在 Power BI 儀表板上的確切欄位。 此外，資料值應該與所選的資料類型相符。

先前/目前 | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | 字串 | 字串 | 字串 | String 
Datetime | String | String |  Datetime | String

## <a name="output-batch-size"></a>輸出批次大小

如需輸出批次大小，請參閱 [Power BI REST API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)
