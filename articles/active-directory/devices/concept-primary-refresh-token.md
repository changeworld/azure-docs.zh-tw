---
title: 主要重新整理權杖（PRT）和 Azure AD Azure Active Directory
description: 什麼是角色，以及如何管理 Azure Active Directory 中的主要重新整理權杖（PRT）？
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
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672640"
---
# <a name="what-is-a-primary-refresh-token"></a>什麼是主要的重新整理權杖？

主要的重新整理權杖（PRT）是在 Windows 10、iOS 和 Android 裝置上 Azure AD 驗證的主要專案。 它是特別發行給 Microsoft 第一方權杖代理人的 JSON Web 權杖（JWT），可跨這些裝置上使用的應用程式啟用單一登入（SSO）。 在本文中，我們將提供有關如何在 Windows 10 裝置上發行、使用和保護 PRT 的詳細資料。

本文假設您已瞭解 Azure AD 中可用的不同裝置狀態，以及單一登入在 Windows 10 中的運作方式。 如需 Azure AD 中裝置的詳細資訊，請參閱[什麼是 Azure Active Directory 中的裝置管理？](overview.md)

## <a name="key-terminology-and-components"></a>重要的術語和元件

下列 Windows 元件在要求和使用 PRT 方面扮演著重要的角色：

* **雲端驗證提供者**（CloudAP）： CloudAP 是適用于 windows 登入的新式驗證提供者，可驗證使用者是否記錄到 windows 10 裝置。 CloudAP 提供一個外掛程式架構，讓身分識別提供者能夠以該身分識別提供者的認證，在 Windows 上建立驗證。
* **Web 帳戶管理員**（WAM）： WAM 是 Windows 10 裝置上的預設權杖訊息代理程式。 WAM 也提供一個外掛程式架構，讓身分識別提供者可以在其應用程式依賴該身分識別提供者時，建立並啟用 SSO。
* **Azure AD CloudAP 外掛程式**： CloudAP 架構上建立的 Azure AD 特定外掛程式，可在 Windows 登入期間使用 Azure AD 來驗證使用者認證。
* **AZURE AD WAM 外掛程式**： WAM 架構上建立的 Azure AD 特定外掛程式，可讓 SSO 應用程式依賴 Azure AD 進行驗證。
* **Dsreg**： Windows 10 上的 Azure AD 特定元件，可處理所有裝置狀態的裝置註冊程式。
* **信賴平臺模組**（tpm）： TPM 是內建于裝置的硬體元件，可為使用者和裝置密碼提供硬體型安全性功能。 如需更多詳細資料，請參閱可[信賴平臺模組技術總覽](/windows/security/information-protection/tpm/trusted-platform-module-overview)一文。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些內容？

PRT 包含通常包含在任何 Azure AD 重新整理權杖中的宣告。 此外，PRT 中還包含一些裝置特定的宣告。 如下所示：

* **裝置識別碼**： PRT 會發行給特定裝置上的使用者。 裝置識別碼宣告 `deviceID` 會決定 PRT 發給使用者的裝置。 此宣告會在稍後發給透過 PRT 取得的權杖。 裝置識別碼宣告是用來根據裝置狀態或合規性來判斷條件式存取的授權。
* **工作階段金鑰**：工作階段金鑰是加密的對稱金鑰，由 Azure AD authentication 服務所產生，並會在 PRT 中發行。 當使用 PRT 來取得其他應用程式的權杖時，工作階段金鑰會作為擁有權證明。

### <a name="can-i-see-whats-in-a-prt"></a>我可以看到 PRT 中的內容嗎？

PRT 是從 Azure AD 傳送的不透明 blob，其內容對任何用戶端元件而言都是不知道的。 您看不到 PRT 內的內容。

## <a name="how-is-a-prt-issued"></a>PRT 如何發行？

裝置註冊是 Azure AD 中以裝置為基礎之驗證的必要條件。 只有在已註冊的裝置上，才會將 PRT 發給使用者。 如需裝置註冊的更深入詳細資料，請參閱[Windows Hello 企業版和裝置註冊](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)一文。 在裝置註冊期間，dsreg 元件會產生兩組密碼編譯金鑰組：

