---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006490"
---
## <a name="setting-up-the-library"></a>設定程式庫

叫用 `Start()`，讓您的工作階段能夠處理環境資料。

若要處理您的工作階段所引發的事件，請將工作階段的 `delegate` 屬性設定為物件，例如您的檢視。 此物件必須實作 `SSCCloudSpatialAnchorSessionDelegate` 通訊協定。
