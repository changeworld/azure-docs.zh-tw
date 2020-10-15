---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694610"
---
## <a name="provide-feedback-to-the-user"></a>將意見反應提供給使用者

您可以撰寫程式碼以處理工作階段更新的事件。 每當工作階段增進其對環境的了解時，就會引發此事件。 這麼做可讓您：

- 在裝置移動時，使用 `UserFeedback` 類別將意見反應提供給使用者，且工作階段會更新其對環境的了解。 若要這樣做：
- 判斷何時有足夠的追蹤空間資料可建立空間錨點。 您可以使用 `ReadyForCreateProgress` 或 `RecommendedForCreateProgress` 進行此判斷。 一旦 `ReadyForCreateProgress` 高於1，即表示有足夠的資料可儲存雲端空間錨點，但建議您等到 `RecommendedForCreateProgress` 高於 1 時再執行此作業。
