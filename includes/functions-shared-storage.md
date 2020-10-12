---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963645"
---
若要將效能最大化，請針對每個函式應用程式使用個別的儲存體帳戶。 當您有 Durable Functions 或事件中樞觸發的函式時，這一點特別重要，這兩個函式都會產生大量的儲存體交易。 當您的應用程式邏輯與 Azure 儲存體互動 (不論是直接 (使用儲存體 SDK) 或透過其中一個儲存體繫結) 時，您應該使用專用的儲存體帳戶。 例如，若有事件中樞觸發的函式將一些資料寫入 Blob 儲存體，請使用兩個儲存體帳戶&mdash;一個用於函式應用程式，另一個用於函式所儲存的 Blob。