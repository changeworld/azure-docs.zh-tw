---
title: Azure Data Factory 中的附加變數活動
description: 了解如何設定「附加變數」活動，以將值新增至 Data Factory 管線中定義的現有陣列變數
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414202"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory 中的附加變數活動
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
使用「附加變數」活動，將值新增至 Data Factory 管線中定義的現有陣列變數。

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 必要
-------- | ----------- | --------
NAME | 管線中的活動名稱 | 是
description | 說明活動用途的文字 | 否
type | 活動類型是 AppendVariable | 是
value | 用來附加到指定變數的字串常值或運算式物件值 | 是
variableName | 將由活動修改的變數名稱，該變數必須是「陣列」類型 | 是

## <a name="next-steps"></a>後續步驟
深入了解 Data Factory 所支援的相關控制流程活動： 

- [設定變數活動](control-flow-set-variable-activity.md)
