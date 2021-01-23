---
title: 在 Azure Active Directory 中建立更具復原性的混合式驗證
description: 建立彈性混合式基礎結構的架構設計人員和 IT 系統管理員指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724754"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>打造混合式架構的復原能力

混合式驗證可讓使用者以內部部署的內部部署身分識別來存取雲端式資源。 混合式基礎結構包含雲端和內部部署元件。

* 雲端元件包含 Azure AD、Azure 資源和服務、您組織的雲端式應用程式和 SaaS 應用程式。

* 內部部署元件包括內部部署應用程式、SQL 資料庫之類的資源，以及 Windows Server Active Directory 等身分識別提供者。 

> [!IMPORTANT]
> 當您在混合式基礎結構中規劃復原能力時，就是將相依性和單一失敗點降至最低的關鍵。 

Microsoft 提供三種混合式驗證機制。 這些選項會依復原順序列出。 建議您盡可能執行密碼雜湊同步處理。

* [密碼雜湊同步](../hybrid/whatis-phs.md) 處理 (PHS) 使用 Azure AD Connect，將身分識別和密碼雜湊的雜湊同步處理至 Azure AD，讓使用者能夠以內部部署的密碼為基礎，登入雲端式資源。 PHS 只有同步處理的內部部署相依性，而不是用於驗證。

* [傳遞驗證](../hybrid/how-to-connect-pta.md) (PTA) 會將使用者重新導向至 Azure AD 以進行登入。 然後，使用者名稱和密碼會透過部署在公司網路中的代理程式，針對內部部署 Active Directory 進行驗證。 PTA 具有其 Azure AD PTA 代理程式的內部部署使用量，位於內部部署的伺服器上。

* [同盟](../hybrid/whatis-fed.md) 客戶會部署同盟服務，例如 AD FS，然後 Azure AD 驗證 federation service 所產生的 SAML 判斷提示。 同盟對內部部署基礎結構具有最高的相依性，因此會有更多的失敗點。 

   
您可以在組織中使用一或多個這些方法。 如需詳細資訊，請參閱[針對 Azure AD 混合式身分識別解決方案選擇正確的驗證方法](../hybrid/choose-ad-authn.md)。 本文包含可協助您決定方法的決策樹。

## <a name="password-hash-synchronization"></a>密碼雜湊同步處理

Azure AD 的最簡單且最具復原性的混合式驗證選項是 [密碼雜湊同步](../hybrid/whatis-phs.md) 處理，在處理驗證要求時，不會有任何內部部署身分識別基礎結構相依性。 一旦使用密碼雜湊的身分識別同步處理至 Azure AD，使用者就可以向雲端資源進行驗證，而不需要內部部署身分識別元件的相依性。 

![PHS 的架構圖](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

如果您選擇此驗證選項，當內部部署身分識別元件變成無法使用時，將不會發生中斷情況。 發生內部部署中斷的原因有很多，包括硬體故障、電源中斷、自然災難以及惡意程式碼攻擊。 

### <a name="how-do-i-implement-phs"></a>如何? 執行 PHS？

若要執行 PHS，請參閱下列資源：

* [使用 Azure AD Connect 來執行密碼雜湊同步處理](../hybrid/how-to-connect-password-hash-synchronization.md)

* [啟用密碼雜湊同步處理](../hybrid/how-to-connect-password-hash-synchronization.md)

如果您的需求是無法使用 PHS，請使用傳遞驗證。

## <a name="pass-through-authentication"></a>傳遞驗證

傳遞驗證相依于位於內部部署伺服器上的驗證代理程式。 持續連線（或服務匯流排）存在於 Azure AD 和內部部署 PTA 代理程式之間。 防火牆、裝載驗證代理程式的伺服器，以及內部部署 Windows Server Active Directory (或其他身分識別提供者) 都是潛在的失敗點。 

![PTA 的架構圖](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>如何? 執行 PTA？

若要執行傳遞驗證，請參閱下列資源。

* [傳遞驗證的運作方式](../hybrid/how-to-connect-pta-how-it-works.md)

* [傳遞驗證安全性深入探討](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [安裝 Azure AD 傳遞驗證](../hybrid/how-to-connect-pta-quick-start.md)

* 如果您使用 PTA，請定義 [高可用性拓撲](../hybrid/how-to-connect-pta-quick-start.md)。

 ## <a name="federation"></a>同盟

同盟牽涉到在 Azure AD 和 federation service 之間建立信任關係，包括端點交換、權杖簽署憑證，以及其他中繼資料。 當要求 Azure AD 時，它會讀取設定，並將使用者重新導向至設定的端點。 此時，使用者會與 federation service 互動，而此服務會發出 Azure AD 所驗證的 SAML 判斷提示。 

下圖顯示企業 Active Directory 同盟服務 (AD FS) 的部署，包括跨多個內部部署資料中心的重複同盟和 web 應用程式 proxy 伺服器。 此設定依賴商業網路基礎結構元件，例如 DNS、具有地理親和性功能的網路負載平衡、防火牆等等。所有內部部署元件和連線都很容易失敗。 如需詳細資訊，請造訪 [AD FS 容量規劃檔](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) 。

> [!NOTE]
>  同盟具有最高的內部部署相依性數目，因此是最可能的失敗點。 雖然此圖顯示 AD FS，但其他內部部署身分識別提供者會受到類似的設計考慮，以達成高可用性、擴充性及容錯移轉。

![同盟的架構圖](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>如何? 實施同盟？

如果您正在執行同盟驗證策略，或想要讓它更具彈性，請參閱下列資源。

* [什麼是同盟驗證](../hybrid/whatis-fed.md)

* [同盟如何運作](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD 同盟相容性清單](../hybrid/how-to-connect-fed-compatibility.md)

* 遵循 [AD FS 容量規劃檔](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [在 Azure IaaS 中部署 AD FS](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [啟用 PHS](../hybrid/tutorial-phs-backup.md) 搭配您的同盟

## <a name="next-steps"></a>後續步驟
系統管理員和架構設計人員的復原能力資源
 
* [使用認證管理建立恢復功能](resilience-in-credentials.md)

* [以裝置狀態建立恢復功能](resilience-with-device-states.md)

* [使用連續存取評估來建立恢復功能 (CAE) ](resilience-with-continuous-access-evaluation.md)

* [在外部使用者驗證中建立恢復功能](resilience-b2b-authentication.md)

* [使用應用程式 Proxy 來建立應用程式存取的復原能力](resilience-on-premises-access.md)

開發人員的復原能力資源

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)