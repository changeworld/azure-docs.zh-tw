---
title: 適用于 App Service 的 Azure Defender-優點和功能
description: 瞭解適用于 App Service 的 Azure Defender 的優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d15e881ca1246d463d8054fe5bd094efaff0cdd5
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977183"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>適用于 App Service 的 Azure Defender 簡介

Azure App Service 是完全受控的平臺，可建立和裝載您的 web 應用程式和 Api，而不需擔心管理基礎結構。 它提供管理、監視和 operational insights，以符合企業級的效能、安全性和合規性需求。 如需詳細資訊，請參閱 [Azure App Service](https://azure.microsoft.com/services/app-service/)。

**適用于 App Service 的 Azure Defender** 會使用雲端的規模，以找出以透過 App Service 執行的應用程式為目標的攻擊。 攻擊者會探查 Web 應用程式，以找出弱點並加以入侵。 在路由到特定環境之前，對執行於 Azure 中的應用程式提出的要求會經過數個閘道，並在其中進行檢查和記錄。 接著，此資料會用來識別入侵和攻擊者，並了解後續將使用的新模式。

利用 Azure 作為雲端提供者所具備的可見度，資訊安全中心可分析 App Service 內部記錄，以識別多個目標的攻擊方法。 例如，這些方法包括廣泛的掃描和分散式攻擊。 這種類型的攻擊通常來自於一小部分的 IP，且其模式為在多部主機上搜耙至類似端點。 攻擊會搜尋易受攻擊的頁面或外掛程式，且無法從單一主機的觀點來識別。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|預覽|
|定價：|[適用于 App Service 的 Azure Defender](azure-defender.md)會依[定價頁面上的](security-center-pricing.md)顯示方式計費|
|支援的 App Service 方案：|![是 ](./media/icons/yes-icon.png) Basic、Standard、Premium、隔離式或 Linux<br>![](./media/icons/no-icon.png)免費、共用或耗用量<br>[深入瞭解 App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|雲端：|![Yes](./media/icons/yes-icon.png) 商業雲端<br>![No](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Azure Defender 的 App Service 保護哪些專案？

啟用 App Service 計畫之後，資訊安全中心會評估您 App Service 方案所涵蓋的資源，並根據其結果產生安全性建議。 「安全性中心」會保護您的 App Service 執行所在的 VM 實例和管理介面。 它也會監視傳送給在 App Service 中執行之應用程式的要求和回應，以及從這些應用程式傳送的要求和回應。

如果您執行以 Windows 為基礎的 App Service 方案，資訊安全中心也可存取基礎沙箱和 VM。 搭配前述的記錄資料，基礎結構也可勾勒出攻擊的概況，包括到處肆虐的新式攻擊，以及對客戶電腦的入侵。 因此，即使是在 Web 應用程式遭到惡意探索後才部署資訊安全中心，還是有可能偵測到進行中的攻擊。


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>保護您的 Azure App Service Web 應用程式和 API
使用適用于 App Service 的 Azure Defender 保護您的 Azure App Service 方案：

- 確定您有與專用電腦相關聯的支援 App Service 方案。 支援的方案列示于上述的 [可用性](#availability)。

- 在您的訂用帳戶上啟用**Azure defender** (您可以選擇性地啟用 App Service 方案) 的**azure defender** ，如[Azure 資訊安全中心定價](security-center-pricing.md)所述

安全性中心與 App Service 的原生整合，不需要部署和上架-整合是透明的。

>[!NOTE]
> [定價和設定] 頁面會列出您 **資源數量**的一些實例。 這代表此訂用帳戶上所有 App Service 方案中的計算實例總數，在您開啟 [定價層] 頁面時正在執行。
>
> Azure App Service 提供各種不同的方案。 您的 App Service 方案會定義要執行之 web 應用程式的一組計算資源。 這些都相當於傳統 web 裝載中的伺服器陣列。 一或多個應用程式可設定為在相同的計算資源上執行 (或在相同的 App Service 方案中執行)。
>
>若要驗證計數，請前往 Azure 入口網站中的 [App Service 方案]，您可以在其中查看每個方案所使用的計算實例數目。 



## <a name="next-steps"></a>下一步

在本文中，您已瞭解 Azure Defender 的 App Service。 

如需相關內容，請參閱下列文章： 

- 無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。
- 如需 Azure App Service 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureappserv)。
- 如需 App Service 方案的詳細資訊，請參閱 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。
- > [!div class="nextstepaction"]
    > [啟用 Azure Defender](security-center-pricing.md)