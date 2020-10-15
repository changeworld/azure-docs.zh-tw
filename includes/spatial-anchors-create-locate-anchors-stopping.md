---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006500"
---
## <a name="pause-reset-or-stop-the-session"></a>暫停、重設或停止工作階段

若要暫時停止工作階段，您可以叫用 `Stop()`。 如此，即使您叫用 `ProcessFrame()`，也會停止任何監看員和環境處理。 接著，您可以叫用 `Start()` 以繼續處理。 繼續處理時，會保有工作階段中已擷取的環境資料。
