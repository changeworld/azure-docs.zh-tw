---
title: Azure Advisor 的建議摘要
description: 取得有效建議的定期摘要
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: e446fca11e029e28e44ada884efd071f3142514c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518074"
---
# <a name="configure-periodic-summary-for-recommendations"></a>設定建議的定期摘要

Advisor **建議摘要** 提供簡單且主動的方式，讓您在不同的類別之間保持作用中的建議。 這項功能可讓您在不同的類別中設定所有作用中建議摘要的定期通知。 您可以使用動作群組，選擇您想要的通知通道，例如電子郵件、sms 或他人。 本文說明如何為您的 Advisor 建議設定 **建議摘要** 。


## <a name="setting-up-your-recommendation-digest"></a>設定您的建議摘要 

**建議摘要**建立體驗可協助您設定摘要。 您可以針對設定選取下列參數：
1. 類別：我們有像是成本、高可用性、效能及卓越營運的建議類別。 這項功能尚未提供安全性建議。
2. 摘要的頻率：摘要通知的頻率可以是每週、每兩周和每月。
3. 動作群組：您可以選取現有的動作群組，或建立新的動作群組。 若要深入瞭解動作群組，請參閱 [建立和管理動作群組](../azure-monitor/platform/action-groups.md)。
4. 摘要的語言
5. 建議摘要名稱：您可以使用方便使用的字串來追蹤和監視摘要。

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>在 Azure 入口網站中建立建議摘要的步驟

以下是建立**建議摘要**的步驟：
* **步驟1：** 在 Azure 入口網站中，移至 [ **Advisor** ]，然後在 [**監視**] 區段中選取 [**建議摘要**]。 

   ![建議摘要進入點](./media/digest-0.png)

* **步驟2：** 從頂端列選取 **新的建議摘要** ，如下所示：

   ![建立建議摘要](./media/digest-5.png)

* **步驟3：** 在 [ **範圍** ] 區段中，選取您的摘要 **訂** 用帳戶

   ![提供建議摘要輸入](./media/digest-1.png)

* **步驟4：** 在 [**條件**] 區段中，選取 [**類別**]、[**頻率**及**語言**] 等設定。

   ![提供建議摘要輸入條件](./media/digest-2.png)

* **步驟5：** 在 [ **動作群組** ] 區段中，選取摘要的 **動作群組** 。 您可以在這裡深入瞭解- [建立及管理動作群組](../azure-monitor/platform/action-groups.md)

   ![提供建議摘要輸入動作群組](./media/digest-3.png)

* **步驟6：** 在此 **摘要詳細資料**的最後一節中，您可以將名稱和狀態指派給建議摘要。 按下 [ **建立建議摘要** ] 以完成設定。
   ![完成建議摘要建立](./media/digest-4.png)

## <a name="next-steps"></a>接下來的步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure Advisor 簡介](advisor-overview.md) (機器翻譯)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
