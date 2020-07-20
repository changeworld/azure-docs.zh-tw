---
title: 主要重新整理權杖 (PRT) 和 Azure AD - Azure Active Directory
description: Azure Active Directory 中的主要重新整理權杖 (PRT) 是什麼角色，以及該如何管理？
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ccd51bd69c982aeae25dbf52d1e5d076542cf35
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771191"
---
# <a name="what-is-a-primary-refresh-token"></a>什麼是主要重新整理權杖？

主要重新整理權杖 (PRT) 是在 Windows 10、iOS 和 Android 裝置上進行 Azure AD 驗證的主要成品。 這是特別向 Microsoft 第一方權杖代理程式 (token broker) 發出的 JSON Web 權杖 (JWT)，用來在這些裝置上使用的應用程式之間啟用單一登入 (SSO)。 在本文中，我們將提供有關如何在 Windows 10 裝置上發出、使用和保護 PRT 的詳細資料。

本文假設您已了解 Azure AD 中可用的不同裝置狀態，以及單一登入在 Windows 10 中的運作方式。 如需有關 Azure AD 中裝置的詳細資訊，請參閱[什麼是 Azure Active Directory 中的裝置管理？](overview.md)一文

## <a name="key-terminology-and-components"></a>重要術語和元件

下列 Windows 元件在要求和使用 PRT 方面扮演著重要角色：

* **雲端驗證提供者** (CloudAP)：CloudAP 是 Windows 登入的新式驗證提供者，可驗證使用者是否已記錄到 Windows 10 裝置。 CloudAP 提供一個外掛程式架構，讓識別提供者能夠以此為建置基礎，使用該識別提供者的認證對 Windows 啟用驗證。
* **Web 帳戶管理員** (WAM)：WAM 是 Windows 10 裝置上的預設權杖代理程式。 WAM 也提供一個外掛程式架構，讓識別提供者能夠以此為建置基礎，並對依賴該識別提供者的應用程式啟用 SSO。
* **Azure AD CloudAP 外掛程式**：以 CloudAP 架構為基礎的 Azure AD 專屬外掛程式，可在 Windows 登入期間，使用 Azure AD 來驗證使用者認證。
* **Azure AD WAM 外掛程式**：以 WAM 架構為基礎的 Azure AD 專屬外掛程式，可對依賴 Azure AD 進行驗證的應用程式啟用 SSO。
* **Dsreg**：Windows 10 上的 Azure AD 專屬元件，可針對所有裝置狀態處理裝置註冊程序。
* **信賴平台模組** (TPM)：TPM 是裝置內建的硬體元件，可為使用者和裝置祕密提供硬體型安全功能。 如需更多詳細資料，請參閱[信賴平台模組技術概觀](/windows/security/information-protection/tpm/trusted-platform-module-overview)。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些內容？

PRT 包含一般會包含在任何 Azure AD 重新整理權杖中的宣告。 此外，PRT 中還包含一些裝置特有的宣告。 如下所示：

* **裝置識別碼**：PRT 的發佈對象是特定裝置上的使用者。 裝置識別碼宣告 `deviceID` 會決定獲得 PRT 的使用者位於哪個裝置。 此宣告會在稍後發給透過 PRT 取得的權杖。 裝置識別碼宣告的用途是根據裝置狀態或合規性來決定條件式存取的授權。
* **工作階段金鑰**：工作階段金鑰是加密的對稱金鑰，由 Azure AD 驗證服務產生，並且會作為 PRT 的一部份來發出。 當使用 PRT 來取得其他應用程式的權杖時，工作階段金鑰會作為擁有權證明。

### <a name="can-i-see-whats-in-a-prt"></a>我可以看到 PRT 中的內容嗎？

PRT 是從 Azure AD 傳送的不透明 Blob，任何用戶端元件都不會知道其內容。 因此您看不到 PRT 的內容。

## <a name="how-is-a-prt-issued"></a>PRT 如何發出？

裝置註冊是在 Azure AD 中進行裝置型驗證時的必要條件。 PRT 只會發給已註冊裝置上的使用者。 如需深入了解裝置註冊的詳細資料，請參閱 [Windows Hello 企業版和裝置註冊](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)一文。 在裝置註冊期間，dsreg 元件會產生兩組密碼編譯金鑰組：

