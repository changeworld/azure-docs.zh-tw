---
title: Azure 對等互連服務連線遙測
description: 深入瞭解 Microsoft Azure 對等互連服務連線遙測
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871298"
---
# <a name="peering-service-connection-telemetry"></a>對等互連服務連線遙測

連線遙測會提供針對客戶位置與 Microsoft 網路之間的連線所收集的深入解析。 客戶可以藉由在 Azure 入口網站中註冊連線，取得 Azure 對等服務連線的遙測。 這項功能可提供網路效能的前置詞安全性和深入解析。


連接遙測是由下列範圍組成：

### <a name="latency-measurement"></a>延遲測量

 延遲會從用戶端測量到 Microsoft Edge PoP，以取得對等互連服務內已註冊的首碼。

### <a name="route-prefix-monitoring-and-protection"></a>路由前置詞監視和保護

路由路徑會針對任何在事件記錄檔中捕捉到的可疑活動進行監視。 例如，建立事件記錄檔的原因如下：

- 首碼劫持
- 首碼提款
- 路由流失

## <a name="next-steps"></a>後續步驟

- 若要深入了解對等互連服務連線，請參閱[對等互連服務連線](connection.md)。
- 若要讓對等互連服務連線上線，請參閱[將對等服務模型](onboarding-model.md)上線。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。
