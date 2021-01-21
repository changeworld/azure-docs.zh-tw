---
title: 使用 Azure AD App Proxy 的內部部署應用程式以標頭為基礎的單一登入
description: 瞭解如何為使用標頭型驗證保護的內部部署應用程式提供單一登入。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c30d8a66cf9deffaa32e5ec0356a9067575b77e8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660697"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>使用 Azure AD App Proxy (Preview 的內部部署應用程式以標頭為基礎的單一登入) 

Azure Active Directory (Azure AD) 應用程式 Proxy 原本就支援使用標頭進行驗證之應用程式的單一登入存取。 您可以在 Azure AD 中設定應用程式所需的標頭值。 標頭值將會透過應用程式 Proxy 向下傳送至應用程式。 使用原生支援進行以標頭為基礎之驗證與應用程式 Proxy 的一些優點包括：  

* **簡化提供對內部部署應用程式遠端存取** 的功能-應用程式 Proxy 可讓您簡化現有的遠端存取架構。 您可以將 VPN 存取權取代為這些應用程式。 您也可以移除內部部署身分識別解決方案的相依性以進行驗證。 當使用者登入以使用您的公司應用程式時，您的使用者不會注意到什麼不同。 這些還是可以在任何裝置上從任何地方運作。  

* 您的 **應用程式不需要額外的軟體或變更**，您可以使用現有的應用程式 Proxy 連接器，也不需要安裝任何其他軟體。  

