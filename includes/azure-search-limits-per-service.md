---
title: 包含檔案
description: 包含檔案
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682628"
---
搜尋服務受限於磁碟空間，或者索引或索引子的數目上限，取決於何者先出現。 下表記載儲存體限制。 如需最大物件限制，請參閱[依資源的限制](../articles/search/search-limits-quotas-capacity.md#index-limits)。

| 資源 | 免費 | 基本<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| 服務等級協定 (SLA)<sup>2</sup>  |否 |是 |是 |是 |是 |是 |是 |是 |
| 每個資料分割的儲存體 |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 每個服務的分割區 |N/A |1 |12 |12 |12 |3 |12 |12 |
| 分割區大小 |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| 複本數 |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> [基本] 具有一個固定的磁碟分割。 您可以使用其他搜尋單位來新增大型查詢磁碟區的複本。

<sup>2</sup> 服務等級協定適用於專用資源上的可計費服務。 免費服務和預覽功能不提供 SLA。 對於可計費服務，SLA 會在您為您的服務佈建足夠的備援性時生效。 查詢 (讀取) SLA 時需要兩個 (含) 以上的複本。 查詢和檢索 (讀寫) SLA 時需要三個 (含) 以上的複本。 分割區數目不是 SLA 考量。 