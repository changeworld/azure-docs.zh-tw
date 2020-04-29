---
title: 包含檔案
description: 包含檔案
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272630"
---
您可以在訂用帳戶內建立多個服務。 每一個都可以在特定層布建。 您只受限於每一層所允許的服務數目。 例如，您最多可在基本層建立 12 個服務，並在同一個訂用帳戶內的 S1 層另外建立 12 個服務。 如需有關階層的詳細資訊，請參閱[選擇 Azure 認知搜尋的 SKU 或層級](../articles/search/search-sku-tier.md)。

最大服務限制可以視要求引發。 如果您需要相同訂用帳戶內的更多服務，請聯絡 Azure 支援。

| 資源            | 免費<sup>1</sup> | 基本 | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| 服務數目上限    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| 搜尋單位的最大縮放數（SU）<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> 免費服務是根據共用而非專用的資源。 共用資源上不支援相應增加。

<sup>2</sup>搜尋單位是計費單位，配置為*複本*或資料*分割*。 您需要這兩種資源才能進行儲存、編製索引及查詢作業。 若要深入了解 SU 計算，請參閱[針對查詢和索引工作負載調整資源層級](../articles/search/search-capacity-planning.md)。 