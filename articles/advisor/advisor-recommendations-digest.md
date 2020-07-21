---
title: Azure Advisor 的建議摘要
description: 取得作用中建議的定期摘要
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: e446fca11e029e28e44ada884efd071f3142514c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518074"
---
# <a name="configure-periodic-summary-for-recommendations"></a>設定建議的定期摘要

Advisor**建議摘要**提供一個簡單又主動的方法，讓您在不同的類別之間保持作用中的建議。 此功能可讓您在不同的類別中，針對所有作用中建議的摘要設定定期通知。 您可以使用動作群組，為電子郵件、sms 或其他人等通知選擇您想要的通道。 本文說明如何為您的 Advisor 建議設定**建議摘要**。


## <a name="setting-up-your-recommendation-digest"></a>設定建議摘要 

**建議摘要**建立體驗可協助您設定摘要。 您可以選取下列設定的參數：
1. 類別：我們的建議類別如成本、高可用性、效能和營運卓越。 此功能尚不適用於安全性建議。
2. 摘要的頻率：摘要通知的頻率可以是每週、每週和每月。
3. 動作群組：您可以選取現有的動作群組，或建立新的動作群組。 若要深入瞭解動作群組，請參閱[建立和管理動作群組](../azure-monitor/platform/action-groups.md)。
4. 摘要的語言
5. 建議摘要名稱：您可以使用使用者易記的字串，以更好的追蹤和監視摘要。

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>在 Azure 入口網站中建立建議摘要的步驟

以下是建立**建議摘要**的步驟：
* **步驟1：** 在 Azure 入口網站中，移至 [ **Advisor** ]，然後在 [**監視**] 區段下，選取 [**建議摘要**] 

   ![建議摘要進入點](./media/digest-0.png)

* **步驟2：** 從頂端列選取 [**新增建議摘要**]，如下所示：

   ![建立建議摘要](./media/digest-5.png)

* **步驟3：** 在 [**範圍**] 區段中，選取您的摘要**訂**用帳戶

   ![提供建議摘要輸入](./media/digest-1.png)

* **步驟4：** 在 [**條件**] 區段中，選取 [**類別**]、[**頻率**] 和 [**語言**] 等設定。

   ![提供建議摘要輸入條件](./media/digest-2.png)

* **步驟5：** 在 [**動作群組**] 區段中，選取摘要的 [**動作群組**]。 您可以在這裡深入瞭解-[建立和管理動作群組](../azure-monitor/platform/action-groups.md)

   ![提供建議摘要輸入動作群組](./media/digest-3.png)

* **步驟6：** 在這份**摘要詳細資料**的最後一節中，您可以將名稱和狀態指派給建議摘要。 按 [**建立建議摘要**] 以完成設定。
   ![建立完整的建議摘要](./media/digest-4.png)

## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
