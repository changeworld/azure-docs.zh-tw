---
title: 對應資料流程中的聯集轉換
description: Azure Data Factory 對應資料流程新增分支轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: e10418c18e6f8e0089a893e9d33ee1081f3131f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823643"
---
# <a name="union-transformation-in-mapping-data-flow"></a>對應資料流程中的聯集轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

集合聯集會將多個資料流結合在一起，並使那些資料流的 SQL 集合聯集成為來自集合聯集轉換的新輸出。 每個輸入資料流程的所有架構都會合並到您的資料流程內，而不需要有聯結索引鍵。

您可以在 [設定] 資料表中，選取每個已設定資料列旁的 [+] 圖示，包括來源資料，以及資料流程中現有轉換的資料流程，藉此合併多個資料流程。

以下是 ADF 的對應資料流程中聯集轉換的簡短影片逐步解說：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![集合聯集轉換](media/data-flow/union.png "Union")

在此情況下，您可以結合多個來源的不同中繼資料 (在此範例中，) 三個不同的來源檔案，然後將它們合併成單一資料流程：

![Union 轉換總覽](media/data-flow/union111.png "聯集1")

若要達成此目的，請在 Union 設定中加入其他資料列，方法是包含您想要新增的所有來源。 不需要一般查閱或聯結索引鍵：

![聯集轉換設定](media/data-flow/unionsettings.png "聯集設定")

如果您在聯集之後設定 Select 轉換，您將能夠重新命名不是從 headerless 來源命名的重迭欄位或欄位。 按一下 [檢查] 可在此範例中，從三個不同的來源，查看合併中繼資料與132總計資料行：

![Union 轉換最終](media/data-flow/union333.png "聯集3")

## <a name="name-and-position"></a>名稱和位置

當您選擇 [依名稱聯集] 時，每一個資料行值都會從每個來源的對應資料行中，放入新的串連中繼資料架構。

如果您選擇「依位置聯集」，每個資料行值都會從每個對應的來源放入原始位置，產生新的合併資料流程，其中每個來源的資料會加入至相同的資料流程：

![聯集輸出](media/data-flow/unionoutput.png "聯集輸出")

## <a name="next-steps"></a>後續步驟

探索類似的轉換，包括 [聯結](data-flow-join.md) 和 [存在](data-flow-exists.md)。
