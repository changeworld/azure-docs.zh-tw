---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694322"
---
## <a name="provide-frames-to-the-session"></a>提供工作階段的畫面

空間錨點工作階段的運作方式為對應使用者周圍的空間。 這麼做有助於判斷錨點的所在位置。 行動平台 (iOS 和 Android) 需要對觀景窗摘要進行原生呼叫，才能從您平台的 AR 媒體櫃取得畫面。 相反地，HoloLens 會持續掃描環境，因此像在行動裝置上無須特定呼叫。