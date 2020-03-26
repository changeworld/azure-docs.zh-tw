---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76887876"
---
## <a name="locate-a-cloud-spatial-anchor"></a>尋找雲端空間錨點

使用 Azure Spatial Anchors 的主要原因之一，是為了要找出先前儲存的雲端空間錨點。 您可透過幾種不同的方法找出雲端空間錨點。 您一次只能對監看員使用一種策略。
- 依識別碼尋找錨點。
- 尋找與先前找到的錨點連線的錨點。 您可以在[這裡](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)了解錨點關聯性。
- 使用[粗略重新定位](/azure/spatial-anchors/concepts/coarse-reloc/)尋找錨點。

如果您是依識別碼尋找雲端空間錨點，您可以將雲端空間錨點識別碼儲存在應用程式的後端服務中，並且讓所有能夠正確通過驗證的裝置都能加以存取。 如需這類範例，請參閱[教學課程：跨裝置共用空間錨點](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)。

具現化 `AnchorLocateCriteria` 物件、設定您要尋找的識別碼，並提供您的 `AnchorLocateCriteria` 藉以在工作階段上叫用 `CreateWatcher` 方法。