---
title: 遠端存取本地應用 - Azure AD 應用程式代理
description: Azure Active Directory 應用程式 Proxy 為內部部署 Web 應用程式提供安全的遠端存取。 單一登入 Azure AD 後，使用者可以透過外部 URL 或內部應用程式入口網站存取雲端和內部部署應用程式。 例如，應用程式 Proxy 可以為遠端桌面、SharePoint、Teams、Tableau、Qlik 和企業營運 (LOB) 應用程式提供遠端存取和單一登入。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274842"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>透過 Azure Active Directory 應用程式 Proxy 遠端存取內部部署應用程式 

Azure Active Directory 應用程式 Proxy 為內部部署 Web 應用程式提供安全的遠端存取。 單一登入 Azure AD 後，使用者可以透過外部 URL 或內部應用程式入口網站存取雲端和內部部署應用程式。 例如，應用程式 Proxy 可以為遠端桌面、SharePoint、Teams、Tableau、Qlik 和企業營運 (LOB) 應用程式提供遠端存取和單一登入。

Azure AD 應用程式 Proxy：

- **用法簡單**。 使用者可以使用和 O365 以及其他與 Azure AD 整合之 SaaS 應用程式相同的存取方式，來存取內部部署應用程式。 您不需要變更或更新應用程式，即可使用應用程式 Proxy。 

- **安全**。 內部部署應用程式可以使用 Azure 的授權控制項和安全性分析。 例如，本地應用程式可以使用條件訪問和兩步驗證。 應用程式 Proxy 不需要您穿過防火牆開啟輸入連線。
 
- **符合成本效益**。 內部部署解決方案則一般需要您設定及維護周邊網路 (DMZ)、Edge Server 或其他複雜的基礎結構。 應用程式 Proxy 在雲端中執行，這使其更容易使用。 若要使用應用程式 Proxy，您不需要變更網路基礎結構，或在內部部署環境中安裝額外的設備。

## <a name="what-is-application-proxy"></a>什麼是應用程式 Proxy？
應用程式 Proxy 是 Azure AD 的一項功能，可讓使用者從遠端用戶端存取內部部署 Web 應用程式。 應用程式 Proxy 包括在雲端中執行的應用程式 Proxy 服務，和在內部部署伺服器上執行的應用程式 Proxy 連接器。 Azure AD、應用程式 Proxy 服務和應用程式 Proxy 連接器，可以將使用者登入權杖從 Azure AD 安全地傳遞至 Web 應用程式。

應用程式 Proxy 適用於：

* 使用[整合式 Windows 驗證](application-proxy-configure-single-sign-on-with-kcd.md)來進行驗證的 Web 應用程式  
* 使用表單架構或[標頭型](application-proxy-configure-single-sign-on-with-ping-access.md)存取的 Web 應用程式  
* 您想要公開給不同裝置上豐富應用程式的 Web API  
* 裝載在[遠端桌面閘道](application-proxy-integrate-with-remote-desktop-services.md)之後的應用程式  
* 與 Active Directory Authentication Library (ADAL) 整合的豐富型用戶端應用程式

應用程式 Proxy 支援單一登入。 如需有關支援的方法的詳細資訊，請參閱[選擇單一登入方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。

建議使用應用程式代理來允許遠端使用者訪問內部資源。 應用程式代理取代了對 VPN 或反向代理的需求。 它不適用於公司網路上的內部使用者。  這些不必要地使用應用程式代理的使用者可能會帶來意外和不受歡迎的性能問題。

## <a name="how-application-proxy-works"></a>應用程式 Proxy 的運作方式為何

下圖顯示顯示 Azure AD 和應用程式 Proxy 一起運作，以向內部部署應用程式提供單一登入的方式。

![Azure AD 應用程式 Proxy 圖表](./media/application-proxy/azureappproxxy.png)

1. 使用者透過端點存取應用程式之後，系統會將使用者導向至 Azure AD 登入頁面。 
2. 成功登入之後，Azure AD 會向使用者的用戶端裝置傳送權杖。
3. 用戶端會將權杖傳送至應用程式 Proxy 服務，該服務會取出權杖的使用者主體名稱 (UPN) 和安全性主體名稱 (SPN)。 然後，應用程式 Proxy 會將要求傳送至應用程式 Proxy 連接器。
4. 如果您已設定單一登入，則連接器會代表使用者執行其他任何所需的驗證。
5. 連接器會將要求傳送至內部部署應用程式。  
6. 回應會透過應用程式 Proxy 服務與連接器傳送給使用者。

| 元件 | 描述 |
| --------- | ----------- |
| 端點  | 端點可以是 URL 或[使用者入口網站](end-user-experiences.md)。 使用者可以藉由存取外部 URL，來連線網路外部的應用程式。 您網路內的使用者可以透過 URL 或使用者入口網站存取應用程式。 使用者存取這些端點的其中一個時，會在 Azure AD 中進行驗證，然後透過連接器路由至內部部署應用程式。|
| Azure AD | Azure AD 使用儲存在雲端中的租用戶目錄執行驗證。 |
| 應用程式 Proxy 服務 | 此應用程式 Proxy 服務作為 Azure AD 的一部分在雲端中執行。 它會將登入權杖從使用者傳遞至應用程式 Proxy 連接器時。 應用程式 Proxy 在請求上轉送任何可存取的標頭，並根據其通訊協定將標頭設定為用戶端 IP 位址。 如果對 Proxy 的連入要求中已經有該標頭，則將用戶端 IP 位址加入到以逗號分隔清單的結尾，該用戶端 IP 位址是標頭的值。|
| 應用程式 Proxy 連接器 | 連接器是位於網路內部 Windows 伺服器上執行的輕量型代理程式。 連接器管理雲端中的應用程式 Proxy 服務與內部部署應用程式之間的通訊。 連接器僅使用輸出連線，因此您不需要開啟任何輸入連接埠，或在 DMZ 中放置任何物件。 連接器是無狀態的，且在必要時會從雲端提取資訊。 如需連接器的詳細資訊，例如如何負載平衡和驗證，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)。|
| Active Directory (AD) | Active Directory 在內部部署執行以對網域帳戶執行驗證。 設定單一登入之後，連接器將與 AD 通訊以執行所需的任何額外的驗證。
| 內部部署應用程式 | 最後，使用者就能夠存取內部部署應用程式。 

## <a name="next-steps"></a>後續步驟
要開始使用應用程式代理，請參閱[教程：添加本地應用程式，以便通過應用程式代理進行遠端存取](application-proxy-add-on-premises-application.md)。 

如需最新消息，請參閱[應用程式 Proxy 部落格](https://blogs.technet.com/b/applicationproxyblog/)


