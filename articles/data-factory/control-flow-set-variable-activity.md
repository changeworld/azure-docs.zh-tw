---
title: Azure Data Factory 中的設定變數活動
description: 了解如何使用「設定變數」活動來設定在 Data Factory 管線中定義的現有變數值
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930651"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory 中的設定變數活動

使用「設定變數」活動，為定義於 Data Factory 管線中的「字串」、「布林」或「陣列」類型設定現有變數值。

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 必要
-------- | ----------- | --------
NAME | 管線中的活動名稱 | 是
description | 說明活動用途的文字 | 否
type | 活動類型是 SetVariable | 是
value | 用來設定指定變數的字串常值或運算式物件值 | 是
variableName | 此活動所將設定的變數名稱 | 是


## <a name="next-steps"></a>後續步驟
深入了解 Data Factory 所支援的相關控制流程活動： 

- [附加變數活動](control-flow-append-variable-activity.md)
