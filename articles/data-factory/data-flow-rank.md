---
title: 對應資料流程中的排名轉換
description: 如何使用 Azure Data Factory 的對應資料流程順位轉換產生排名資料行
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: 8584d1b64191cc5258c6eeeef9ae4125bf1a2c65
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044729"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>對應資料流程中的排名轉換 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您可以使用「排名」轉換，根據使用者指定的排序條件來產生排序的排名。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>組態

![排名設定](media/data-flow/rank-configuration.png "排名設定")

不**區分大小寫：** 如果排序資料行是字串類型，則會將大小寫納入排名中。 

**密集：** 如果啟用，則 rank 資料行將會是密集的等級。 每個排名計數將會是連續的數位，且不會在系結之後略過順位值。

**排名資料行：** 產生的 rank 資料行名稱。 此資料行的類型會是 long。

**排序條件：** 選擇您要排序的資料行，以及排序的排序次序。 順序決定排序優先順序。

上述設定會接受傳入的籃球資料，並建立名為 ' pointsRanking ' 的排名資料行。 具有資料行值最大值的資料 *列會有* *pointsRanking* 值1。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>範例

![排名設定](media/data-flow/rank-configuration.png "排名設定")

上述排名設定的資料流程腳本位於下列程式碼片段中。

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>後續步驟

使用 [篩選準則轉換](data-flow-filter.md)來根據排名值篩選資料列。