* 裝置金鑰（dkpub/dkpriv）
* 傳輸金鑰（tkpub/tkpriv）

如果裝置具有有效且正常運作的 TPM，則私密金鑰會系結至裝置的 TPM，而公開金鑰則會在裝置註冊程式期間傳送至 Azure AD。 在 PRT 要求期間，會使用這些金鑰來驗證裝置狀態。

在兩種情況下，PRT 會在 Windows 10 裝置上的使用者驗證期間發出：

* **Azure AD 已聯結**或已**加入混合式 Azure AD**：當使用者以其組織認證登入時，會在 Windows 登入期間發出 PRT。 PRT 是以所有 Windows 10 支援的認證（例如，密碼和 Windows Hello 企業版）發行。 在此案例中，Azure AD CloudAP 外掛程式是 PRT 的主要授權單位。
* **Azure AD 已註冊的裝置**：當使用者將次要工作帳戶新增至其 Windows 10 裝置時，就會發出 PRT。 使用者可以透過兩種不同的方式將帳戶新增至 Windows 10-  
   * 在登入應用程式之後，透過在**此裝置上的任何位置使用此帳戶**來新增帳戶（例如 Outlook）
   * 從 **[** **設定**] > **帳戶**新增帳戶 > **存取公司或學校** > Connect

在 Azure AD 註冊的裝置案例中，Azure AD WAM 外掛程式是 PRT 的主要授權單位，因為此 Azure AD 帳戶不會發生 Windows 登入。

> [!NOTE]
> 協力廠商身分識別提供者必須支援 WS-TRUST 通訊協定，才能在 Windows 10 裝置上啟用 PRT 發行。 若沒有 WS-TRUST，PRT 就無法在已加入混合式 Azure AD 或已加入 Azure AD 的裝置上向使用者發出

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 的存留期為何？

一旦發出，PRT 的有效時間為14天，而且只要使用者主動使用裝置，就會持續更新。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

Windows 中的兩個主要元件會使用 PRT：

* **Azure AD CloudAP 外掛程式**：在 Windows 登入期間，Azure AD CloudAP 外掛程式會使用使用者所提供的認證，向 AZURE AD 要求 PRT。 它也會快取 PRT，以在使用者無法存取網際網路連線時啟用快取登入。
* **AZURE AD WAM 外掛程式**：當使用者嘗試存取應用程式時，Azure AD WAM 外掛程式會使用 PRT 來啟用 Windows 10 上的 SSO。 Azure AD WAM 外掛程式會使用 PRT，針對依賴 WAM 進行權杖要求的應用程式要求重新整理和存取權杖。 它也會藉由將 PRT 插入瀏覽器要求中，在瀏覽器上啟用 SSO。 Microsoft Edge （原生）和 Chrome （透過 Windows 10 帳戶或 Office Online 延伸模組）支援 Windows 10 中的瀏覽器 SSO。

## <a name="how-is-a-prt-renewed"></a>PRT 如何續訂？

PRT 會以兩種不同的方法進行更新：

* **每隔4小時 Azure AD CloudAP 外掛程式**： CloudAP 外掛程式會在 Windows 登入期間每隔4小時更新 PRT。 如果使用者在這段時間內沒有網際網路連線，則在裝置連線到網際網路之後，CloudAP 外掛程式將會更新 PRT。
* **在應用程式權杖要求期間 AZURE AD WAM 外掛程式**： WAM 外掛程式會啟用應用程式的無訊息權杖要求，以在 Windows 10 裝置上啟用 SSO。 WAM 外掛程式可以透過兩種不同的方式，在這些權杖要求期間更新 PRT：
   * 應用程式會以無訊息方式要求存取權杖的 WAM，但該應用程式沒有可用的重新整理權杖。 在此情況下，WAM 會使用 PRT 來要求應用程式的權杖，並在回應中取得新的 PRT。
   * 應用程式要求存取權杖的 WAM，但 PRT 無效，或 Azure AD 需要額外的授權（例如，Azure 多重要素驗證）。 在此案例中，WAM 會起始互動式登入，要求使用者重新驗證或提供額外的驗證，並在成功驗證時發出新的 PRT。

