---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67173680"
---
## <a name="setting-up-the-library"></a>設定程式庫

叫用 Start()，讓您的工作階段能夠處理環境資料。

若要處理您的工作階段所引發的事件，請將工作階段的 `delegate` 屬性設定為物件，例如您的檢視。 此物件必須實作 SSCCloudSpatialAnchorSessionDelegate 通訊協定。
