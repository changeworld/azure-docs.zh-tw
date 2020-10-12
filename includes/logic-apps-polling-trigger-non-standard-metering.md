---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74793439"
---
若要更精確地預估耗用成本，請考慮任何一天可能送達的訊息或事件數目，而非只根據輪詢間隔來計算成本。 當事件或訊息符合觸發條件時，許多觸發程序會立即嘗試讀取任何和所有其他符合條件的等候事件或訊息。 這個行為表示，即使在您選取較長的輪詢間隔時，觸發程序也會根據有資格啟動工作流程的等候事件或訊息數目而引發。 遵循此行為的觸發程序包括 Azure 服務匯流排和 Azure 事件中樞。

因此，舉例來說，假設您所設定的觸發程序會每天檢查端點。 當觸發程序檢查端點並找到 15 個符合條件的事件時，觸發程序便會引發並執行對應的工作流程 15 次。 Logic Apps 會測量這 15 個工作流程所執行的所有動作，包括觸發程序要求。 若要計算可能的成本，請嘗試使用 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)。