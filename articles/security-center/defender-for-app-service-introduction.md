---
title: 適用於 App Service 的 Azure Defender - 優點和功能
description: 了解適用於 App Service 的 Azure Defender 有何優點和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c53f7e712668f32766feaf76d6a08582bda9af22
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449109"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>適用於 App Service 的 Azure Defender 簡介

Azure App Service 是完全受控的平台，可讓您建置及裝載 Web 應用程式和 API，而無須擔心管理基礎結構的問題。 此平台提供管理、監視和運作的深入解析，以符合企業級的效能、安全性和合規需求。 如需詳細資訊，請參閱 [Azure App Service](https://azure.microsoft.com/services/app-service/)。

**適用於 App Service 的 Azure Defender** 使用雲端規模，來識別將透過 App Service 執行的應用程式視為目標的攻擊。 攻擊者會探查 Web 應用程式，以找出弱點並加以入侵。 在路由到特定環境之前，對執行於 Azure 中的應用程式提出的要求會經過數個閘道，並在其中進行檢查和記錄。 接著，此資料會用來識別入侵和攻擊者，並了解後續將使用的新模式。

利用 Azure 作為雲端提供者所具備的可見度，資訊安全中心可分析 App Service 內部記錄，以識別多個目標的攻擊方法。 例如，這些方法包括廣泛的掃描和分散式攻擊。 這種類型的攻擊通常來自於一小部分的 IP，且其模式為在多部主機上搜耙至類似端點。 攻擊會搜尋易受攻擊的頁面或外掛程式，且無法從單一主機的觀點來識別。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽|
|定價：|[適用於 App Service 的 Azure Defender](azure-defender.md) 的計費方式如[定價頁面](security-center-pricing.md)所示|
|支援的 App Service 方案：|![是](./media/icons/yes-icon.png) 基本、標準、進階、隔離式或 Linux<br>![無](./media/icons/no-icon.png) 免費、共用或使用量<br>[深入了解 App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>適用於 App Service 的 Azure Defender 可保護哪些項目？

啟用 App Service 方案後，資訊安全中心會評估 App Service 方案所涵蓋的資源，並根據其發現產生安全性建議。 資訊安全中心會保護您 App Service 執行所在的 VM 執行個體及管理介面。 它也會監視傳送給在 App Service 中執行之應用程式的要求和回應，以及從這些應用程式傳送的要求和回應。

如果您執行以 Windows 為基礎的 App Service 方案，資訊安全中心也可存取基礎沙箱和 VM。 搭配前述的記錄資料，基礎結構也可勾勒出攻擊的概況，包括到處肆虐的新式攻擊，以及對客戶電腦的入侵。 因此，即使是在 Web 應用程式遭到惡意探索後才部署資訊安全中心，還是有可能偵測到進行中的攻擊。


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>保護您的 Azure App Service Web 應用程式和 API
若要使用適用於 App Service 的 Azure Defender 來保護您的 Azure App Service 方案：

- 確定您有受支援且與專用機器相關聯的 App Service 方案。 支援的方案如前述的[可用性](#availability)所列。

- 在您的訂用帳戶上啟用 **Azure Defender** (您可以選擇僅啟用**適用於 App Service 的 Azure Defender** 方案)，如 [Azure 資訊安全中心的定價](security-center-pricing.md)所說明

資訊安全中心會與 App Service 原生整合，因此無須進行部署及上線 - 整合程序是透明的。

>[!NOTE]
> [定價和設定] 頁面會列出 [資源數量] 的多個執行個體。 這代表在您開啟定價層頁面時，此訂用帳戶上所有 App Service 方案中正在執行的計算執行個體總數。
>
> Azure App Service 提供多種不同的方案。 App Service 方案會針對要執行的 Web 應用程式定義一組計算資源。 這些資源相當於傳統 Web 裝載中的伺服器陣列。 一或多個應用程式可設定為在相同的計算資源上執行 (或在相同的 App Service 方案中執行)。
>
>若要驗證計數，請前往 Azure 入口網站中的 [App Service 方案]，您將可看到每個方案所使用的計算執行個體數目。 



## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於 App Service 的 Azure Defender。 

如需相關內容，請參閱下列文章： 

- 無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。
- 如需 Azure App Service 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureappserv)。
- 如需 App Service 方案的詳細資訊，請參閱 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。
- > [!div class="nextstepaction"]
    > [啟用 Azure Defender](security-center-pricing.md)