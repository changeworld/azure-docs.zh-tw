---
title: 命名 Azure Data Factory 實體的規則
description: 描述 Data Factory 實體的命名規則。
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 21adf26c2dbaca4507a4c925e3dae3b99c9d53ba
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497515"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 命名規則

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

下表提供 Data Factory 購件命名規則。

| 名稱 | 名稱唯一性 | 驗證檢查 |
|:--- |:--- |:--- |
| 資料處理站 | 在 Microsoft Azure 中是唯一的。 名稱不區分大小寫，亦即 `MyDF` 和 `mydf` 會指相同的 Data Factory。 |<ul><li>每個 Data Factory 只會繫結至一個 Azure 訂用帳戶。</li><li>物件名稱必須以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。</li><li>每個虛線 (-) 字元的前面和後面必須緊接字母或數字。 容器名稱中不允許使用連續虛線。</li><li>名稱長度可以介於 3-63 個字元。</li></ul> |
| 連結的服務/資料集/管線/資料流程 | 在 Data Factory 中是唯一的。 名稱不區分大小寫。 |<ul><li>物件名稱必須以字母開頭。</li><li>不允許使用下列字元： "."、"+"、"？"、"/"、"<"、">"、"*"、"%"、"&"、"："、" \\ "</li><li>連結服務、資料流程和資料集的名稱中不允許使用虛線 ( "-" ) 。</li></ul>  |
| 整合執行階段 |在 Data Factory 中是唯一的。 名稱不區分大小寫。 |<ul><li>整合執行時間名稱只能包含字母、數位和虛線 (-) 字元。</li><li>第一個字元和最後一個字元必須是字母或數位。 每個虛線 (-) 字元的前面和後面必須緊接字母或數字。</li><li>整合執行時間名稱中不允許連續的連字號。 </li></ul> |
| 資料流程轉換 | 在資料流程內是唯一的。 名稱不區分大小寫 | <ul><li>資料流程轉換名稱只能包含字母和數位</li><li>第一個字元必須是字母。 </li></ul> |
| 資源群組 |在 Microsoft Azure 中是唯一的。 名稱不區分大小寫。 | 如需詳細資訊，請參閱 [Azure 命名規則和限制](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)。 |

## <a name="next-steps"></a>後續步驟
遵循[快速入門：建立資料處理站](quickstart-create-data-factory-powershell.md)一文中的逐步指示，以了解如何建立資料處理站。 
