---
title: Azure 保留建議的建立方式
description: 了解如何針對虛擬機器建立 Azure 保留建議。
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76991043"
---
# <a name="how-reservation-recommendations-are-created"></a>建立保留建議的方式
Azure 保留執行個體 (RI) 購買建議是透過 Azure 耗用量[保留建議 API](/rest/api/consumption/reservationrecommendations)、[Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)，以及 Azure 入口網站中的保留購買體驗來提供的。

下列步驟定義了計算建議的方法： 
1. 建議引擎會針對您在過去 7 天、30 天以及 60 天指定範圍內，評估資源的每小時使用量。
2. 根據使用量資料，引擎會在使用和不使用保留的情況下來模擬您的成本。
3. 系統會針對不同的數量來模擬成本，並建議節省最多成本的數量。
4. 如果您的資源會定期關閉，則模擬找不到任何節省成本，因此不會提供任何購買建議。

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor 中的建議
Azure Advisor 中提供了虛擬機器的保留購買建議。 請記住下列幾點： 
- Advisor 只具有單一訂用帳戶範圍建議。
- Advisor 中提供了以 30 天回顧期間計算的建議。
- 如果您購買共用範圍的保留，Advisor 保留購買建議最多可能需要 30 天才會消失。

## <a name="other-expected-api-behavior"></a>其他預期的 API 行為
- 如果您使用 7 天的回顧期間，當 VM 關閉超過一天時，您可能無法取得建議。

## <a name="next-steps"></a>後續步驟
- 了解 [Azure 保留折扣如何套用至虛擬機器](../manage/understand-vm-reservation-charges.md)。
