---
title: 顧問建議的快速修復修復
description: 使用顧問中的快速修復執行批量修復
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502486"
---
# <a name="quick-fix-remediation-for-advisor"></a>顧問的快速修復修復
**快速修復**功能支援對多個資源的建議進行更快、更簡單的補救方法。 它提供資源批量修復的功能，並説明您通過大規模資源修復更快地優化訂閱。
此功能僅可用於某些建議，可通過 Azure 門戶。


## <a name="steps-to-use-quick-fix"></a>使用"快速修復"的步驟

1. 從具有 **"快速修復"** 標籤的建議清單中，按一下建議。

   ![顧問快速修復](./media/quick-fix-1.png)
   
   *圖像中的價格僅供參考*

2. 在"建議詳細資訊"頁上，您將看到具有此建議的資源清單。 選擇要為建議進行補救的所有資源。

   ![顧問快速修復](./media/quick-fix-2.png)
   
   *圖像中的價格僅供參考*

3. 選擇資源後，按一下 **"快速修復"** 按鈕進行批量修復。

   > [!NOTE]
   > 某些列出的資源可能會被禁用，因為您沒有修改它們的適當許可權。
   
   > [!NOTE]
   > 如果存在其他影響，除了 Advisor 中提到的好處外，還將在體驗中通知您，以説明您做出明智的補救決策。
   
4. 您將收到有關修復完成的通知。 如果資源清單視圖中存在未修復的資源和資源處於所選模式，則會看到錯誤。  


## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [顧問卓越運營建議](advisor-operational-excellence-recommendations.md)
* [顧問 REST API](https://docs.microsoft.com/rest/api/advisor/)