### <a name="key-considerations"></a>主要考量

* 只有在原生應用程式驗證期間，才會發出 PRT 並加以更新。 PRT 不會在瀏覽器會話期間更新或發行。
* 在 Azure AD 聯結和混合式 Azure AD 聯結裝置中，CloudAP 外掛程式是 PRT 的主要授權單位。 如果在以 WAM 為基礎的權杖要求期間更新 PRT，則會將 PRT 傳送回 CloudAP 外掛程式，這會在接受 Azure AD 之前驗證 PRT 的有效性。

## <a name="how-is-the-prt-protected"></a>PRT 如何受到保護？

PRT 受到保護，其方式是將它系結至使用者已登入的裝置。 Azure AD 和 Windows 10 透過下列方法啟用 PRT 保護：

* **第一次登入期間**：第一次登入期間，會使用裝置註冊期間產生的裝置金鑰加密來簽署要求，藉此發出 PRT。 在具有有效且可運作之 TPM 的裝置上，裝置金鑰會受到 TPM 的保護，以防止任何惡意存取。 如果無法驗證對應的裝置金鑰簽章，則不會發出 PRT。
* **在權杖要求和更新期間**：發出 PRT 時，Azure AD 也會向裝置發出加密的工作階段金鑰。 它會使用產生的公開傳輸金鑰（tkpub）進行加密，並傳送至 Azure AD 做為裝置註冊的一部分。 此工作階段金鑰只能由 TPM 所保護的私用傳輸金鑰（tkpriv）解密。 工作階段金鑰是傳送至 Azure AD 之任何要求的擁有權證明（POP）金鑰。  工作階段金鑰也會受到 TPM 的保護，而且沒有其他作業系統元件可以存取它。 此工作階段金鑰會透過 TPM 安全地簽署權杖要求或 PRT 更新要求，因此無法被篡改。 Azure AD 會使來自裝置的任何要求無效，而不是由對應的工作階段金鑰所簽署。

藉由使用 TPM 保護這些金鑰，惡意執行者就無法竊取金鑰，也無法在其他地方重新執行 PRT，因為即使攻擊者有實體擁有裝置，也無法存取 TPM。  因此，使用 TPM 可大幅提升已加入 Azure AD 的安全性，混合式 Azure AD 聯結，以及 Azure AD 註冊的裝置，以防止認證遭竊。 針對效能和可靠性，Windows 10 上所有 Azure AD 裝置註冊案例的建議使用 TPM 2.0。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>如何保護應用程式權杖和瀏覽器 cookie？

**應用程式權杖**：當應用程式透過 WAM 要求權杖時，Azure AD 會發出重新整理權杖和存取權杖。 不過，WAM 只會將存取權杖傳回給應用程式，並藉由使用使用者的資料保護應用程式開發介面（DPAPI）金鑰來加密，以保護其快取中的重新整理權杖。 WAM 會以工作階段金鑰簽署要求，以安全地使用重新整理權杖，以發出進一步的存取權杖。 DPAPI 金鑰會受到 Azure AD 本身中以 Azure AD 為基礎的對稱金鑰所保護。 當裝置需要使用 DPAPI 金鑰來解密使用者設定檔時，Azure AD 提供由工作階段金鑰加密的 DPAPI 金鑰，CloudAP 外掛程式會要求 TPM 進行解密。 這項功能可確保保護重新整理權杖的一致性，並避免應用程式執行自己的保護機制。  

**瀏覽器 cookie**：在 windows 10 中，Azure AD 透過 windows 10 帳戶擴充功能，在 Internet Explorer 和 Microsoft Edge 中以原生方式或在 Google Chrome 中支援瀏覽器 SSO。 安全性不只是用來保護 cookie，也會建立 cookie 的傳送端點。 瀏覽器 cookie 的保護方式與 PRT 相同，方法是利用工作階段金鑰來簽署和保護 cookie。