* 裝置金鑰 (dkpub/dkpriv)
* 傳輸金鑰 (tkpub/tkpriv)

如果裝置具有有效且正常運作的 TPM，則私密金鑰會繫結至裝置的 TPM，而公開金鑰則會在裝置註冊過程中傳送至 Azure AD。 在 PRT 要求期間，這些金鑰會用來驗證裝置狀態。

在兩種情況下，Windows 10 裝置上的使用者驗證期間會發出 PRT：

* **已加入 Azure AD** 或**已加入混合式 Azure AD**：當使用者以其組織認證登入時，PRT 會在執行 Windows 登入期間發出。 PRT 會以所有 Windows 10 支援的認證 (例如，密碼和 Windows Hello 企業版) 來發出。 在此案例中，Azure AD CloudAP 外掛程式是 PRT 的主要授權單位。
* **已註冊 Azure AD 的裝置**：PRT 會在使用者將次要公司帳戶新增至其 Windows 10 裝置時發出。 使用者可以透過兩種不同的方式，將帳戶新增至 Windows 10 -  
   * 在登入應用程式 (例如 Outlook) 之後，透過 [在此裝置上的任何位置使用此帳戶] 提示，來新增帳戶
   * 從 [設定] > [帳戶] > [存取公司或學校帳戶] > [連線] 中新增帳戶

如果是已註冊 Azure AD 的裝置，Azure AD WAM 外掛程式會是 PRT 的主要授權單位，因為此 Azure AD 帳戶不會發生 Windows 登入。

> [!NOTE]
> 第三方識別提供者必須支援 WS-Trust 通訊協定，才能在 Windows 10 裝置上發出 PRT。 若沒有 WS-Trust，將會無法在已加入混合式 Azure AD 或已加入 Azure AD 的裝置上向使用者發出 PRT

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 的存留期為何？

PRT 在發出之後的有效時間為 14 天，而且只要使用者積極使用裝置，PRT 就會持續更新。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

Windows 中有兩個主要元件會使用 PRT：

* **Azure AD CloudAP 外掛程式**：在 Windows 登入期間，Azure AD CloudAP 外掛程式會透過使用者所提供的認證，向 Azure AD 要求 PRT。 其也會快取 PRT，以在使用者無法存取網際網路連線時啟用快取登入。
* **Azure AD WAM 外掛程式**：當使用者嘗試存取應用程式時，Azure AD WAM 外掛程式會使用 PRT 來啟用 Windows 10 上的 SSO。 針對依賴 WAM 進行權杖要求的應用程式，Azure AD WAM 外掛程式會使用 PRT 來向其要求重新整理和存取權杖。 其也會藉由將 PRT 插入瀏覽器要求中，在瀏覽器上啟用 SSO。 Microsoft Edge (原生) 和 Chrome (透過 [Windows 10 帳戶](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en)或 [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) 擴充功能) 都可支援 Windows 10 中的瀏覽器 SSO。

## <a name="how-is-a-prt-renewed"></a>PRT 如何更新？

PRT 會以兩種不同的方法進行更新：

* **Azure AD CloudAP 外掛程式 (每隔 4 小時)** ：在 Windows 登入期間，CloudAP 外掛程式會每隔 4 小時更新 PRT。 如果使用者在這段時間內沒有網際網路連線，則 CloudAP 外掛程式會在裝置連線到網際網路之後更新 PRT。
* **Azure AD WAM 外掛程式 (在應用程式權杖要求期間)** ：WAM 外掛程式會藉由啟用應用程式的無訊息權杖要求，在 Windows 10 裝置上啟用 SSO。 WAM 外掛程式可以透過兩種不同的方式，在這些權杖要求期間更新 PRT：
   * 應用程式以無訊息方式向 WAM 要求存取權杖，但該應用程式沒有可用的重新整理權杖。 在此情況下，WAM 會使用 PRT 來要求應用程式的權杖，並在回應中取得新的 PRT。
   * 應用程式向 WAM要求存取權杖，但 PRT 無效或 Azure AD 需要額外的授權 (例如，Azure Multi-Factor Authentication)。 在此情況下，WAM 會起始需要使用者進行重新驗證或提供額外驗證的互動式登入，而新的 PRT 會在成功驗證時發出。

