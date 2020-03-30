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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272630"
---
您可以在訂閱中創建多個服務。 每個層都可以在特定層進行預配。 您僅受每個層允許的服務數的限制。 例如，您最多可在基本層建立 12 個服務，並在同一個訂用帳戶內的 S1 層另外建立 12 個服務。 有關層的詳細資訊，請參閱[為 Azure 認知搜索選擇 SKU 或層](../articles/search/search-sku-tier.md)。

最大服務限制可以視要求引發。 如果在同一訂閱中需要更多服務，請與 Azure 支援部門聯繫。

| 資源            | 免費<sup>1</sup> | 基本 | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| 服務數目上限    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| 搜索單位 （SU） 中的最大比例<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> 免費服務是根據共用而非專用的資源。 共用資源上不支援相應增加。

<sup>2</sup>搜索單元是計費單位，作為*副本*或*分區*分配。 您需要這兩種資源才能進行儲存、編製索引及查詢作業。 若要深入了解 SU 計算，請參閱[針對查詢和索引工作負載調整資源層級](../articles/search/search-capacity-planning.md)。 