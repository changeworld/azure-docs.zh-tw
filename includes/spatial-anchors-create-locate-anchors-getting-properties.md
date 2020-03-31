---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "67173690"
---
錨點在服務上建立後，您就無法更新其位置 - 您必須建立新的錨點並刪除舊的錨定，以追蹤新的位置。

如果您不需要尋找錨點以更新其屬性，您可以使用 `GetAnchorPropertiesAsync()` 方法，以傳回具有屬性的 `CloudSpatialAnchor` 物件。