當使用者起始瀏覽器互動時，瀏覽器（或延伸模組）會叫用 COM native client 主機。 Native client 主機可確保頁面是來自其中一個允許的網域。 瀏覽器可以將其他參數傳送至 native client 主機（包括 nonce），不過 native client 主機會保證主機名稱的驗證。 Native client 主機向 CloudAP 外掛程式要求 PRT-cookie，它會使用 TPM 保護的工作階段金鑰來建立並簽署它。 由於 PRT cookie 是由工作階段金鑰所簽署，因此無法被篡改。 此 PRT cookie 會包含在要求標頭中，以供 Azure AD 用來驗證其來源裝置。 如果使用 Chrome 瀏覽器，只有在原生用戶端主控制項資訊清單中明確定義的擴充功能可以叫用它，防止任意延伸模組進行這些要求。 一旦 Azure AD 驗證 PRT cookie，它就會向瀏覽器發出會話 cookie。 此會話 cookie 也包含與 PRT 一起發行的相同工作階段金鑰。 在後續要求期間，工作階段金鑰會進行驗證，以有效地將 cookie 系結至裝置，並防止從其他地方重新執行。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 何時會取得 MFA 宣告？

在特定案例中，PRT 可以取得多重要素驗證（MFA）宣告。 當使用 MFA 型 PRT 來要求應用程式的權杖時，MFA 宣告會轉移至這些應用程式權杖。 這項功能可針對需要的每個應用程式防止 MFA 挑戰，為使用者提供順暢的體驗。 PRT 可以透過下列方式取得 MFA 宣告：

* **使用 Windows Hello 企業版登入**： Windows Hello 企業版會取代密碼，並使用加密編譯金鑰來提供強式雙因素驗證。 Windows Hello 企業版專屬於裝置上的使用者，而且本身需要 MFA 才能布建。 當使用者使用 Windows Hello 企業版登入時，使用者的 PRT 會取得 MFA 宣告。 如果智慧卡驗證從 ADFS 產生 MFA 宣告，則此案例也適用于使用智慧卡登入的使用者。
   * 當 Windows Hello 企業版被視為多重要素驗證時，MFA 宣告會在 PRT 本身重新整理時更新，因此當使用者使用 WIndows Hello 企業版登入時，MFA 持續時間將持續延長
* **WAM 互動式登入期間的 MFA**：在透過 WAM 的權杖要求期間，如果使用者必須執行 mfa 才能存取應用程式，則在此互動期間更新的 PRT 會 IMPRINTED 為 mfa 宣告。
   * 在此情況下，MFA 宣告不會持續更新，因此 MFA 持續時間是以目錄上設定的存留期為基礎。
   * 當先前現有的 PRT 和 RT 用來存取應用程式時，PRT 和 RT 將被視為第一次的驗證證明。 在第二次證明和 imprinted MFA 宣告中，將會需要新的。 這也會發出新的 PRT 和 RT。
