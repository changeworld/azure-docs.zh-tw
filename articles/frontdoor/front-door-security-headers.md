---
title: 使用規則引擎新增安全性標頭 - Azure Front Door
description: 本文會教您如何透過 Azure Front Door 上的規則引擎來設定安全性標頭
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: duau
ms.openlocfilehash: ad1e8a8a2162ece69af9904d76a394d4bad5de23
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399135"
---
# <a name="add-security-headers-with-rules-engine"></a>使用規則引擎新增安全性標頭

實作安全性標頭以防止以瀏覽器為基礎的漏洞，例如 HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy 或 X-Frame-Options。 以安全性為基礎的屬性也可以使用 Cookie 來定義。

下列範例示範如何將 Content-Security-Policy 標頭新增至所有符合規則引擎組態相關聯路由中所定義路徑的傳入要求。 在這裡我們只允許來自信任網站 **https://apiphany.portal.azure-api.net** 的指令碼在我們的應用程式上執行。

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>在 Azure 入口網站中新增 Content-Security-Policy 標頭

1. 如果這是您第一次使用 AFD 或規則引擎功能，在建立此特定規則之前，請先了解如何[建立 Front Door](quickstart-create-front-door.md) 或如何[建立規則引擎](front-door-tutorial-rules-engine.md)。

2. 按一下 [新增] 以新增新的規則。 提供規則的名稱，然後按一下 [新增動作]  >  [回應標頭]。

3. 將運算子設定為 [附加] 以新增此標頭，作為對此路由所有傳入要求的回應。

4. 新增標頭名稱：**Content-Security-Policy**，並且定義此標頭應該接受的值。 在此案例中，我們選擇「*script-src 'self' https://apiphany.portal.azure-api.net 」。*

5. 將所有想要的規則新增至您的組態之後，請別忘了移至慣用的路由，並且將您的規則引擎組態與您的路由規則產生關聯。 必須執行此步驟，才能讓規則生效。 

![入口網站範例](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> 在此案例中，我們不會將[比對條件](front-door-rules-engine-match-conditions.md)新增至規則。 所有符合路由規則中所定義路徑的傳入要求都會套用此規則。 如果您只想要將規則套用到這些要求的子集，請務必將您的特定比對條件新增至此規則。


## <a name="next-steps"></a>後續步驟

- 深入了解 [AFD 規則引擎](front-door-rules-engine.md)。 
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
- 深入了解規則引擎[比對條件](front-door-rules-engine-match-conditions.md)
- 如需詳細資訊，請參閱 AFD 規則引擎 [CLI 參考](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)。 
- 如需詳細資訊，請參閱 AFD 規則引擎 [PowerShell 參考](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)。 
