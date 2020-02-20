---
title: 了解保留折扣如何套用至 Azure 資料總管
description: 了解保留折扣如何套用至 Azure 資料總管加成計量。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ab107a0afe8be1d95de8dafb21f239e6da733271
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199325"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>了解保留折扣如何套用至 Azure 資料總管

購買 Azure 資料總管保留容量之後，保留折扣會自動套用至符合保留屬性和數量的 Azure 保留容量資源。 保留包含 Azure 資料總管加成費用。 但不包含計算、網路、儲存體或任何其他用來操作 Azure 資料總管叢集的 Azure 資源。 這些資源的保留應另外購買。

## <a name="how-reservation-discount-is-applied"></a>保留折扣的套用方式

保留折扣採「不用則作廢」  的原則。 因此，如果您有任何一小時沒有相符的資源，就會失去該小時的保留數量。 您無法遞轉未使用的保留時數。

當您關閉資源時，保留折扣會自動套用至指定範圍中另一個相符的資源。 如果在指定的範圍內找不到相符的資源，則會「失去」  保留時數。

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>套用至 Azure 資料總管叢集的保留折扣

保留折扣會以每小時為基礎，套用至 Azure 資料總管加成使用量。 針對執行時間不滿一小時的 Azure 資料總管資源，保留折扣會自動套用至其他符合保留屬性的資料總管資源。 此折扣可套用至同時執行的 Azure 資料總管資源。 如果沒有既執行滿一個小時又符合保留屬性的 Azure 資料總管資源，您就無法獲得該小時保留折扣的完整權益。

> [!NOTE]
> * **強烈建議**為 Azure 資料總管叢集所使用的虛擬機器購買[保留容量](../../virtual-machines/windows/prepay-reserved-vm-instances.md)，以節省最多保留容量。
> * 保留購買會將折扣套用至所有區域。

## <a name="examples"></a>範例

下列範例說明如何根據所購買的核心數目，以及其執行時間，套用 Azure 資料總管保留容量折扣。
例如，針對引擎叢集大小：**2 D11_v2 VM**，您的隨選總費用是針對每小時 4 單位的 Azure 資料總管加成計量。

**案例 1**

您購買了 8 個 Azure 資料總管加成單位的 Azure 資料總管保留容量。 您會執行兩個 D13_v2 VM 的引擎叢集 (總共有 16 個核心)，其收費方式為每小時 16 單位的 Azure 資料總管加成單位，且符合保留的其餘屬性。 您需要就 8 個核心的 Azure 資料總管計算使用量支付隨用隨付價格的費用，並可取得一小時的 8 核心 Azure 資料總管加成單位使用量保留折扣。

對於其餘的範例，假設您所購買的 Azure 資料總管保留容量適用於 16 核心 Azure 資料總管叢集，而且其餘的保留屬性符合執行中的 Azure 資料總管叢集。

**案例 2**

您在兩個不同的區域中執行具有 8 個核心的兩個 Azure 資料總管引擎叢集，每個叢集各執行一小時。 這兩個叢集以及其所取用 16 個單位的 Azure 資料總管加成單位都會套用 16 核心保留折扣。

**案例 3**

您下午 1 點到 1:30 執行一個 16 核心 Azure 資料總管引擎叢集。 您從下午 1:30 到 2 點執行另一個 16 核心 Azure 資料總管引擎叢集。 兩者都在保留折扣的涵蓋範圍內。

**案例 4**

您下午 1 點到 1:45 執行一個 16 核心 Azure 資料總管引擎叢集。 您從下午 1:30 到 2 點執行另一個 16 核心 Azure 資料總管引擎叢集。 您需要就 15 分鐘的重疊時間支付隨用隨付價格的費用。 保留折扣會套用至其餘時間的 Azure 資料總管加成使用量。

若要在計費使用量報告中了解及檢視 Azure Reservations 的應用，請參閱[了解 Azure 保留使用量](understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

* [使用 Azure 資料總管保留容量預付 Azure 資料總管計算資源](../../data-explorer/pricing-reserved-capacity.md)  
* [什麼是 Azure 的保留](save-compute-costs-reservations.md)  
* [管理 Azure 保留](manage-reserved-vm-instance.md)  
* [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
* [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
* [了解 CSP 訂用帳戶的保留使用量](https://docs.microsoft.com/partner-center/azure-reservations)
