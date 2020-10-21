---
title: 了解保留折扣 - 適用於 MariaDB的 Azure 資料庫
description: 了解保留折扣如何套用至適用於 MariaDB 的 Azure 資料庫
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 0691ec320b62ed212235a63dacedcf3ec38a23ba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151466"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>保留折扣如何套用至適用於 MariaDB 的 Azure 資料庫

購買適用於 MariaDB 的 Azure 資料庫保留容量之後，保留折扣就會自動套用至符合保留屬性和數量的 MariaDB 伺服器。 保留僅涵蓋適用於 MariaDB 的 Azure 資料庫計算成本。 您必須依標準費率支付儲存體和網路的費用。

## <a name="how-reservation-discount-is-applied"></a>保留折扣的套用方式

保留折扣採「不用則作廢」的原則。 因此，如果您有任何一小時沒有相符的資源，就會失去該小時的保留數量。 您無法遞轉未使用的保留時數。

當您關閉資源時，保留折扣會自動套用至指定範圍中另一個相符的資源。 如果在指定的範圍內找不到相符的資源，則會失去保留時數。

## <a name="discount-applied-to-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫所套用的折扣

適用於 MariaDB 的 Azure 資料庫保留容量折扣會套用至以小時為單位執行 MariaDB 伺服器。 您所購買的保留會與執行中適用於 MariaDB 的 Azure 資料庫伺服器所發出的計算使用量進行比對。 對於未執行滿一個小時的 MariaDB 伺服器，此保留會自動套用至符合保留屬性的其他適用於 MariaDB 的 Azure 資料庫。 折扣可套用同時執行的適用於 MariaDB 的 Azure 資料庫伺服器。 如果沒有既符合保留屬性又執行滿一個小時的 MariaDB 伺服器，您就無法獲得該小時的完整保留折扣權益。

下列範例說明如何根據所購買的核心數目，以及其執行時間，套用適用於 MariaDB 的 Azure 資料庫保留容量折扣。

**範例 1**：您針對 8 虛擬核心購買適用於 MariaDB 的 Azure 資料庫保留容量。 如果您執行的 16 虛擬核心適用於 MariaDB 的 Azure 資料庫伺服器符合其餘的保留屬性，則會針對 8 虛擬核心的 MariaDB 伺服器計算使用量向您收取隨用隨付價格的費用，而您可就一小時的 8 虛擬核心 MariaDB 伺服器計算使用量獲得保留折扣。

對於下列其餘範例，假設您所購買的適用於 MariaDB 的 Azure 資料庫保留容量適用於 16 虛擬核心適用於 MariaDB 的 Azure 資料庫，而且其餘的保留屬性符合執行中的 MariaDB 伺服器。

* **範例 2：** 您執行兩部具有 8 虛擬核心的適用於 MariaDB 的 Azure 資料庫伺服器，每部各執行一小時。 16 虛擬核心保留折扣會套用至這兩部 8 虛擬核心適用於 MariaDB 的 Azure 資料庫伺服器計算使用量。

* **範例 3**：您會從下午 1 點到 1:30 執行一部 16 虛擬核心適用於 MariaDB 的 Azure 資料庫伺服器。 您會從下午 1:30 到 2 點執行另一部 16 虛擬核心適用於 MariaDB 的 Azure 資料庫伺服器。 兩者都在保留折扣的涵蓋範圍內。

* **範例 4**：您會從下午 1 點到 1:45 執行一部 16 虛擬核心適用於 MariaDB 的 Azure 資料庫伺服器。 您會從下午 1:30 到 2 點執行另一部 16 虛擬核心適用於 MariaDB 的 Azure 資料庫伺服器。 您需要就 15 分鐘的重疊時間支付隨用隨付價格的費用。 保留折扣會套用至其餘時間的計算使用量。

若要在計費使用量報告中了解及檢視 Azure Reservations 的應用，請參閱[了解 Azure 保留使用量](./understand-reserved-instance-usage-ea.md)。

## <a name="next-steps"></a>後續步驟

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。