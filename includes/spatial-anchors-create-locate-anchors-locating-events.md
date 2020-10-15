---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "71180038"
---
監看員建立後，系統會針對每個要求的錨點引發 `AnchorLocated` 事件。 在找到錨點時，或找不到錨點時，都會引發此事件。 如果發生這種情況，將會在狀態中指出原因。 在處理完監看員的所有錨點後 (包括找到和找不到的)，就會引發 `LocateAnchorsCompleted` 事件。 每個監看員有 35 個識別碼的限制。 
