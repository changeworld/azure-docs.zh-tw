---
title: 映射資料流程
description: Azure 資料工廠映射資料串支轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 35f6b74f3b6cce5a0af812d4613f9da2f9fbd552
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606256"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure 資料工廠映射資料串流

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

集合聯集會將多個資料流結合在一起，並使那些資料流的 SQL 集合聯集成為來自集合聯集轉換的新輸出。 每個輸入流中的所有架構都將在數據流內部合併,而無需具有聯接鍵。

您可以通過選擇每個配置行旁邊的「+」圖示(包括源資料以及資料流中現有轉換的流)來組合設置表中的 n 個流。

![集合聯集轉換](media/data-flow/union.png "Union")

在這種情況下,可以合併來自多個來源的不同元資料(在此範例為三個不同的來源檔案),並將它們合併到單個流中:

![聯合轉換概述](media/data-flow/union111.png "聯盟 1")

為此,通過在"聯合設置"中添加其他行,包括要添加的所有源。 不需要通用搜尋或聯接鍵:

![聯合轉換設定](media/data-flow/unionsettings.png "聯合設定")

如果在 Union 之後設置 Select 轉換,則可以重新命名未從無標頭源命名的重疊欄位或欄位。 點選「檢查」以檢視此範例中來自三個不同來源的 132 個總列的組合元資料:

![聯合轉型最終](media/data-flow/union333.png "聯盟 3")

## <a name="name-and-position"></a>名稱與位置

當您選擇"按名稱聯合"時,每個列值將從每個源放入相應的列中,並帶有一個新的串聯元數據架構。

如果選擇「按位置合併」,則每個列值將從每個對應源下降到原始位置,從而產生新的合併資料流,其中每個源的資料將添加到同一流:

![聯合輸出](media/data-flow/unionoutput.png "聯合輸出")

## <a name="next-steps"></a>後續步驟

探索類似的轉換,包括[連網](data-flow-join.md)與[存在](data-flow-exists.md)。
