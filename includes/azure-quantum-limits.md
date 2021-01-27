---
title: 包含檔案
description: 包含檔案
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920458"
---
### <a name="provider-limits--quota"></a>提供者限制 & 配額

Azure Quantum 服務支援第一方和協力廠商服務提供者。 協力廠商提供者擁有其限制和配額。 在提供者分頁中設定協力廠商提供者時，使用者可以在 Azure 入口網站中查看供應專案和限制。 

您可以在下方找到 Microsoft 第一方優化解決方案提供者已發佈的配額限制。 

#### <a name="learn--develop-sku"></a>瞭解 & 開發 SKU

| 資源 | 限制 |
| --- | --- |
| 以 CPU 為基礎的並行作業 | 最多5個並行作業 |
| 以 FPGA 為基礎的並行作業 | 最多2個並行作業 |
| 以 CPU 為基礎的規劃時間 | 每月20小時  |
| 以 FPGA 為基礎的規劃時間 | 每月1小時  |

如果您使用「學習 & 開發 SKU，則 **無法** 要求增加您的配額限制。 相反地，您應該切換到大規模 SKU 的效能。

#### <a name="performance-at-scale-sku"></a>大規模 SKU 效能

| 資源 | 限制 |
| --- | --- |
| 以 CPU 為基礎的並行作業 | 最多100個並行作業 |
| 以 FPGA 為基礎的並行作業 | 最多10個並行作業 |
| 規劃時間 | 每月50000小時  |

如果您需要要求增加限制，請與 Azure 支援人員聯繫。 

如需詳細資訊，請參閱 [Azure Quantum 定價頁面](https://aka.ms/AQ/Pricing)。
如需協力廠商供應專案的詳細資訊，請參閱 Azure 入口網站中的相關提供者頁面。
