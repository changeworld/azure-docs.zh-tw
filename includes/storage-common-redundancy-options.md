---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157208"
---
存儲帳戶的冗余選項包括：

* 本地冗余存儲 （LRS）：一種簡單、低成本的冗余策略。 資料在主區域內同步複製三次。
* 區域冗余存儲 （ZRS）：需要高可用性的方案的冗余。 資料在主區域中的三個 Azure 可用性區域中同步複製。
* 異地冗余存儲 （GRS）：跨區域冗余，以防止區域中斷。 資料在主區域中同步複製三次，然後非同步複製到次要區域。 對於對次要區域中資料的讀取存取，啟用讀取存取異地冗余存儲 （RA-GRS）。
* 地理區域冗余存儲 （GZRS） （預覽）：需要高可用性和最大耐用性的方案的冗余性。 資料在主區域中的三個 Azure 可用性區域上同步複製，然後非同步複製到次要區域。 對於對次要區域中資料的讀取存取，啟用讀取存取地理區域冗余存儲 （RA-GZRS）。

有關 Azure 存儲中的冗余選項的詳細資訊，請參閱[Azure 存儲冗余](../articles/storage/common/storage-redundancy.md)。
