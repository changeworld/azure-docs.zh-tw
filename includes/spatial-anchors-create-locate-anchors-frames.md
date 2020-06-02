---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006447"
---
## <a name="provide-frames-to-the-session"></a>提供工作階段的畫面

空間錨點工作階段的運作方式為對應使用者周圍的空間。 這麼做有助於判斷錨點的所在位置。 行動平台 (iOS 和 Android) 需要對觀景窗摘要進行原生呼叫，才能從您平台的 AR 媒體櫃取得畫面。 相反地，HoloLens 會持續掃描環境，因此無須像在行動裝置上那樣進行特定呼叫。