---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76963645"
---
若要將效能最大化，請針對每個函式應用程式使用個別的儲存體帳戶。 當您有 Durable Functions 或事件中樞觸發的函式時，這一點特別重要，這兩個函數都會產生大量的儲存體交易。 當您的應用程式邏輯與 Azure 儲存體（不論是直接（使用儲存體 SDK）或其中一個儲存體系結）互動時，您應該使用專用的儲存體帳戶。 例如，如果您有事件中樞觸發的函式將一些資料寫入 blob 儲存體，請使用兩個儲存體帳戶， &mdash; 一個用於函式應用程式，另一個用於函數所儲存的 blob。