* **可用的屬性和轉換的廣泛清單** -所有可用的標頭值都是以 Azure AD 發出的標準宣告為基礎。 可以用來設定 [SAML 或 OIDC 應用程式宣告的](../develop/active-directory-saml-claims-customization.md#attributes) 所有屬性和轉換，也可以當做標頭值使用。 

## <a name="pre-requisites"></a>必要條件
開始使用以標頭為基礎的驗證應用程式的單一登入之前，請確定您的環境已備妥下列設定和設定：
- 您需要啟用應用程式 Proxy，並將具有網站的連接器安裝到您的應用程式。 請參閱教學課程： [新增內部部署應用程式以透過應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) ，以瞭解如何準備您的內部部署環境、安裝及註冊連接器，以及測試連接器。 

## <a name="supported-capabilities"></a>支援的功能

下表列出應用程式 Proxy 支援的標頭型驗證應用程式所需的一般功能。 

|需求   |描述|
|----------|-----------|
|同盟 SSO |在預先驗證模式中，所有應用程式都會受到 Azure AD authentication 的保護，並讓使用者能夠使用單一登入。 |
|遠端存取 |應用程式 Proxy 可讓您從遠端存取應用程式。 使用者可以在任何瀏覽器上使用外部 URL 從網際網路存取應用程式。 應用程式 Proxy 不適合公司存取用途。 |
|以標頭為基礎的整合 |應用程式 Proxy 會執行 SSO 與 Azure AD 的整合，然後將身分識別或其他應用程式資料當作 HTTP 標頭傳遞給應用程式。 |
|應用程式授權 |您可以根據所存取的應用程式、使用者的群組成員資格和其他原則來指定一般原則。 在 Azure AD 中，會使用 [條件式存取](../conditional-access/overview.md)來執行原則。 應用程式授權原則僅適用于初始驗證要求。 |
|逐步驗證 |您可以定義原則來強制新增驗證，例如取得敏感性資源的存取權。 |
|更細緻的授權 |提供 URL 層級的存取控制。 您可以根據所存取的 URL 來強制執行已新增的原則。 針對應用程式設定的內部 URL 會定義套用原則的應用程式範圍。 會強制執行為最細微路徑設定的原則。  |

> [!NOTE] 
> 本文將說明如何使用應用程式 Proxy 將標頭型驗證應用程式連接到 Azure AD，且這是建議的模式。 另一種方法是使用 PingAccess 搭配 Azure AD 來啟用標頭型驗證的整合模式。 如需詳細資訊，請參閱 [使用應用程式 Proxy 和 PingAccess 進行單一登入的標頭型驗證](application-proxy-ping-access-publishing-guide.md)。

## <a name="how-it-works"></a>運作方式

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="以標頭為基礎的單一登入如何與應用程式 Proxy 搭配運作。" lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. 系統管理員會在 Azure AD 入口網站中自訂應用程式所需的屬性對應。 
2. 當使用者存取應用程式時，應用程式 Proxy 可確保使用者已通過驗證 Azure AD 
3. 應用程式 Proxy 雲端服務可感知所需的屬性。 因此，服務會從驗證期間收到的識別碼權杖中提取對應的宣告。 接著，服務會將這些值轉譯為所需的 HTTP 標頭，作為連接器要求的一部分。 
4. 接著，要求會傳遞至連接器，然後傳遞給後端應用程式。 
5. 應用程式會接收標頭，而且可以視需要使用這些標頭。 

## <a name="publish-the-application-with-application-proxy"></a>使用應用程式 Proxy 發佈應用程式

1. 根據 [ [使用應用程式 Proxy 發佈應用](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)程式] 中所述的指示，發行您的應用程式。  
    - 內部 URL 值會決定應用程式的範圍。 如果您在應用程式的根路徑設定內部 URL 值，則根目錄底下的所有子路徑都會收到相同的標頭設定和其他應用程式設定。 
    - 建立新的應用程式，以設定不同的標頭設定或使用者指派，以提供比您設定的應用程式更細微的路徑。 在新的應用程式中，使用您需要的特定路徑來設定內部 URL，然後設定此 URL 所需的特定標頭。 應用程式 Proxy 一律會將您的設定設定為針對應用程式所設定的最細微路徑。 

2. 選取 **Azure Active Directory**   作為 **預先驗證方法**。 
3. 藉由流覽至 [ **使用者和群組** ] 並指派適當的使用者和群組，指派測試使用者。 
4. 開啟瀏覽器並流覽至 [應用程式 Proxy] 設定中的 [ **外部 URL**]   。 
5. 確認您可以連接至應用程式。 雖然您可以連接，但因為未設定標頭，所以您無法存取應用程式。 

## <a name="configure-single-sign-on"></a>設定單一登入 
開始使用以標頭為基礎的應用程式的單一登入之前，您應該已安裝應用程式 Proxy 連接器，而且連接器可以存取目標應用程式。 如果沒有，請依照教學課程 [： Azure AD 應用程式 Proxy](application-proxy-add-on-premises-application.md)中的步驟執行，   然後回到這裡。 

1. 當您的應用程式出現在企業應用程式清單中時，請選取它，然後選取 [ **單一登入**]。 
2. 將單一登入模式設定為以 **標頭為基礎**。 
3. 在 [基本設定] 中，預設會選取 [ **Azure Active Directory**]。 
4. 選取標頭中的 [編輯鉛筆]，以設定要傳送至應用程式的標頭。 
5. 選取 [ **加入新的標頭**]。 提供標頭的 **名稱** ，並選取 [ **屬性** ] 或 [ **轉換** ]，然後從下拉式清單中選取您的應用程式所需的標頭。  
    - 若要深入瞭解可用屬性的清單，請參閱 [宣告自訂-屬性](../develop/active-directory-saml-claims-customization.md#attributes)。 
    - 若要深入瞭解可用的轉換清單，請參閱 [宣告自訂-宣告轉換](../develop/active-directory-saml-claims-customization.md#claim-transformations)。 
    - 您也可以新增 **群組標頭**，以傳送使用者所屬的所有群組，或指派給應用程式的群組作為標頭。 若要深入瞭解將群組設定為值的詳細資訊，請參閱： [設定應用程式的群組宣告](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration)。 
6. 選取 [儲存]。 

## <a name="test-your-app"></a>測試應用程式 

當您完成所有這些步驟之後，您的應用程式應該會執行並可供使用。 若要測試應用程式： 
1. 開啟新的瀏覽器或私用瀏覽器視窗，確認已清除先前快取的標頭。 然後，流覽至 [應用程式 Proxy] 設定中的 [ **外部 URL**]   。
2. 使用您指派給應用程式的測試帳戶來登入。 如果您可以使用 SSO 載入並登入應用程式，那麼您就很好！ 

## <a name="considerations"></a>考量

- 應用程式 Proxy 是用來提供遠端存取內部部署或私人雲端上的應用程式。 不建議應用程式 Proxy 處理源自公司網路內部的流量。
- 以標頭為基礎的驗證應用程式的存取權應該限制為僅限來自連接器的流量或其他允許的標頭型驗證解決方案。 這通常是透過使用應用程式伺服器上的防火牆或 IP 限制來限制對應用程式的網路存取而完成。

## <a name="next-steps"></a>下一步

- [什麼是單一登入？](what-is-single-sign-on.md)
- [什麼是應用程式 proxy？](what-is-application-proxy.md)
- [應用程式管理快速入門系列](view-applications-portal.md)
