---
title: 主刷新權杖 （PRT） 和 Azure AD - Azure 活動目錄
description: Azure 活動目錄中的主要刷新權杖 （PRT） 的作用是什麼以及如何管理？
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672640"
---
# <a name="what-is-a-primary-refresh-token"></a>什麼是主刷新權杖？

主刷新權杖 （PRT） 是 Windows 10、iOS 和 Android 設備上 Azure AD 身份驗證的關鍵專案。 它是專門頒發給 Microsoft 第一方權杖代理的 JSON Web 權杖 （JWT），用於跨這些設備中使用的應用程式啟用單一登入 （SSO）。 在本文中，我們將提供有關如何在 Windows 10 設備上頒發、使用和保護 PRT 的詳細資訊。

本文假定您已經瞭解了 Azure AD 中可用的不同設備狀態以及單一登入在 Windows 10 中的工作方式。 有關 Azure AD 中的設備的詳細資訊，請參閱["Azure 活動目錄中什麼是裝置管理"](overview.md)一文。

## <a name="key-terminology-and-components"></a>關鍵術語和元件

以下 Windows 元件在請求和使用 PRT 時起著關鍵作用：

* **雲身份檢查器提供者**（CloudAP）：CloudAP 是 Windows 登錄的現代身份檢查器提供者，用於驗證登錄到 Windows 10 設備的使用者。 CloudAP 提供了一個外掛程式框架，身份供應商可以構建該外掛程式框架，以便使用該標識提供程式的憑據對 Windows 啟用身份驗證。
* **Web 帳戶管理器**（WAM）：WAM 是 Windows 10 設備上的預設權杖代理。 WAM 還提供一個外掛程式框架，身份提供程式可以基於該外掛程式框架，並使 SSO 能夠依賴于該標識提供程式。
* **Azure AD CloudAP 外掛程式**：在 CloudAP 框架上構建的特定于 Azure AD 的外掛程式，用於在 Windows 登錄期間使用 Azure AD 驗證使用者憑據。
* **Azure AD WAM 外掛程式**：在 WAM 框架上構建的特定于 Azure AD 的外掛程式，該外掛程式使 SSO 能夠連接到依賴 Azure AD 進行身份驗證的應用程式。
* **Dsreg**： Windows 10 上特定于 Azure AD 的元件，用於處理所有設備狀態的設備註冊過程。
* **受信任的平臺模組**（TPM）：TPM 是內置於設備中的硬體元件，為使用者和設備機密提供基於硬體的安全功能。 更多詳細資訊請參閱文章["受信任的平臺模組技術概述](/windows/security/information-protection/tpm/trusted-platform-module-overview)"。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些內容？

PRT 包含通常包含在任何 Azure AD 刷新權杖中的聲明。 此外，PRT 中還包括一些特定于設備的聲明。 如下所示：

* **裝置識別碼**：向特定設備上的使用者發出 PRT。 裝置識別碼`deviceID`聲明確定 PRT 頒發給使用者的設備。 此聲明後來頒發給通過 PRT 獲得的權杖。 裝置識別碼 聲明用於根據設備狀態或合規性確定條件訪問的授權。
* **工作階段金鑰**：工作階段金鑰是一個加密的對稱金鑰，由 Azure AD 身份驗證服務生成，作為 PRT 的一部分發出。 當使用 PRT 獲取其他應用程式的權杖時，工作階段金鑰充當佔有證明。

### <a name="can-i-see-whats-in-a-prt"></a>我能看看 PRT 中的內容嗎？

PRT 是從 Azure AD 發送的不透明 Blob，其內容不為任何用戶端元件所知。 您看不到 PRT 中的內容。

## <a name="how-is-a-prt-issued"></a>如何頒發 PRT？

設備註冊是 Azure AD 中基於設備的身份驗證的先決條件。 PRT 僅在已註冊的設備上頒發給使用者。 有關設備註冊的更深入詳細資訊，請參閱["Windows Hello 用於企業和設備註冊](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)"一文。 在設備註冊期間，dsreg 元件生成兩組加密金鑰對：

* 設備金鑰（dkpub/dkpriv）
* 運輸鑰匙（tkpub/tkpriv）

如果設備具有有效和正常運行的 TPM，則私密金鑰將綁定到設備的 TPM，而公開金鑰在設備註冊過程中會發送到 Azure AD。 這些金鑰用於在 PRT 請求期間驗證設備狀態。

PRT 在 Windows 10 設備上的使用者身份驗證期間在以下兩種情況下發出：

* **Azure AD 已加入**或**混合 Azure AD 加入**：當使用者使用組織憑據登錄時，在 Windows 登錄期間發出 PRT。 PRT 會頒發所有 Windows 10 支援的憑據，例如，密碼和適用于企業的 Windows Hello。 在這種情況下，Azure AD CloudAP 外掛程式是 PRT 的主要許可權。
* **Azure AD 註冊設備**：當使用者將輔助工作帳戶添加到其 Windows 10 設備時，將發出 PRT。 使用者可以以兩種不同的方式向 Windows 10 添加帳戶 -  
   * 登錄到應用後，通過 **"在此設備上的任何地方使用此帳戶**"添加帳戶（例如，Outlook）
   * 從**設置** > **帳戶** > **訪問工作或學校** > **連接**添加帳戶

在 Azure AD 註冊設備方案中，Azure AD WAM 外掛程式是 PRT 的主要許可權，因為此 Azure AD 帳戶未發生 Windows 登錄。

> [!NOTE]
> 協力廠商標識提供程式需要支援 WS-Trust 協定，以便在 Windows 10 設備上啟用 PRT 發行。 如果沒有 WS 信任，PRT 將無法頒發給混合 Azure AD 聯接或 Azure AD 聯接設備上的使用者

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 的壽命是多少？

一旦發出，PRT 的有效期為 14 天，只要使用者主動使用設備，它就會持續續訂。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

PRT 由 Windows 中的兩個關鍵元件使用：

* **Azure AD CloudAP 外掛程式**：在 Windows 登錄期間，Azure AD CloudAP 外掛程式使用使用者提供的憑據從 Azure AD 請求 PRT。 它還緩存 PRT，以便在使用者無法訪問互聯網連接時啟用緩存登錄。
* **Azure AD WAM 外掛程式**：當使用者嘗試訪問應用程式時，Azure AD WAM 外掛程式使用 PRT 在 Windows 10 上啟用 SSO。 Azure AD WAM 外掛程式使用 PRT 請求刷新並訪問依賴 WAM 進行權杖請求的應用程式的權杖。 它還通過將 PRT 注入瀏覽器請求，在瀏覽器上啟用 SSO。 微軟邊緣（本機）和 Chrome（通過 Windows 10 帳戶或辦公室連線副檔名）支援 Windows 10 中的瀏覽器 SSO。

## <a name="how-is-a-prt-renewed"></a>如何續訂 PRT？

PRT 以兩種不同的方法續訂：

* **Azure AD CloudAP 外掛程式每 4 小時一次**：在 Windows 登錄期間，雲AP 外掛程式每 4 小時更新一次 PRT。 如果使用者在此期間沒有互聯網連接，CloudAP 外掛程式將在設備連接到互聯網後續訂 PRT。
* **Azure AD WAM 外掛程式在應用權杖請求期間**： WAM 外掛程式通過為應用程式啟用靜默權杖請求，在 Windows 10 設備上啟用 SSO。 WAM 外掛程式可以在這些權杖請求期間以兩種不同的方式續訂 PRT：
   * 應用以靜默方式請求 WAM 訪問權杖，但沒有可用於該應用的刷新權杖。 在這種情況下，WAM 使用 PRT 請求應用的權杖，並在回應中返回新的 PRT。
   * 應用請求 WAM 獲取訪問權杖，但 PRT 無效或 Azure AD 需要其他授權（例如，Azure 多重要素驗證）。 在這種情況下，WAM 啟動互動式登入，要求使用者重新驗證或提供其他驗證，並在成功身份驗證時頒發新的 PRT。

### <a name="key-considerations"></a>主要考量

* PRT 僅在本機應用身份驗證期間頒發和續訂。 PRT 不會在瀏覽器會話期間續訂或頒發。
* 在 Azure AD 聯接和混合 Azure AD 聯接設備中，CloudAP 外掛程式是 PRT 的主要許可權。 如果在基於 WAM 的權杖請求期間續訂 PRT，則 PRT 將發送回 CloudAP 外掛程式，該外掛程式在接受之前使用 Azure AD 驗證 PRT 的有效性。

## <a name="how-is-the-prt-protected"></a>如何保護 PRT？

PRT 通過綁定到使用者已登錄的設備進行保護。 Azure AD 和 Windows 10 通過以下方法啟用 PRT 保護：

* **在第一次登錄**期間：在第一次登錄期間，使用設備註冊期間加密生成的設備金鑰，通過簽名請求發出 PRT。 在具有有效和正常運行的 TPM 的設備上，設備金鑰由 TPM 保護，以防止任何惡意訪問。 如果無法驗證相應的設備金鑰簽名，則不發出 PRT。
* **在權杖請求和續訂期間**：發出 PRT 時，Azure AD 還會向設備發出加密的工作階段金鑰。 它使用生成的公共傳輸金鑰 （tkpub） 進行加密，並作為設備註冊的一部分發送到 Azure AD。 此工作階段金鑰只能由 TPM 保護的專用傳輸金鑰 （tkpriv） 解密。 工作階段金鑰是發送到 Azure AD 的任何請求的佔有證明 （POP） 金鑰。  工作階段金鑰還受 TPM 保護，沒有其他作業系統元件可以訪問它。 權杖請求或 PRT 續訂請求由此工作階段金鑰通過 TPM 安全簽名，因此不能被篡改。 Azure AD 將使來自設備的任何未由相應工作階段金鑰簽名的請求無效。

通過使用 TPM 保護這些金鑰，惡意參與者無法竊取金鑰，也不能在其他地方重播 PRT，因為即使攻擊者擁有設備，TPM 也無法訪問。  因此，使用 TPM 可大大提高 Azure AD 加入、混合 Azure AD 聯接和 Azure AD 註冊設備的安全性，防止憑據被盜。 為性能和可靠性，TPM 2.0 是 Windows 10 上所有 Azure AD 設備註冊方案的推薦版本。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>如何保護應用權杖和瀏覽器 Cookie？

**應用權杖**：當應用通過 WAM 請求權杖時，Azure AD 會發出刷新權杖和訪問權杖。 但是，WAM 僅將訪問權杖返回到應用，並通過使用使用者的資料保護應用程式開發介面 （DPAPI） 金鑰加密刷新權杖來保護其緩存中的刷新權杖。 WAM 使用會話金鑰組請求進行簽名，從而安全地使用刷新權杖來頒發進一步的訪問權杖。 DPAPI 金鑰由 Azure AD 本身中基於 Azure AD 的對稱金鑰保護。 當設備需要使用 DPAPI 金鑰解密使用者設定檔時，Azure AD 提供由工作階段金鑰加密的 DPAPI 金鑰，CloudAP 外掛程式請求 TPM 解密該金鑰。 此功能可確保在保護刷新權杖時保持一致性，並避免應用程式實現自己的保護機制。  

**瀏覽器 Cookie**： 在 Windows 10 中，Azure AD 支援瀏覽器 SSO 在互聯網瀏覽器和微軟邊緣本機或在谷歌 Chrome 通過 Windows 10 帳戶擴展。 安全性不僅用於保護 Cookie，還用於保護 Cookie 發送到的終結點。 瀏覽器 Cookie 的保護方式與 PRT 一樣，通過使用會話金鑰組 Cookie 進行簽名和保護。

當使用者啟動瀏覽器交互時，瀏覽器（或擴展）將調用 COM 本機用戶端主機。 本機用戶端主機可確保頁面來自允許的域之一。 瀏覽器可以向本機用戶端主機發送其他參數，包括 nonce，但本機用戶端主機保證對主機名稱的驗證。 本機用戶端主機從 CloudAP 外掛程式請求 PRT-cookie，該外掛程式使用受 TPM 保護的工作階段金鑰創建並簽名。 由於 PRT-cookie 由工作階段金鑰簽名，因此無法篡改。 此 PRT-cookie 包含在 Azure AD 的請求標頭中，以驗證其源自的設備。 如果使用 Chrome 瀏覽器，則只有本機用戶端主機清單中顯式定義的擴展才能調用它，防止任意擴展發出這些請求。 Azure AD 驗證 PRT Cookie 後，它會向瀏覽器發出會話 Cookie。 此會話 Cookie 還包含使用 PRT 頒發的同一工作階段金鑰。 在後續請求中，工作階段金鑰得到驗證，有效地將 Cookie 綁定到設備，並阻止從其他地方重播。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 何時獲得 MFA 索賠？

PRT 可以在特定方案中獲得多重要素驗證 （MFA） 聲明。 當基於 MFA 的 PRT 用於請求應用程式的權杖時，MFA 聲明將傳輸到這些應用權杖。 此功能通過防止每個需要它的應用的 MFA 挑戰，為使用者提供無縫體驗。 PRT 可以通過以下方式獲得 MFA 索賠：

* **使用 Windows Hello 為企業**登錄： 適用于企業的 Windows Hello 替換密碼並使用加密金鑰提供強大的雙重身份驗證。 適用于企業的 Windows Hello 特定于設備上的使用者，並且本身需要 MFA 來預配。 當使用者使用 Windows Hello 業務登錄時，使用者的 PRT 將獲得 MFA 聲明。 如果智慧卡身份驗證從 ADFS 生成 MFA 聲明，則此方案也適用于使用智慧卡登錄的使用者。
   * 由於適用于企業的 Windows Hello 被視為多重要素驗證，因此在刷新 PRT 本身時，MFA 聲明會更新，因此當使用者使用 WIndows Hello Business 登錄時，MFA 持續時間將持續延長
* **MFA 在 WAM 互動式登入期間**： 通過 WAM 進行權杖請求期間，如果使用者需要執行 MFA 才能訪問應用，則在此交互期間續訂的 PRT 將帶有 MFA 聲明。
   * 在這種情況下，MFA 聲明不會持續更新，因此 MFA 持續時間基於目錄中設置的存留期。
   * 當以前的現有 PRT 和 RT 用於訪問應用時，PRT 和 RT 將被視為身份驗證的第一個證明。 需要一個新的AT，有第二個證據和印記MFA索賠。 這還將發佈新的 PRT 和 RT。
* **設備註冊期間的 MFA：** 如果管理員在 Azure AD 中配置了設備設置[，要求 MFA 註冊設備](device-management-azure-portal.md#configure-device-settings)，則使用者需要執行 MFA 才能完成註冊。 在此過程中，向使用者發出的 PRT 在註冊期間獲得了 MFA 索賠。 此功能僅適用于執行聯接操作的使用者，不適用於登錄到該設備的其他使用者。
   * 與 WAM 互動式登入類似，MFA 聲明不會持續更新，因此 MFA 持續時間基於目錄上設置的存留期。

Windows 10 維護每個憑據的已分區 PRT 清單。 因此，每個 Windows Hello 企業、密碼或智慧卡都有一個 PRT。 此分區可確保 MFA 聲明根據使用的憑據隔離，不會在權杖請求期間混合。

## <a name="how-is-a-prt-invalidated"></a>PRT 如何失效？

在以下情況下，PRT 無效：

* **無效使用者**：如果在 Azure AD 中刪除或禁用使用者，則其 PRT 將失效，不能用於獲取應用程式的權杖。 如果已刪除或禁用的使用者之前已登錄到設備，緩存的登錄將登錄，直到 CloudAP 知道其無效狀態。 一旦 CloudAP 確定使用者無效，它將阻止後續登錄。 無效使用者將自動阻止登錄到未緩存其憑據的新設備。
* **無效設備**：如果在 Azure AD 中刪除或禁用設備，則在該設備上獲得的 PRT 將失效，不能用於獲取其他應用程式的權杖。 如果使用者已登錄到無效設備，他們可以繼續這樣做。 但是，設備上的所有權杖都無效，並且使用者沒有 SSO 到該設備的任何資源。
* **密碼更改**：使用者更改其密碼後，使用前一個密碼獲取的 PRT 將由 Azure AD 失效。 密碼更改會導致使用者獲得新的 PRT。 這種失效可以通過兩種不同的方式發生：
   * 如果使用者使用新密碼登錄到 Windows，CloudAP 將丟棄舊的 PRT，並請求 Azure AD 使用新密碼頒發新 PRT。 如果使用者沒有互聯網連接，則無法驗證新密碼，Windows 可能要求使用者輸入其舊密碼。
   * 如果使用者在登錄到 Windows 後使用舊密碼登錄或更改了密碼，則舊 PRT 將用於任何基於 WAM 的權杖請求。 在這種情況下，系統會提示使用者在 WAM 權杖請求期間重新進行身份驗證，並頒發新的 PRT。
* **TPM 問題**：有時，設備的 TPM 可能會抖抖或失敗，從而導致 TPM 保護的金鑰無法訪問。 在這種情況下，設備無法獲取 PRT 或使用現有 PRT 請求權杖，因為它無法證明擁有加密金鑰。 因此，任何現有的 PRT 都因 Azure AD 而失效。 當 Windows 10 檢測到故障時，它會啟動恢復流以使用新的加密金鑰重新註冊設備。 使用混合 Azure 廣告聯接，就像初始註冊一樣，恢復在沒有使用者輸入的情況下靜默進行。 對於 Azure AD 已加入或 Azure AD 註冊設備，恢復需要由在設備上具有管理員許可權的使用者執行。 在這種情況下，恢復流由 Windows 提示啟動，該提示引導使用者成功恢復設備。

## <a name="detailed-flows"></a>詳細流程

下圖說明瞭頒發、續訂和使用 PRT 請求應用程式訪問權杖的基本詳細資訊。 此外，這些步驟還描述了在這些交互過程中如何應用上述安全機制。

### <a name="prt-issuance-during-first-sign-in"></a>首次登錄期間的 PRT 發行

![PRT 在第一次簽署期間在詳細流中發出](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在 Azure AD 聯接的設備上，此交換同步發生，以在使用者可以登錄到 Windows 之前發出 PRT。 在混合 Azure AD 聯接設備中，本地活動目錄是主要許可權。 因此，使用者只等待，直到他們可以獲取 TGT 登錄，而 PRT 發行以非同步方式進行。 此方案不適用於 Azure AD 註冊設備，因為登錄不使用 Azure AD 憑據。

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者在 UI 中的登錄名中輸入其密碼。 LogonUI 將 auth 緩衝區中的憑據傳遞給 LSA，LSA 在內部傳遞到 CloudAP。 CloudAP 將此請求轉發給 CloudAP 外掛程式。 |
| B | CloudAP 外掛程式啟動域發現請求，以標識使用者的標識提供程式。 如果使用者的租戶具有聯合提供程式設置，Azure AD 將返回聯合提供程式的中繼資料交換終結點 （MEX）。 如果沒有，Azure AD 將返回使用者被管理，指示使用者可以使用 Azure AD 進行身份驗證。 |
| C | 如果使用者已管理，CloudAP 將從 Azure AD 獲取 nonce。 如果使用者是聯合的，CloudAP 外掛程式會向聯合供應商請求具有使用者憑據的 SAML 權杖。 一旦它收到 SAML 權杖，它將請求 Azure AD 的 nonce。 |
| D | CloudAP 外掛程式使用使用者的憑據 nonce 和代理作用域構造身份驗證請求，使用設備金鑰 （dkpriv） 對請求進行簽名，並將其發送到 Azure AD。 在聯合環境中，CloudAP 外掛程式使用聯合提供程式返回的 SAML 權杖，而不是使用者的憑據。 |
| E | Azure AD 驗證使用者憑據、nonce 和設備簽名，驗證設備在租戶中是否有效並頒發加密 PRT。 與 PRT 一起，Azure AD 還會發出一個對稱金鑰，稱為 Azure AD 使用傳輸金鑰 （tkpub） 加密的工作階段金鑰。 此外，工作階段金鑰也嵌入 PRT 中。 此工作階段金鑰充當 PRT 後續請求的佔有證明 （PoP） 金鑰。 |
| F | CloudAP 外掛程式將加密 PRT 和工作階段金鑰傳遞給 CloudAP。 CloudAP 請求 TPM 使用傳輸金鑰 （tkpriv） 解密工作階段金鑰，並使用 TPM 自己的金鑰重新加密它。 CloudAP 將加密工作階段金鑰與 PRT 一起存儲在其緩存中。 |

### <a name="prt-renewal-in-subsequent-logons"></a>後續登錄中的 PRT 續訂

![後續登錄中的 PRT 續訂](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者在 UI 中的登錄名中輸入其密碼。 LogonUI 將 auth 緩衝區中的憑據傳遞給 LSA，LSA 在內部傳遞到 CloudAP。 CloudAP 將此請求轉發給 CloudAP 外掛程式。 |
| B | 如果使用者以前登錄到該使用者，Windows 將啟動緩存登錄並驗證憑據以登錄使用者。 每 4 小時，CloudAP 外掛程式會非同步啟動 PRT 續訂。 |
| C | CloudAP 外掛程式啟動域發現請求，以標識使用者的標識提供程式。 如果使用者的租戶具有聯合提供程式設置，Azure AD 將返回聯合提供程式的中繼資料交換終結點 （MEX）。 如果沒有，Azure AD 將返回使用者被管理，指示使用者可以使用 Azure AD 進行身份驗證。 |
| D | 如果使用者是聯合的，CloudAP 外掛程式會向聯合供應商請求具有使用者憑據的 SAML 權杖。 一旦它收到 SAML 權杖，它將請求 Azure AD 的 nonce。 如果使用者是託管的，CloudAP 將直接從 Azure AD 獲取 nonce。 |
| E | CloudAP 外掛程式使用使用者的憑據 nonce 和現有 PRT 構造身份驗證請求，使用會話金鑰組請求進行簽名，並將其發送到 Azure AD。 在聯合環境中，CloudAP 外掛程式使用聯合提供程式返回的 SAML 權杖，而不是使用者的憑據。 |
| F | Azure AD 通過將其與 PRT 中嵌入的工作階段金鑰進行比較來驗證工作階段金鑰簽名，驗證 nonce 並驗證設備在租戶中是否有效並頒發新的 PRT。 如前所示，PRT 再次附帶由傳輸金鑰 （tkpub） 加密的工作階段金鑰。 |
| G | CloudAP 外掛程式將加密 PRT 和工作階段金鑰傳遞給 CloudAP。 CloudAP 請求 TPM 使用傳輸金鑰 （tkpriv） 解密工作階段金鑰，並使用 TPM 自己的金鑰重新加密它。 CloudAP 將加密工作階段金鑰與 PRT 一起存儲在其緩存中。 |

### <a name="prt-usage-during-app-token-requests"></a>應用權杖請求期間的 PRT 使用

![應用權杖請求期間的 PRT 使用](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 應用程式（例如 Outlook、OneNote 等）向 WAM 發起權杖請求。 WAM 反過來要求 Azure AD WAM 外掛程式為權杖請求提供服務。 |
| B | 如果應用程式的刷新權杖已可用，Azure AD WAM 外掛程式會使用它請求訪問權杖。 為了提供設備綁定證明，WAM 外掛程式使用會話金鑰組請求進行簽名。 Azure AD 驗證工作階段金鑰，並為應用頒發訪問權杖和新的刷新權杖，由工作階段金鑰加密。 WAM 外掛程式請求 Cloud AP 外掛程式解密權杖，而權杖又請求 TPM 使用工作階段金鑰解密，從而導致 WAM 外掛程式同時獲取兩個權杖。 接下來，WAM 外掛程式僅提供對應用程式的訪問權杖，同時使用 DPAPI 重新加密刷新權杖並將其存儲在自己的緩存中  |
| C |  如果應用程式的刷新權杖不可用，Azure AD WAM 外掛程式將使用 PRT 請求訪問權杖。 為了提供擁有證明，WAM 外掛程式使用工作階段金鑰在包含 PRT 的請求上簽名。 Azure AD 通過將其與 PRT 中嵌入的工作階段金鑰進行比較來驗證工作階段金鑰簽名，驗證設備是否有效，並為應用程式頒發訪問權杖和刷新權杖。 此外，Azure AD 可以發佈新的 PRT（基於刷新週期），所有這些 PRT 都由工作階段金鑰加密。 |
| D | WAM 外掛程式請求 Cloud AP 外掛程式解密權杖，而權杖又請求 TPM 使用工作階段金鑰解密，從而導致 WAM 外掛程式同時獲取兩個權杖。 接下來，WAM 外掛程式僅提供對應用程式的訪問權杖，同時使用 DPAPI 重新加密刷新權杖並將其存儲在自己的緩存中。 WAM 外掛程式將為此應用程式使用刷新權杖。 WAM 外掛程式還會將新的 PRT 回饋給雲 AP 外掛程式，該外掛程式在在其自己的緩存中更新 PRT 之前，使用 Azure AD 對其進行驗證。 雲 AP 外掛程式將未來使用新的 PRT。 |
| E | WAM 向 WAM 提供新頒發的訪問權杖，而 WAM 又將其發回檔用應用程式|

### <a name="browser-sso-using-prt"></a>使用 PRT 的瀏覽器 SSO

![使用 PRT 的瀏覽器 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者使用憑據登錄到 Windows 以獲取 PRT。 使用者打開瀏覽器後，瀏覽器（或擴展）將從註冊表載入 URL。 |
| B | 當使用者打開 Azure AD 登錄 URL 時，瀏覽器或擴展將驗證 URL 與從註冊表獲取的 URL。 如果它們匹配，瀏覽器將調用本機用戶端主機以獲取權杖。 |
| C | 本機用戶端主機驗證 URL 是否屬於 Microsoft 標識提供程式（Microsoft 帳戶或 Azure AD），提取從 URL 發送的 nonce，並調用 CloudAP 外掛程式以獲取 PRT Cookie。 |
| D | CloudAP 外掛程式將創建 PRT Cookie，使用 TPM 綁定工作階段金鑰登錄，並將其發送回本機用戶端主機。 由於 Cookie 由工作階段金鑰簽名，因此無法篡改它。 |
| E | 本機用戶端主機將此 PRT Cookie 返回到瀏覽器，瀏覽器將包含它作為稱為 x-ms-RefreshToken 憑據的請求標頭的一部分，並從 Azure AD 請求權杖。 |
| F | Azure AD 驗證 PRT Cookie 上的工作階段金鑰簽名，驗證 nonce，驗證設備在租戶中是否有效，並為網頁發出 ID 權杖和瀏覽器的加密會話 Cookie。 |

## <a name="next-steps"></a>後續步驟

有關解決 PRT 相關問題疑難排解的詳細資訊，請參閱有關[排除混合 Azure 活動目錄加入 Windows 10 和 Windows Server 2016 設備](troubleshoot-hybrid-join-windows-current.md)的文章。
