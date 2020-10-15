---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173692"
---
## <a name="update-properties"></a>更新屬性

若要更新錨點的屬性，您可以使用 `UpdateAnchorProperties()` 方法。 如果有兩個或更多裝置同時嘗試更新相同錨點的屬性，我們會使用開放式並行模型。 這表示會以最先寫入者為準。  其他所有的寫入都會收到「並行」錯誤：必須在重新整理屬性後再試一次。
