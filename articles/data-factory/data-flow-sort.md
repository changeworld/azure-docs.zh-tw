---
title: 對應資料流程中的排序轉換
description: Azure Data Factory 對應資料排序轉換
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606329"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>對應資料流程中的排序轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

「排序」轉換可讓您排序目前資料流程上的傳入資料列。 您可以選擇個別的資料行，並以遞增或遞減順序排序。

> [!NOTE]
> 對應資料流程會在 spark 叢集上執行，而這些叢集會將資料分散到多個節點和分割區。 如果您選擇在後續的轉換中重新分割資料，您可能會因為資料重新輪換而失去排序。

## <a name="configuration"></a>設定

![排序設定](media/data-flow/sort.png "Sort")

不**區分大小寫：** 排序字串或文字欄位時，是否要忽略大小寫

**只在資料分割內排序：** 當資料流程在 spark 上執行時，每個資料流程都會分割成多個磁碟分割。 此設定只會在傳入分割區內排序資料，而不會排序整個資料流程。 

**排序條件：** 選擇排序依據的資料行，以及排序發生的順序。 順序會決定排序優先順序。 選擇是否要在資料流程的開頭或結尾出現 null。

### <a name="computed-columns"></a>計算資料行

若要在套用排序之前修改或解壓縮資料行值，請將滑鼠停留在資料行上，然後選取 [計算資料行]。 這會開啟 [運算式產生器] 來建立排序作業的運算式，而不是使用資料行值。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>範例

![排序設定](media/data-flow/sort.png "Sort")

上述排序設定的資料流程腳本位於下列程式碼片段中。

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>後續步驟

排序之後，您可能會想要使用「[匯總」轉換](data-flow-aggregate.md)