### <a name="key-considerations"></a>主要考量

* PRT 只會在原生應用程式驗證期間發出及更新。 PRT 不會在瀏覽器工作階段期間更新或發出。
* 在加入 Azure AD 和加入混合式 Azure AD 的裝置中，CloudAP 外掛程式是 PRT 的主要授權單位。 如果在以 WAM 為基礎的權杖要求期間更新 PRT，則 PRT 會傳送回 CloudAP 外掛程式，該程式會在接受 PRT 之前，向 Azure AD 驗證 PRT 的有效性。

## <a name="how-is-the-prt-protected"></a>PRT 如何受到保護？

保護 PRT 的方式是將其繫結至使用者已登入的裝置。 Azure AD 和 Windows 10 會透過下列方法啟用 PRT 保護：

* **第一次登入時**：第一次登入時，PRT 會在使用裝置註冊期間產生的加密裝置金鑰來簽署要求時發出。 在其 TPM 有效且可運作的裝置上，裝置金鑰會受到 TPM 的保護，以防止任何惡意存取。 如果無法驗證對應的裝置金鑰簽章，則不會發出 PRT。
* **在權杖要求和更新期間**：發出 PRT 時，Azure AD 也會向裝置發出加密的工作階段金鑰。 其會使用產生的公開傳輸金鑰 (tkpub) 進行加密，並在裝置註冊過程中傳送至 Azure AD。 此工作階段金鑰只能由 TPM 所保護的私人傳輸金鑰 (tkpriv) 解密。 對傳送至 Azure AD 的任何要求而言，工作階段金鑰是擁有權證明 (POP) 金鑰。  工作階段金鑰也會受到 TPM 的保護，而且沒有其他作業系統元件可以存取該金鑰。 此工作階段金鑰會透過 TPM 安全地簽署權杖要求或 PRT 更新要求，因此無法遭到篡改。 只要不是由對應工作階段金鑰所簽署的要求，Azure AD 就會讓來自裝置的任何要求無效。

透過 TPM 來保護這些金鑰，惡意執行者就無法竊取金鑰，也無法在其他地方重新執行 PRT，因為即使攻擊者有裝置的實體擁有權，也無法存取 TPM。  因此，使用 TPM 可為已加入 Azure AD、已加入混合式 Azure AD 及註冊 Azure AD 的裝置大幅提升安全性，以防止認證遭竊。 為了擁有更好的效能和可靠性，我們建議您針對 Windows 10 上所有 Azure AD 裝置註冊案例使用 TPM 2.0。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>如何保護應用程式權杖和瀏覽器 Cookie？

**APP 權杖**：當應用程式透過 WAM 要求權杖時，Azure AD 會發出重新整理權杖和存取權杖。 不過，WAM 只會將存取權杖傳回給應用程式，並透過使用者的資料保護應用程式開發介面 (DPAPI) 金鑰來加密重新整理權杖，進而在其快取中保護重新整理權杖。 WAM 會使用工作階段金鑰簽署要求，藉此安全地使用重新整理權杖來發出另外的存取權杖。 DPAPI 金鑰會由 Azure AD 本身的 Azure AD 式對稱金鑰保護。 當裝置需要使用 DPAPI 金鑰來解密使用者設定檔時，Azure AD 會提供由工作階段金鑰加密的 DPAPI 金鑰，然後由 CloudAP 外掛程式要求 TPM 進行解密。 這項功能可確保重新整理權杖的保護具有一致性，並避免應用程式執行自己的保護機制。  

**瀏覽器 Cookie**：在 Windows 10 中，Azure AD 可在 Internet Explorer 和 Microsoft Edge 中 (以原生方式) 或 Google Chrome 中 (以 Windows 10 帳戶擴充功能) 支援瀏覽器 SSO。 安全性不只是用來保護 Cookie，也會保護 Cookie 的目的地端點。 瀏覽器 Cookie 的保護方式與 PRT 相同，也是利用工作階段金鑰來簽署和保護 Cookie。

