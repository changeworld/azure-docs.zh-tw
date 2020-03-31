---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963645"
---
要最大限度地提高性能，請使用每個函數應用的單獨存儲帳戶。 當您具有持久函數或事件中心觸發函數時，這一點尤其重要，這些函數都會生成大量存儲事務。 當應用程式邏輯直接（使用存儲 SDK）或通過其中一個存儲綁定與 Azure 存儲交互時，應使用專用存儲帳戶。 例如，如果您有事件中心觸發的函數將一些資料寫入 Blob 存儲，請使用兩個存儲帳戶&mdash;，一個用於函數應用，另一個用於函數存儲的 Blob。