* **裝置註冊期間的 MFA**：如果系統管理員已在 Azure AD 中設定其裝置設定，以[要求使用 mfa 來註冊裝置](device-management-azure-portal.md#configure-device-settings)，則使用者必須執行 mfa 才能完成註冊。 在此過程中，向使用者發出的 PRT 會在註冊期間取得 MFA 宣告。 這項功能僅適用于執行聯結作業的使用者，而不會套用至其他登入該裝置的使用者。
   * 類似于 WAM 互動式登入，MFA 宣告不會持續更新，因此 MFA 持續時間是以目錄上設定的存留期為基礎。

Windows 10 會針對每個認證維護一個 PRTs 的資料分割清單。 因此，每個 Windows Hello 企業版、密碼或智慧卡都有 PRT。 此分割可確保 MFA 宣告會根據所使用的認證加以隔離，而不會在權杖要求期間進行混合。

## <a name="how-is-a-prt-invalidated"></a>PRT 如何失效？

在下列案例中，PRT 會失效：

* **不正確使用者**：如果已在 Azure AD 中刪除或停用使用者，其 PRT 會失效，且無法用來取得應用程式的權杖。 如果已刪除或停用的使用者在之前已登入裝置，則快取登入會將其記錄在中，直到 CloudAP 知道其無效狀態為止。 一旦 CloudAP 判定使用者無效，就會封鎖後續的登入。 系統會自動封鎖不正確使用者，使其無法登入未快取其認證的新裝置。
* **不正確裝置**：如果已在 Azure AD 中刪除或停用裝置，則在該裝置上取得的 PRT 會失效，且無法用來取得其他應用程式的權杖。 如果使用者已登入不正確裝置，他們可以繼續執行此動作。 但裝置上的所有權杖都已失效，且使用者沒有從該裝置到任何資源的 SSO。
* **密碼變更**：使用者變更其密碼之後，以先前的密碼取得的 PRT 會 Azure AD 失效。 密碼變更會導致使用者取得新的 PRT。 這個失效可能會以兩種不同的方式發生：
   * 如果使用者使用新密碼登入 Windows，CloudAP 會捨棄舊的 PRT，並要求 Azure AD 使用新的密碼來發出新的 PRT。 如果使用者沒有網際網路連線，就無法驗證新的密碼，Windows 可能會要求使用者輸入舊密碼。
   * 如果使用者在登入 Windows 之後以舊密碼登入或變更其密碼，則會針對任何以 WAM 為基礎的權杖要求使用舊的 PRT。 在此案例中，系統會提示使用者在 WAM token 要求期間進行重新驗證，併發出新的 PRT。
* **TPM 問題**：有時候，裝置的 TPM 可能會折損或失敗，而導致 INACCESSIBILITY 受 TPM 保護的金鑰。 在此情況下，裝置無法使用現有的 PRT 取得 PRT 或要求權杖，因為它無法證明擁有密碼編譯金鑰。 因此，任何現有的 PRT 都會 Azure AD 失效。 當 Windows 10 偵測到失敗時，它會起始復原流程，以使用新的密碼編譯金鑰來重新註冊裝置。 使用混合式 Azure Ad join 時，就像初始註冊一樣，會以無訊息模式執行復原，而不需要使用者輸入。 若為已加入 Azure AD 或 Azure AD 已註冊的裝置，則必須由在裝置上具有系統管理員許可權的使用者執行復原。 在此案例中，復原流程是由引導使用者成功復原裝置的 Windows 提示字元所起始。

## <a name="detailed-flows"></a>詳細流程

下圖說明發行、更新和使用 PRT 來要求應用程式的存取權杖時的基礎詳細資料。 此外，這些步驟也會說明在這些互動期間，如何套用前述的安全性機制。

### <a name="prt-issuance-during-first-sign-in"></a>第一次登入期間的 PRT 發行

![第一次登入詳細流程期間的 PRT 發行](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在 Azure AD 聯結的裝置中，這項交換會同步進行，以在使用者可以登入 Windows 之前發出 PRT。 在已加入混合式 Azure AD 的裝置中，內部部署 Active Directory 是主要授權單位。 因此，使用者只會等到他們可以取得 TGT 來登入，而 PRT 發行則會以非同步方式發生。 此案例不適用於 Azure AD 已註冊的裝置，因為登入不會使用 Azure AD 認證。

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者在登入 UI 中輸入其密碼。 LogonUI 會將驗證緩衝區中的認證傳遞給 LSA，這會在內部將它傳遞給 CloudAP。 CloudAP 會將此要求轉送至 CloudAP 外掛程式。 |
| B | CloudAP 外掛程式會起始領域探索要求，以識別使用者的身分識別提供者。 如果使用者的租使用者已設定同盟提供者，Azure AD 會傳回同盟提供者的中繼資料交換端點（MEX）端點。 如果不是，Azure AD 會傳回管理的使用者，表示使用者可以使用 Azure AD 進行驗證。 |
| C | 如果使用者是受管理的，CloudAP 會從 Azure AD 取得 nonce。 如果使用者是同盟的，CloudAP 外掛程式會向同盟提供者要求 SAML 權杖與使用者的認證。 一旦收到 SAML 權杖，就會向 Azure AD 要求 nonce。 |
| D | CloudAP 外掛程式會使用使用者的認證、nonce 和 broker 範圍來建立驗證要求，並使用裝置金鑰（dkpriv）簽署要求，並將它傳送至 Azure AD。 在聯合環境中，CloudAP 外掛程式會使用同盟提供者所傳回的 SAML 權杖，而不是使用者的認證。 |
| E | Azure AD 會驗證使用者認證、nonce 和裝置簽章，確認裝置在租使用者中是有效的，併發出加密的 PRT。 除了 PRT 之外，Azure AD 也會發出對稱金鑰，稱為 Azure AD 使用傳輸金鑰（tkpub）加密的工作階段金鑰。 此外，工作階段金鑰也會內嵌在 PRT 中。 此工作階段金鑰會作為 PRT 後續要求的擁有權證明（PoP）金鑰。 |
| 華氏 (F) | CloudAP 外掛程式會將加密的 PRT 和工作階段金鑰傳遞至 CloudAP。 CloudAP 要求 TPM 使用傳輸金鑰（tkpriv）來解密工作階段金鑰，並使用 TPM 自己的金鑰重新加密它。 CloudAP 會將加密的工作階段金鑰連同 PRT 一起儲存在其快取中。 |

### <a name="prt-renewal-in-subsequent-logons"></a>後續登入中的 PRT 更新

![後續登入中的 PRT 更新](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者在登入 UI 中輸入其密碼。 LogonUI 會將驗證緩衝區中的認證傳遞給 LSA，這會在內部將它傳遞給 CloudAP。 CloudAP 會將此要求轉送至 CloudAP 外掛程式。 |
| B | 如果使用者先前已登入使用者，Windows 就會起始快取登入，並驗證認證以登入使用者。 CloudAP 外掛程式每隔4小時會以非同步方式起始 PRT 更新。 |
| C | CloudAP 外掛程式會起始領域探索要求，以識別使用者的身分識別提供者。 如果使用者的租使用者已設定同盟提供者，Azure AD 會傳回同盟提供者的中繼資料交換端點（MEX）端點。 如果不是，Azure AD 會傳回管理的使用者，表示使用者可以使用 Azure AD 進行驗證。 |
| D | 如果使用者是同盟的，CloudAP 外掛程式會向同盟提供者要求 SAML 權杖與使用者的認證。 一旦收到 SAML 權杖，就會向 Azure AD 要求 nonce。 如果使用者是受管理的，CloudAP 會直接從 Azure AD 取得 nonce。 |
| E | CloudAP 外掛程式會使用使用者的認證、nonce 和現有的 PRT 來建立驗證要求，並使用工作階段金鑰來簽署要求，並將它傳送至 Azure AD。 在聯合環境中，CloudAP 外掛程式會使用同盟提供者所傳回的 SAML 權杖，而不是使用者的認證。 |
| 華氏 (F) | Azure AD 會藉由與內嵌在 PRT 中的工作階段金鑰進行比較來驗證工作階段金鑰簽章、驗證 nonce，並確認裝置在租使用者中是有效的，併發出新的 PRT。 如先前所見，PRT 會再次伴隨著由傳輸金鑰（tkpub）加密的工作階段金鑰。 |
| G | CloudAP 外掛程式會將加密的 PRT 和工作階段金鑰傳遞至 CloudAP。 CloudAP 會要求 TPM 使用傳輸金鑰（tkpriv）來解密工作階段金鑰，並使用 TPM 自己的金鑰重新加密它。 CloudAP 會將加密的工作階段金鑰連同 PRT 一起儲存在其快取中。 |

### <a name="prt-usage-during-app-token-requests"></a>應用程式權杖要求期間的 PRT 使用方式

![應用程式權杖要求期間的 PRT 使用方式](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 應用程式（例如 Outlook、OneNote 等）會起始 WAM 的權杖要求。 接著，WAM 會要求 Azure AD WAM 外掛程式提供權杖要求的服務。 |
| B | 如果應用程式的重新整理權杖已可供使用，Azure AD WAM 外掛程式會使用它來要求存取權杖。 為了提供裝置系結的證明，WAM 外掛程式會使用工作階段金鑰來簽署要求。 Azure AD 會驗證工作階段金鑰，併發出應用程式的存取權杖和新的重新整理權杖，並由工作階段金鑰加密。 WAM 外掛程式會要求雲端 AP 外掛程式來解密權杖，接著會要求 TPM 使用工作階段金鑰進行解密，因而導致 WAM 外掛程式取得這兩個權杖。 接下來，WAM 外掛程式只會提供應用程式的存取權杖，同時它會使用 DPAPI 重新加密更新權杖，並將它儲存在自己的快取中  |
| C |  如果無法使用應用程式的重新整理權杖，Azure AD WAM 外掛程式會使用 PRT 來要求存取權杖。 為了提供擁有權證明，WAM 外掛程式會使用工作階段金鑰來簽署包含 PRT 的要求。 Azure AD 會藉由與內嵌在 PRT 中的工作階段金鑰進行比較來驗證工作階段金鑰簽章，驗證裝置是否有效，併發出應用程式的存取權杖和重新整理權杖。 此外，Azure AD 可以發出新的 PRT （根據重新整理週期），這些都是由工作階段金鑰加密。 |
| D | WAM 外掛程式會要求雲端 AP 外掛程式來解密權杖，接著會要求 TPM 使用工作階段金鑰進行解密，因而導致 WAM 外掛程式取得這兩個權杖。 接下來，WAM 外掛程式只會提供應用程式的存取權杖，同時會使用 DPAPI 重新加密更新權杖，並將它儲存在自己的快取中。 WAM 外掛程式將會針對此應用程式使用重新整理權杖。 WAM 外掛程式也會將新的 PRT 提供給雲端 AP 外掛程式，這會使用 Azure AD 來驗證 PRT，然後才在自己的快取中更新它。 雲端 AP 外掛程式將會使用新的 PRT。 |
| E | WAM 會將新發行的存取權杖提供給 WAM，然後再將它提供給呼叫的應用程式。|

### <a name="browser-sso-using-prt"></a>使用 PRT 的瀏覽器 SSO

![使用 PRT 的瀏覽器 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者使用其認證登入 Windows 以取得 PRT。 一旦使用者開啟瀏覽器，瀏覽器（或延伸模組）會從登錄載入 Url。 |
| B | 當使用者開啟 Azure AD 登入 URL 時，瀏覽器或延伸模組會以從登錄取得的 URL 來驗證 URL。 如果兩者相符，瀏覽器會叫用原生用戶端主機來取得權杖。 |
| C | Native client 主機會驗證 Url 是否屬於 Microsoft 身分識別提供者（Microsoft 帳戶或 Azure AD）、解壓縮從 URL 傳送的 nonce，以及呼叫 CloudAP 外掛程式以取得 PRT cookie。 |
| D | CloudAP 外掛程式會建立 PRT cookie，並使用 TPM 系結工作階段金鑰登入，並將其傳回給 native client 主機。 由於 cookie 是由工作階段金鑰所簽署，因此無法被篡改。 |
| E | Native client 主機會將此 PRT cookie 傳回給瀏覽器，其會將它包含在要求標頭中，稱為 x-ms-RefreshTokenCredential 和 Azure AD 的要求權杖。 |
| 華氏 (F) | Azure AD 會驗證 PRT cookie 上的工作階段金鑰簽章、驗證 nonce、驗證該裝置在租使用者中是否有效，併發出網頁的識別碼權杖和瀏覽器的加密會話 cookie。 |

## <a name="next-steps"></a>後續步驟

如需有關針對 PRT 相關問題進行疑難排解的詳細資訊，請參閱[疑難排解已加入混合式 Azure Active Directory windows 10 和 Windows Server 2016 裝置](troubleshoot-hybrid-join-windows-current.md)一文。