當使用者起始瀏覽器互動時，瀏覽器 (或擴充功能) 會叫用 COM 原生用戶端主機。 原生用戶端主機可確保頁面是來自其中一個允許的網域。 瀏覽器可以將其他參數傳送至原生用戶端主機 (包括 nonce)，不過原生用戶端主機可保證主機名稱的正確性。 原生用戶端主機會向 CloudAP 外掛程式要求 PRT Cookie，該程式會使用 TPM 保護的工作階段金鑰來建立及簽署此 Cookie。 由於 PRT Cookie 是由工作階段金鑰所簽署，因此無法遭到篡改。 此 PRT Cookie 會包含在要求標頭中，以供 Azure AD 用來驗證其來源裝置。 如果使用 Chrome 瀏覽器，則只有在原生用戶端主機資訊清單中明確定義的擴充功能可以叫用 PRT Cookie，以防止任意擴充功能提出這些要求。 Azure AD 會在驗證 PRT Cookie 之後，向瀏覽器發出工作階段 Cookie。 此工作階段 Cookie 也包含工作階段金鑰，與透過 PRT 發出的工作階段金鑰相同。 在後續要求期間，工作階段金鑰會進行驗證，以有效地將 Cookie 繫結至裝置，並防止從其他地方重新執行。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 何時會取得 MFA 宣告？

PRT 可以在特定案例中取得多重要素驗證 (MFA) 宣告。 使用 MFA 型 PRT 來要求應用程式的權杖時，MFA 宣告會轉移至這些應用程式權杖。 這項功能可避免每個需要 MFA 的應用程式遇到 MFA 方面的問題，為使用者提供順暢的體驗。 PRT 可以透過下列方式取得 MFA 宣告：

* **使用 Windows Hello 企業版登入**：Windows Hello 企業版會取代密碼，並使用密碼編譯金鑰來提供強式雙重要素驗證。 Windows Hello 企業版專屬於裝置上的使用者，而且本身需要 MFA 才能佈建。 當使用者使用 Windows Hello 企業版登入時，使用者的 PRT 就會取得 MFA 宣告。 如果智慧卡驗證從 ADFS 產生 MFA 宣告，則此狀況也適用於使用智慧卡登入的使用者。
   * 當 Windows Hello 企業版被視為多重要素驗證時，MFA 宣告就會在 PRT 本身重新整理時更新，因此當使用者使用 WIndows Hello 企業版登入時，MFA 的持續期間將繼續延長
* **WAM 互動式登入期間的 MFA**：在透過 WAM 進行權杖要求的期間，如果使用者必須執行 MFA 來存取應用程式，則在此互動期間更新的 PRT 就會嵌入 MFA 宣告。
   * 在此情況下，MFA 宣告不會持續更新，因此 MFA 持續時間是以目錄上設定的存留期為依據。
   * 當先前存在的 PRT 和 RT 用來存取應用程式時，PRT 和 RT 將被視為第一個驗證證明。 新的 AT 將會需要第二個證明和已嵌入的 MFA 宣告。 這也會發出新的 PRT 和 RT。
