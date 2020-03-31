---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "67173700"
---
## <a name="pause-reset-or-stop-the-session"></a>暫停、重設或停止工作階段

若要暫時停止工作階段，您可以叫用 `Stop()`。 如此，即使您叫用 ProcessFrame()，也會停止任何監看員和環境處理。 接著，您可以叫用 `Start()` 以繼續處理。 繼續處理時，會保有工作階段中已擷取的環境資料。
