---
title: 保護您的 Azure App Service web 應用程式和 Api
description: 本文可協助您開始在 Azure 資訊安全中心中保護您的 Azure App Service web 應用程式和 Api。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616474"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>保護您的 Azure App Service web 應用程式和 Api

Azure App Service 是完全受控的平臺，可用於建立及裝載您的 web 應用程式和 Api，而不必擔心管理基礎結構的問題。 它提供管理、監視和營運見解，以符合企業級的效能、安全性和合規性需求。 如需詳細資訊，請參閱[Azure App Service](https://azure.microsoft.com/services/app-service/)。

若要為您的 Azure App Service 方案啟用「先進的威脅防護」，您必須：

* 訂閱 Azure 資訊安全中心的標準定價層
* 啟用 App Service 計畫，如下所示。 資訊安全中心與 App Service 原生整合，因而不需要部署和上線-整合是透明的。
* 具有與專用機器相關聯的 App Service 計畫。 支援的方案包括：基本、標準、Premium、隔離或 Linux。 資訊安全中心不支援「免費」、「共用」或「耗用量」方案。 如需詳細資訊，請參閱[App Service 計畫](https://azure.microsoft.com/pricing/details/app-service/plans/)。

啟用 App Service 方案後，資訊安全中心會評估 App Service 方案所涵蓋的資源，並根據其發現產生安全性建議。 資訊安全中心保護您的 App Service 在其中執行的 VM 實例和管理介面。 它也會監視傳送給在 App Service 中執行之應用程式的要求和回應，以及從這些應用程式傳送的要求和回應。

資訊安全中心利用雲端的規模，以及 Azure 做為雲端提供者的可見度，來監視常見的 web 應用程式攻擊。 資訊安全中心可以探索您應用程式的攻擊，並找出新興的攻擊-即使攻擊者是在偵察階段，掃描以識別多個 Azure 託管應用程式的弱點。 身為 Azure 原生服務，資訊安全中心也位於唯一的位置，可提供涵蓋此 PaaS 之基礎計算節點的主機型安全性分析，讓資訊安全中心能夠偵測到對已被利用的 web 應用程式的攻擊。 如需詳細資訊，請參閱[Azure App Service 的威脅防護](threat-protection.md#app-services)。


## <a name="enabling-monitoring-and-protection-of-app-service"></a>啟用 App Service 監視和保護

1. 在 [Azure 入口網站中，選擇 [資訊安全中心]。
2. 移至 [**定價] & 設定**]，然後選擇訂用帳戶。
3. 在 [定價層]**** 底下的 [應用程式服務]**** 資料列中，將方案切換至 [已啟用]****。

    [![在您的標準層訂用帳戶中啟用應用程式服務](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> 針對您的**資源數量**列出的實例數目，代表在您開啟 [定價層] 分頁時，在此訂用帳戶上所有 App Service 方案中的計算實例總數。
>
> Azure App Service 提供各種不同的方案。 您的 App Service 方案會定義要執行之 web 應用程式的一組計算資源。 這些就相當於傳統 web 裝載中的伺服器陣列。 一或多個應用程式可設定為在相同的計算資源上執行 (或在相同的 App Service 方案中執行)。
>
>若要驗證計數，請前往 Azure 入口網站中的 [App Service 方案]，您可以在其中看到每個方案所使用的計算實例數目。 






若要停用您 App Service 的監視和建議，請重複此程序並將 **App Service** 方案切換至 [已停用]****。



## <a name="see-also"></a>請參閱
在本文中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。 若要深入了解「Azure 資訊安全中心」，請參閱下列文章：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何在 Azure 資訊安全中心設定安全性設定。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [應用程式服務](security-center-virtual-machine-protection.md#app-services)：查看具有健康情況摘要的 app service 環境清單。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 安全性 blog](https://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與合規性的 Blog 文章。
