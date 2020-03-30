---
title: 保護 Azure 應用服務 Web 應用和 API
description: 本文可説明您開始保護 Azure 應用服務 Web 應用和 Azure 安全中心中的 API。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616474"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>保護 Azure 應用服務 Web 應用和 API

Azure 應用服務是一個完全託管的平臺，用於構建和託管 Web 應用和 API，而不必擔心必須管理基礎結構。 它提供管理、監控和運營見解，以滿足企業級性能、安全性和合規性要求。 有關詳細資訊，請參閱[Azure 應用服務](https://azure.microsoft.com/services/app-service/)。

要為 Azure 應用服務方案啟用高級威脅保護，您必須：

* 訂閱 Azure 安全中心的標準定價層
* 啟用應用服務方案，如下所示。 安全中心與應用服務本機集成，無需部署和載入 - 集成是透明的。
* 制定與專用電腦關聯的應用服務方案。 支援的計畫包括：基本、標準、高級、隔離或 Linux。 安全中心不支援免費、共用或消費計畫。 有關詳細資訊，請參閱[應用服務方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。

啟用應用服務方案後，安全中心會評估應用服務方案涵蓋的資源，並根據調查結果生成安全建議。 安全中心保護應用服務運行的 VM 實例和管理介面。 它也會監視傳送給在 App Service 中執行之應用程式的要求和回應，以及從這些應用程式傳送的要求和回應。

安全中心利用雲的規模和 Azure 作為雲供應商的可見度來監視常見的 Web 應用攻擊。 安全中心可以發現對應用程式的攻擊並識別新出現的攻擊 - 即使攻擊者處於偵察階段，也會進行掃描，以識別多個 Azure 託管應用程式的漏洞。 作為 Azure 本機服務，安全中心還具有獨特的地位，可提供基於主機的安全分析，涵蓋此 PaaS 的基礎計算節點，使安全中心能夠檢測對已利用的 Web 應用程式的攻擊。 有關詳細資訊，請參閱[Azure 應用服務的威脅保護](threat-protection.md#app-services)。


## <a name="enabling-monitoring-and-protection-of-app-service"></a>啟用 App Service 監視和保護

1. 在 Azure 門戶中，選擇安全中心。
2. 轉到**定價&設置**並選擇訂閱。
3. 在 [定價層]**** 底下的 [應用程式服務]**** 資料列中，將方案切換至 [已啟用]****。

    [![在標準層訂閱中啟用應用服務](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> 為**資源數量**列出的實例數表示此訂閱上所有應用服務方案中的計算實例總數，在打開定價層邊欄選項卡時運行。
>
> Azure 應用服務提供各種計畫。 應用服務方案定義 Web 應用要運行的計算資源集。 這些等效于傳統 Web 託管中的伺服器場。 一或多個應用程式可設定為在相同的計算資源上執行 (或在相同的 App Service 方案中執行)。
>
>要驗證計數，請前往 Azure 門戶中的"應用服務方案"，您可以在其中查看每個計畫使用的計算實例數。 






若要停用您 App Service 的監視和建議，請重複此程序並將 **App Service** 方案切換至 [已停用]****。



## <a name="see-also"></a>另請參閱
在本文中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。 若要深入了解「Azure 資訊安全中心」，請參閱下列文章：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何在 Azure 資訊安全中心設定安全性設定。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [應用服務](security-center-virtual-machine-protection.md#app-services)：查看具有運行狀況摘要的應用服務環境的清單。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 安全博客](https://blogs.msdn.com/b/azuresecurity/)：查找有關 Azure 安全性和合規性的博客文章。
