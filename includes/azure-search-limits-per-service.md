---
title: 包含檔案
description: 包含檔案
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272629"
---
儲存體受限於磁碟空間，或者索引、文件或其他高層級資源的「數目上限」**，取決於何者較早出現。 下表記載儲存體限制。 有關索引、文檔和其他物件的最大限制，請參閱[按資源劃分的限制](../articles/search/search-limits-quotas-capacity.md#index-limits)。

| 資源 | 免費 | 基本<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| 服務等級協定 （SLA）<sup>3</sup>  |否 |是 |是 |是 |是 |是 |是 |是 |
| 每個資料分割的儲存體 |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 每個服務的分割區 |N/A |1 |12 |12 |12 |3 |12 |12 |
| 分割區大小 |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 複本數 |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> [基本] 具有一個固定的磁碟分割。 在此層中，其他搜索單元用於為增加的查詢工作負荷分配更多副本。

<sup>2</sup> S3 HD 的硬限制為三個分區，低於 S3 的分區限制。 較低的分割區限制是因為 S3 HD 的索引計數較高。 假設計算資源 (儲存體和處理) 和內容 (索引和文件) 的服務限制都存在，會先達到內容限制。

<sup>3</sup>為專用資源的計費服務提供服務等級協定。 免費服務和預覽功能不提供 SLA。 對於可計費服務，SLA 會在您為您的服務佈建足夠的備援性時生效。 查詢（讀取）SL 需要兩個或多個副本。 查詢和索引（讀寫）SL 需要三個或更多副本。 分區數不是 SLA 注意事項。 