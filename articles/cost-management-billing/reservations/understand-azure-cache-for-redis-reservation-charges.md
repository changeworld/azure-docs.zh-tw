---
title: 了解保留折扣如何套用至 Azure Cache for Redis | Microsoft Docs
description: 了解保留折扣如何套用至 Azure Cache for Redis 執行個體。
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 7b57bfbd09cd2b528b5a879acdb41b4716289aa6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371999"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>保留折扣如何套用至 Azure Cache for Redis

購買 Azure Cache for Redis 保留容量之後，保留折扣就會自動套用至符合保留屬性和數量的快取執行個體。 保留僅涵蓋 Azure Cache for Redis 的計算成本。 您必須依標準費率支付儲存體和網路的費用。 保留容量僅供[進階層](/azure/azure-cache-for-redis/quickstart-create-redis)快取使用。

## <a name="how-reservation-discount-is-applied"></a>保留折扣的套用方式

保留折扣採 **不用則作廢** 的原則。 因此，如果您有任何一小時沒有相符的資源，就會失去該小時的保留數量。 您無法遞轉未使用的保留時數。

當您關閉資源時，保留折扣會自動套用至指定範圍中另一個相符的資源。 如果在指定的範圍內找不到相符的資源，則會失去保留時數。

## <a name="discount-applied-to-azure-cache-for-redis"></a>Azure Cache for Redis 所套用的折扣

Azure Cache for Redis 保留容量折扣會以每小時為基礎套用至您的快取。 您所購買的保留會與對應到執行中快取所發出的計算使用量進行比對。 若這些快取執行未滿一個小時，此保留會自動套用至其他與保留屬性相符的快取。 此折扣可以同時套用至各個執行中的快取。 如果沒有既符合保留屬性又執行滿一個小時的快取，您就無法獲得該小時的完整保留折扣權益。

下列範例說明如何根據所購買的快取數目，以及其執行時間，套用 Azure Cache for Redis 保留容量折扣。

**範例 1** ：您購買了 Azure Cache for Redis 保留容量以供 6 GB 快取使用。 如果您執行的 13 GB 快取符合其餘的保留屬性，則會針對 7 GB 的 Azure Cache for Redis 計算使用量向您收取隨用隨付價格的費用，而您可就一小時 6 GB 快取的計算使用量獲得保留折扣。

針對下列其餘範例，假設您所購買的 Azure Cache for Redis 保留容量適用於 26 GB 快取，而且其餘保留屬性符合執行中的快取。

* **範例 2：** 您會執行 2 個 13 GB 的快取一小時。 26 GB 保留折扣適用於這兩個快取的計算使用量。

* **範例 3** ：您會從下午 1 點到 1:30 執行 1 個 26 GB 的快取。 接下來會從下午 1:30 到 2 點執行另一個 26 GB 的快取。 兩者都在保留折扣的涵蓋範圍內。

* **範例 4** ：您會從下午 1 點到 1:45 執行 1 個 26 GB 的快取。 接下來會從下午 1:30 到 2 點執行另一個 26 GB 的快取。 您需要就 15 分鐘的重疊時間支付隨用隨付價格的費用。 保留折扣會套用至其餘時間的計算使用量。

若要在計費使用量報告中了解及檢視 Azure Reservations 的應用，請參閱[了解 Azure 保留使用量](./understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡
如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。