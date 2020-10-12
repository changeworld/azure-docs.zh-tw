---
title: 連線至 GitHub
description: 使用 GitHub 來指定您的 Common Data Model 實體參考
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84771031"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>使用 GitHub 讀取 Common Data Model 實體參考

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 中的 GitHub 連接器只會用來接收對應資料流程中 [Common Data Model](format-common-data-model.md) 格式的實體參考架構。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 GitHub 已連結服務支援的屬性。

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為 **GitHub**。 | 是
| userName | GitHub 使用者名稱 | 是 |
| 密碼 | GitHub 密碼 | 是 |

## <a name="next-steps"></a>後續步驟

在對應的資料流程中建立 [源資料集](data-flow-source.md) 。