---
title: 瞭解 Azure Data Factory 資料流程的保留折扣 |Microsoft Docs
description: 瞭解如何套用保留折扣以執行 ADF 資料流程。 此折扣會以每小時為基礎套用至這些資料流程。
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: makromer
ms.openlocfilehash: 6936b9344196f8071a6c1859869c62e5bee22924
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811655"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>保留折扣如何套用至 Azure Data Factory 的資料流程

在您購買 ADF 的資料流程保留容量之後，保留折扣會自動套用至使用 Azure 整合執行時間的資料流程，該資料流程符合保留的計算類型和核心計數。

## <a name="how-reservation-discount-is-applied"></a>保留折扣的套用方式

保留折扣採「不用則作廢」的原則。 因此，如果您沒有任何一小時使用相符的 Azure 整合資源，則會遺失該小時的保留數量。 您無法遞轉未使用的保留時數。

當您停止使用 integration runtime 進行資料流程時，保留折扣會自動套用至指定範圍中的另一個相符資源。 如果在指定的範圍內找不到相符的資源，則會「失去」保留時數。

## <a name="discount-applied-to-adf-data-flows"></a>適用于 ADF 資料流程的折扣

ADF 資料流程保留容量折扣會套用至以小時為單位執行整合執行時間。 您購買的保留會與使用的整合執行時間所發出的計算使用量進行比對。 針對未執行完整小時的資料流程，保留會自動套用至其他符合保留屬性的資料流程。 此折扣可套用至同時執行的資料流程。 如果您沒有執行的資料流程符合保留屬性的全小時，您就無法獲得該小時保留折扣的完整權益。

下列範例顯示 ADF 資料流程保留容量折扣的套用方式，取決於您購買的核心數目，以及其執行時間。

- 案例1：為80核心的記憶體優化計算購買 ADF 的資料流程保留容量。 您執行的資料流程會將 Azure 整合執行時間設定為144核心的記憶體優化一小時。 您需支付一個小時的64核心使用隨用隨付價格的費用。 您可以取得每小時80核心的記憶體優化使用量的保留折扣。
- 案例2：為32核心的一般目的計算購買 ADF 的資料流程保留容量。 您可以使用 Azure 的一般計算 Azure integration runtime 的32核心，將您的資料流程進行一小時的偵測。 您可以取得該整小時使用量的保留折扣。

若要在計費使用量報告中了解及檢視 Azure Reservations 的應用，請參閱[了解 Azure 保留使用量](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 保留，請參閱下列文章：

- [什麼是 Azure 保留項目？](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
