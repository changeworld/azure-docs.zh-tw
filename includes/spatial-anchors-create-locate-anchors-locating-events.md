---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993021"
---
監看員建立後，系統會針對每個要求的錨點引發 `AnchorLocated` 事件。 在找到錨點時，或找不到錨點時，都會引發此事件。 如果發生這種情況，將會在狀態中指出原因。 在處理完監看員的所有錨點後 (包括找到和找不到的)，就會引發 `LocateAnchorsCompleted` 事件。 每個監看員有 35 個識別碼的限制。 
