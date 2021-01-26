---
title: 使用保留容量來節省計算成本
description: 瞭解如何購買 Azure Data Factory 的資料流程保留容量，以節省您的計算成本。
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 01/25/2021
ms.openlocfilehash: 8bea1ff0dcb945e969553a4f643e289a5157f966
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811661"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>使用保留容量來節省資源成本-Azure Data Factory 的資料流程

藉由針對計算資源（相較于隨用隨付價格）認可保留，以 Azure Data Factory 的資料流程成本來節省成本。 使用保留容量時，您可以在一或三年期間對 ADF 資料流程使用量進行承諾，以取得計算成本的大量折扣。 若要購買保留容量，您必須指定 Azure 區域、計算類型、核心計數和期限。

您不需要將保留指派給特定 factory 或整合執行時間。 現有的 factory 或新部署的工廠會自動獲得權益。 藉由購買保留，您可以認可一或三年期間的資料流程計算成本使用量。 當您購買保留專案時，符合保留屬性的計算費用就不會再以隨用隨付費率計費。 

您可以在 [Azure 入口網站](https://portal.azure.com)中購買保留容量。 保留的付款方式可為[預先付款或每月付款](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md)。 若要購買保留容量：

- 您必須是至少一個具有隨用隨付費率的企業或個別訂用帳戶的擁有者角色。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用 **新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。 保留容量。

如需企業客戶和隨用隨付客戶如何針對保留購買收費的詳細資訊，請參閱 [瞭解您企業註冊的 azure 保留使用量](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) ，以及 [瞭解隨用隨付訂用帳戶的 azure 保留使用量](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage.md)。

> [!NOTE]
> 購買保留容量不會預先配置或保留特定基礎結構資源 (虛擬機器或叢集) 供您使用。

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>判斷購買前所需的適當 Azure IR 大小

保留大小應該以現有或即將部署的資料流程使用相同計算層級的總計算量為基礎。

例如，假設您使用以32核心優化的記憶體來每小時執行管線。 此外，假設您打算在下個月內部署使用一般用途64核心的額外管線。 還有，假設您知道您需要使用這些資源至少 1 年的時間。 在此情況下，您應該針對記憶體優化的資料流程和一般用途64核心1年的保留購買32個核心1年保留。

## <a name="buy-reserved-capacity"></a>購買保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]   > [保留]  。
3. 選取 [ **新增** ]，然後在 [ **購買保留** ] 窗格中，選取 [ **ADF 資料流程** ]，為 adf 資料流程購買新的保留。
4. 填寫您選取的必要欄位和屬性，以取得保留容量折扣。 取得折扣的實際資料流程數目取決於選取的範圍和數量。
5. 在 [ **成本** ] 區段中，檢查容量保留的成本。
6. 選取 [購買]  。
7. 選取 [檢視此保留] 以查看您的購買狀態。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [了解 Azure 保留折扣](data-flow-understand-reservation-charges.md)