* **裝置註冊期間的 MFA**：在 Azure AD 中，如果系統管理員已將其裝置設定為[需要 MFA 才能註冊裝置](device-management-azure-portal.md#configure-device-settings)，則使用者必須執行 MFA 才能完成註冊。 在此過程中，向使用者發出的 PRT 會在註冊期間取得 MFA 宣告。 這項功能僅適用於執行加入作業的使用者，而不會套用至其他登入該裝置的使用者。
   * 類似於 WAM 互動式登入，MFA 宣告不會持續更新，因此 MFA 持續時間是以目錄上設定的存留期為依據。

Windows 10 會針對每個認證維護一個 PRT 的資料分割清單。 因此，每個 Windows Hello 企業版、密碼或智慧卡都會有 PRT。 此資料分割可確保 MFA 宣告會根據所使用的認證加以隔離，而不會在權杖要求期間混在一起。

## <a name="how-is-a-prt-invalidated"></a>PRT 如何會失效？

在下列案例中，PRT 會失效：

* **無效的使用者**：如果 Azure AD 中的使用者遭到刪除或停用，則他們的 PRT 會失效，且無法用來取得應用程式的權杖。 如果已刪除或停用的使用者在之前已登入裝置，則快取登入會將其記錄在其中，直到 CloudAP 發現其無效狀態為止。 一旦 CloudAP 判定使用者無效，就會封鎖後續的登入。 系統會自動封鎖無效使用者，使其無法登入未快取其認證的新裝置。
* **裝置名稱無效**：如果 Azure AD 中的裝置遭到刪除或停用，則在該裝置上取得的 PRT 會失效，且無法用來取得其他應用程式的權杖。 如果使用者已登入無效裝置，他們可以繼續執行此動作。 但裝置上的所有權杖都已失效，且使用者沒有從該裝置登入到任何資源的 SSO。
* **密碼變更**：使用者變更其密碼之後，Azure AD 就會讓使用先前密碼取得的 PRT 失效。 密碼變更會致使使用者取得新的 PRT。 此失效狀況可能會以兩種不同的方式發生：
   * 如果使用者使用新密碼登入 Windows，CloudAP 會捨棄舊的 PRT，並要求 Azure AD 使用新的密碼來發出新的 PRT。 如果使用者沒有網際網路連線，將會無法驗證新的密碼，Windows 可能會要求使用者輸入舊密碼。
   * 如果使用者以舊密碼登入或在登入 Windows 之後變更其密碼，則任何以 WAM 為基礎的權杖要求都會使用舊的 PRT。 在此情況下，系統會提示使用者在 WAM 權杖要求期間進行重新驗證，並發出新的 PRT。
* **TPM 問題**：有時候，裝置的 TPM 可能會折損或失敗，而導致無法存取 TPM 保護的金鑰。 在此情況下，裝置會無法取得 PRT 或使用現有的 PRT 要求權杖，因為其無法證明擁有密碼編譯金鑰。 因此，Azure AD 會讓任何現有的 PRT 都失效。 Windows 10 會在偵測到失敗時起始復原流程，以使用新的密碼編譯金鑰來重新註冊裝置。 使用混合式 Azure Ad Join 時，就像初次註冊一樣，復原會以無訊息模式執行，而且不需要使用者輸入。 若為已加入 Azure AD 或已註冊 Azure AD 的裝置，則必須由在裝置上具有系統管理員權限的使用者執行復原。 在此情況下，復原流程會由引導使用者成功復原裝置的 Windows 提示字元來起始。

## <a name="detailed-flows"></a>詳細流程

下圖說明發出、更新和使用 PRT 來要求應用程式存取權杖時的詳細資料。 此外，這些步驟也會說明在這些互動期間，如何套用前述的安全性機制。

### <a name="prt-issuance-during-first-sign-in"></a>第一次登入期間的 PRT 發佈

![第一次登入期間的 PRT 發佈詳細流程](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在加入 Azure AD 的裝置中，這項交換會同步進行，以在使用者可以登入 Windows 之前發出 PRT。 在已加入混合式 Azure AD 的裝置中，內部部署 Active Directory 是主要授權單位。 因此，使用者只能等待，直到他們可以取得 TGT 來登入，而 PRT 發佈則會以非同步方式發生。 此案例不適用於已註冊 Azure AD 的裝置，因為登入不會使用 Azure AD 認證。

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者在登入 UI 中輸入其密碼。 LogonUI 會將驗證緩衝區中的認證傳遞給 LSA，這會在內部將其傳遞給 CloudAP。 CloudAP 會將此要求轉送至 CloudAP 外掛程式。 |
| B | CloudAP 外掛程式會起始領域探索要求，以識別使用者的識別提供者。 如果使用者的租用戶已設定同盟提供者，Azure AD 會傳回同盟提供者的「中繼資料交換 (MEX)」端點。 如果不是，Azure AD 會傳回使用者已受管理，表示使用者可以使用 Azure AD 進行驗證。 |
| C | 如果使用者是受到管理的，CloudAP 會從 Azure AD 取得 nonce。 如果使用者是同盟的，CloudAP 外掛程式會以使用者認證向同盟提供者要求 SAML 權杖。 收到 SAML 權杖後，此外掛程式會向 Azure AD 要求 nonce。 |
| D | CloudAP 外掛程式會使用使用者的認證、nonce 和代理程式範圍來建立驗證要求，並使用裝置金鑰 (dkpriv) 簽署要求，然後將其傳送至 Azure AD。 在同盟環境中，CloudAP 外掛程式會使用同盟提供者所傳回的 SAML 權杖，而不是使用者的認證。 |
| E | Azure AD 會驗證使用者認證、nonce 和裝置簽章，確認裝置在租用戶中是有效的，並發出加密的 PRT。 除了 PRT 之外，Azure AD 也會發出對稱金鑰，稱為工作階段金鑰，由 Azure AD 使用傳輸金鑰 (tkpub) 對其進行加密。 此外，工作階段金鑰也會內嵌在 PRT 中。 此工作階段金鑰會在使用 PRT 進行後續要求時，作為擁有權證明 (PoP) 金鑰。 |
| F | CloudAP 外掛程式會將加密的 PRT 和工作階段金鑰傳遞至 CloudAP。 CloudAP 會要求 TPM 使用傳輸金鑰 (tkpriv) 來解密工作階段金鑰，並使用 TPM 自己的金鑰來重新對其加密。 CloudAP 會將加密的工作階段金鑰連同 PRT 一起儲存在其快取中。 |

### <a name="prt-renewal-in-subsequent-logons"></a>後續登入中的 PRT 更新

![後續登入中的 PRT 更新](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者在登入 UI 中輸入其密碼。 LogonUI 會將驗證緩衝區中的認證傳遞給 LSA，這會在內部將其傳遞給 CloudAP。 CloudAP 會將此要求轉送至 CloudAP 外掛程式。 |
| B | 如果使用者先前已登入，Windows 就會起始快取登入，並驗證認證來登入使用者。 每隔 4 小時，CloudAP 外掛程式就會以非同步方式起始 PRT 更新。 |
| C | CloudAP 外掛程式會起始領域探索要求，以識別使用者的識別提供者。 如果使用者的租用戶已設定同盟提供者，Azure AD 會傳回同盟提供者的「中繼資料交換 (MEX)」端點。 如果不是，Azure AD 會傳回使用者已受管理，表示使用者可以使用 Azure AD 進行驗證。 |
| D | 如果使用者是同盟的，CloudAP 外掛程式會以使用者認證向同盟提供者要求 SAML 權杖。 收到 SAML 權杖後，此外掛程式會向 Azure AD 要求 nonce。 如果使用者是受到管理的，CloudAP 會直接從 Azure AD 取得 nonce。 |
| E | CloudAP 外掛程式會使用使用者的認證、nonce 和現有 PRT 來建立驗證要求，並使用工作階段金鑰簽署要求，然後將其傳送至 Azure AD。 在同盟環境中，CloudAP 外掛程式會使用同盟提供者所傳回的 SAML 權杖，而不是使用者的認證。 |
| F | Azure AD 會藉由與內嵌在 PRT 中的工作階段金鑰進行比較來驗證工作階段金鑰簽章，並驗證 nonce，以確認裝置在租用戶中是有效的，然後發出新的 PRT。 如先前所見，PRT 會再次伴隨著由傳輸金鑰 (tkpub) 加密的工作階段金鑰。 |
| G | CloudAP 外掛程式會將加密的 PRT 和工作階段金鑰傳遞至 CloudAP。 CloudAP 會要求 TPM 使用傳輸金鑰 (tkpriv) 來解密工作階段金鑰，並使用 TPM 自己的金鑰來重新對其加密。 CloudAP 會將加密的工作階段金鑰連同 PRT 一起儲存在其快取中。 |

### <a name="prt-usage-during-app-token-requests"></a>應用程式權杖要求期間的 PRT 使用方式

![應用程式權杖要求期間的 PRT 使用方式](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 應用程式 (例如 Outlook、OneNote 等) 會向 WAM 起始權杖要求。 接著，WAM 會要求 Azure AD WAM 外掛程式處理權杖要求。 |
| B | 如果應用程式的重新整理權杖已可供使用，Azure AD WAM 外掛程式就會用其來要求存取權杖。 為了提供裝置繫結的證明，WAM 外掛程式會使用工作階段金鑰來簽署要求。 Azure AD 會驗證工作階段金鑰，並發出應用程式的存取權杖和新的重新整理權杖，而且會由工作階段金鑰進行加密。 WAM 外掛程式會要求 Cloud AP 外掛程式來解密權杖，其接著會要求 TPM 使用工作階段金鑰進行解密，好讓 WAM 外掛程式可取得這兩個權杖。 接下來，WAM 外掛程式只會提供應用程式的存取權杖，同時使用 DPAPI 重新加密重新整理權杖，並將其儲存在自己的快取中  |
| C |  如果應用程式的重新整理權杖無法使用，Azure AD WAM 外掛程式就會使用 PRT 來要求存取權杖。 為了提供擁有權證明，WAM 外掛程式會使用工作階段金鑰來簽署包含 PRT 的要求。 Azure AD 會藉由與內嵌在 PRT 中的工作階段金鑰進行比較來驗證工作階段金鑰簽章，並確認裝置有效，然後發出應用程式的存取權杖和重新整理權杖。 此外，Azure AD 可以發出新的 PRT (根據重新整理週期)，而且這些 PRT 都會由工作階段金鑰加密。 |
| D | WAM 外掛程式會要求 Cloud AP 外掛程式來解密權杖，其接著會要求 TPM 使用工作階段金鑰進行解密，好讓 WAM 外掛程式可取得這兩個權杖。 接下來，WAM 外掛程式只會提供應用程式的存取權杖，同時使用 DPAPI 重新加密重新整理權杖，並將其儲存在自己的快取中。 WAM 外掛程式將會針對此應用程式使用重新整理權杖。 WAM 外掛程式也會將新的 PRT 往回提供給 Cloud AP 外掛程式，其會先使用 Azure AD 來驗證 PRT，然後在自己的快取中將其更新。 Cloud AP 外掛程式之後將會使用新的 PRT。 |
| E | WAM 會將新發出的存取權杖提供給 WAM，然後 WAM 再將其往回提供給呼叫應用程式|

### <a name="browser-sso-using-prt"></a>使用 PRT 的瀏覽器 SSO

![使用 PRT 的瀏覽器 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者使用其認證登入 Windows 以取得 PRT。 一旦使用者開啟瀏覽器，瀏覽器 (或擴充功能) 就會從登錄載入 URL。 |
| B | 當使用者開啟 Azure AD 登入 URL 時，瀏覽器或擴充功能就會以從登錄取得的 URL 來驗證 URL。 如果兩者相符，瀏覽器會叫用原生用戶端主機來取得權杖。 |
| C | 原生用戶端主機會驗證 URL 是否屬於 Microsoft 識別提供者 (Microsoft 帳戶或 Azure AD)、解壓縮從 URL 傳送的 nonce，以及呼叫 CloudAP 外掛程式來取得 PRT Cookie。 |
| D | CloudAP 外掛程式會建立 PRT Cookie，並使用與 TPM 繫結的工作階段金鑰登入，然後將其傳回給原生用戶端主機。 由於 Cookie 是由工作階段金鑰所簽署，因此無法遭到篡改。 |
| E | 原生用戶端主機會將此 PRT Cookie 傳回給瀏覽器，瀏覽器會將其包含在稱為 x-ms-RefreshTokenCredential 的要求標頭中，然後從 Azure AD 要求權杖。 |
| F | Azure AD 會驗證 PRT Cookie 上的工作階段金鑰簽章、驗證 nonce，並驗證裝置在租用戶中有效，然後發出網頁的識別碼權杖和瀏覽器的加密工作階段 Cookie。 |

## <a name="next-steps"></a>後續步驟

如需針對 PRT 相關問題進行疑難排解的詳細資訊，請參閱[針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)。
