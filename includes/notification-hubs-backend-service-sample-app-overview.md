---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095172"
---
其可讓您透過所建立的後端服務，從通知中樞註冊和取消註冊。 

如果指定了動作且應用程式在前景中，則會顯示警示。 否則，通知會顯示在通知中心。

> [!NOTE]
> 您通常會在應用程式生命週期中的適當時間點 (或作為第一次執行體驗的一部分) 來執行註冊 (和取消註冊) 動作，而不需要明確輸入使用者註冊/取消註冊的相關內容。 不過，此範例需要明確的使用者輸入，以便更輕鬆地探索和測試此功能。
