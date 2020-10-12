---
title: Azure 對等互連服務連線遙測
description: 瞭解 Microsoft Azure 對等互連服務連線遙測
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871298"
---
# <a name="peering-service-connection-telemetry"></a>對等互連服務連接遙測

連線遙測會提供針對客戶位置與 Microsoft 網路之間的連線所收集的深入解析。 客戶可以在 Azure 入口網站中註冊連線，以取得 Azure 對等互連服務連線的遙測。 這項功能可提供首碼安全性和網路效能的見解。


連接遙測是由下列範圍所組成：

### <a name="latency-measurement"></a>延遲測量

 延遲是從用戶端到對等互連服務中註冊的前置詞的 Microsoft Edge PoP 進行測量。

### <a name="route-prefix-monitoring-and-protection"></a>路由前置詞監視和保護

系統會針對任何在事件記錄檔中捕捉的可疑活動監視路由路徑。 例如，系統會針對其中一些因素建立事件記錄：

- 首碼劫持
- 前置詞提款
- 路由洩漏

## <a name="next-steps"></a>後續步驟

- 若要深入了解對等互連服務連線，請參閱[對等互連服務連線](connection.md)。
- 若要讓對等互連服務連線上線，請參閱 [將對等互連服務模型](onboarding-model.md)上線。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。
