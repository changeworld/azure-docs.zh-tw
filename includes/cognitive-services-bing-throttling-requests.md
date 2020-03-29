---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174263"
---
服務與您的訂用帳戶類型將決定您所適用的每秒查詢數目 (QPS)。 請確定您的應用程式包含維持在配額範圍內所需的邏輯。 如果已達到或超過 QPS 限制，要求即會失敗，且會傳回 HTTP 429 狀態碼。 回應會包含 `Retry-After` 標頭，指出您必須等待多久後才能傳送另一個要求。

## <a name="denial-of-service-versus-throttling"></a>拒絕服務與節流

服務會區分拒絕服務 (DoS) 攻擊和 QPS 違規。 如果服務懷疑有 DoS 攻擊，要求仍會成功 (HTTP 狀態碼為「200 確定」)， 但回應本文會是空的。