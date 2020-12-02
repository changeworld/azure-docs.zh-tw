---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993067"
---
## <a name="locate-a-cloud-spatial-anchor"></a>尋找雲端空間錨點

使用 Azure Spatial Anchors 的主要原因之一，是為了要找出先前儲存的雲端空間錨點。 您可透過幾種不同的方法找出雲端空間錨點。 您一次只能對監看員使用一種策略。
- 依識別碼尋找錨點。
- 尋找與先前找到的錨點連線的錨點。 您可以在[這裡](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md)了解錨點關聯性。
- 使用[粗略重新定位](../articles/spatial-anchors/concepts/coarse-reloc.md)尋找錨點。

> [!NOTE]
> 每當您找到錨點時，Azure Spatial Anchors 就會嘗試使用收集到的環境資料來增加錨點的視覺效果資訊。 如果您找不到錨點，建議您在建立錨點後，以不同的角度和光線條件尋找數次。

如果您是依識別碼尋找雲端空間錨點，您可以將雲端空間錨點識別碼儲存在應用程式的後端服務中，並且讓所有能夠正確通過驗證的裝置都能加以存取。 如需這類範例，請參閱[教學課程：跨裝置共用空間錨點](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md)。

具現化 `AnchorLocateCriteria` 物件、設定您要尋找的識別碼，並提供您的 `AnchorLocateCriteria` 藉以在工作階段上叫用 `CreateWatcher` 方法。