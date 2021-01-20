---
title: Azure AD Connect cloud sync 深入探討-運作方式
description: 本主題提供有關雲端同步運作方式的深入探討。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613415"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>雲端同步深入探討-運作方式

## <a name="overview-of-components"></a>元件概觀

![運作方式](media/concept-how-it-works/how-1.png)

雲端同步是以 Azure AD 服務為基礎，並有2個主要元件：

- 布建 **代理程式**： Azure AD Connect 的雲端布建代理程式與 Workday 輸入的代理程式相同，並以與應用程式 proxy 相同的伺服器端技術為基礎，並通過驗證。 它只需要和輸出連接，代理程式會自動更新。 
- 布建 **服務**：與輸出布建和 Workday 輸入布建相同的布建服務，其使用以排程器為基礎的模型。 如果是雲端同步處理，則會每隔2分鐘布建一次變更。


## <a name="initial-setup"></a>初始設定
在初始設定期間，會完成幾項工作，以進行雲端同步處理。  這些警告是： 

- **在代理程式安裝期間**：您要為您要布建的 AD 網域設定代理程式。  此設定會在混合式身分識別服務中註冊網域，並為接聽要求的服務匯流排建立輸出連線。
- **當您啟用** 布建時：選取 AD 網域，並啟用每2分鐘執行一次的布建。 （選擇性）您可以取消選取密碼雜湊同步處理並定義通知電子郵件。 您也可以使用 Microsoft Graph Api 來管理屬性轉換。


## <a name="agent-installation"></a>代理程式安裝
以下是安裝雲端布建代理程式時所發生狀況的逐步解說。

- 首先，安裝程式會安裝代理程式二進位檔，以及在虛擬服務帳戶下執行的代理程式服務 (網路 SERVICE\AADProvisioningAgent) 。  虛擬服務帳戶是特殊的帳戶類型，這種帳戶沒有密碼，並且是由 Windows 進行管理。
- 然後，安裝程式會啟動精靈。
- Wizard 會提示您輸入 Azure AD 認證，接著會驗證並取出權杖。
- 然後，嚮導會要求目前的電腦網域系統管理員認證。
- 使用這些認證時，會建立或找出此網域 (GMSA) 的代理程式一般受管理的服務帳戶，並重複使用（如果已存在）。
- 代理程式服務現在已重新設定為在 GMSA 下執行。
- 此 wizard 現在會要求您提供網域設定，以及企業系統管理員 (EA) /Domain Admin (DA) 帳戶，用於您希望代理程式服務的每個網域。
- GMSA 帳戶接著會以可讓其存取上述輸入的每個網域的許可權進行更新。
- 接下來，嚮導會觸發代理程式註冊
- 代理程式會建立憑證，並使用 Azure AD 權杖，將本身和混合式身分識別服務的憑證註冊 (他) 註冊服務
- Wizard 會觸發 AgentResourceGrouping 呼叫。 此對其管理服務的呼叫會將代理程式指派給他設定中的一或多個 AD 網域。
- Wizard 現在會重新開機 agent 服務。
- 代理程式會在重新開機時呼叫啟動程式服務 (以及之後每隔10分鐘) 檢查是否有設定更新。  啟動程式服務會驗證代理程式身分識別。  它也會更新上次啟動的時間。  這點很重要，因為如果代理程式未啟動，則它們不會取得更新的服務匯流排端點，而且可能無法接收要求。 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>什麼是跨網域身分識別管理系統 (SCIM)？

[SCIM 規格](https://tools.ietf.org/html/draft-scim-core-schema-01)是一種標準，可用來自動交換身分識別網域（例如 Azure AD）之間的使用者或群組身分識別資訊。 SCIM 逐漸成為佈建的既定標準；與 SAML 或 OpenID Connect 等同盟標準搭配使用時，SCIM 可為管理員提供端對端、以標準為基礎的存取管理解決方案。

Azure AD Connect 雲端布建代理程式使用 SCIM 搭配 Azure AD 來布建和取消布建使用者和群組。

## <a name="synchronization-flow"></a>同步處理流程
![](media/concept-how-it-works/provisioning-4.png)一旦您已安裝代理程式並啟用布建，就會發生下列流程。

1.  一旦設定之後，Azure AD 布建服務會呼叫 Azure AD 混合式服務，以將要求新增至服務匯流排。 代理程式會持續維護對服務匯流排的輸出連線，以接聽要求並挑選系統以進行跨網域身分識別管理 (SCIM) 要求。 
2.  代理程式會根據物件類型，將要求細分為個別的查詢。 
3.  AD 會將結果傳回給代理程式，而代理程式會先篩選這些資料，再將其傳送至 Azure AD。  
4.  Agent 會將 SCIM 回應傳回 Azure AD。  這些回應是以代理程式內發生的篩選為基礎。  代理程式使用範圍來篩選結果。 
5.  布建服務會將變更寫入 Azure AD。
6. 如果這是差異同步處理，而不是完整同步處理，則會使用 cookie/水位線。 新的查詢會從該 cookie/水位線開始進行變更。

## <a name="supported-scenarios"></a>支援的案例：
雲端同步支援下列案例。


- **具有新樹系的現有混合式客戶**： Azure AD Connect 同步處理用於主要樹系。 雲端同步處理可用來從 AD 樹系進行布建， (包括已中斷連線的) 。 如需詳細資訊，請參閱這裡的教學 [課程](tutorial-existing-forest.md)。

 ![現有的混合式](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **新的混合式客戶**：未使用 Azure AD Connect 同步。 雲端同步處理可用來從 AD 樹系進行布建。  如需詳細資訊，請參閱這裡的教學 [課程](tutorial-single-forest.md)。
 
 ![新客戶](media/tutorial-single-forest/diagram-2.png)

- **現有的混合式客戶**： Azure AD Connect 同步處理用於主要樹系。 [我們](tutorial-existing-forest.md)會針對主要樹系中的一小部分使用者試驗雲端同步處理。

 ![現有的試驗](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

如需詳細資訊，請參閱 [支援的拓撲](plan-cloud-sync-topologies.md)。



## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)
