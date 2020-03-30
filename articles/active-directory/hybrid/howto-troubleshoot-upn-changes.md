---
title: 計畫和故障排除 Azure 使用者原則名稱 （UPN） 更改
description: 瞭解 UPN 更改的已知問題和緩解措施
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695773da624bc8d4ccff09119d64fc43319ff488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246427"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>在 Azure 活動目錄中規劃和排除使用者主體名稱更改

使用者主體名稱 （UPN） 是使用者帳戶的互聯網通信標準的屬性。 UPN 是由 UPN 前置詞 (使用者帳戶名稱) 和 UPN 尾碼 (DNS 網域名稱) 所組成。 首碼使用"+"符號連接尾碼。 例如： someone@example.com 。 在目錄樹系的所有安全性主體物件之間，UPN 必須是唯一的。 

> [!NOTE]
> 對於開發人員，我們建議您使用使用者物件識別碼 作為不彈性識別碼，而不是 UPN。 如果您的應用程式當前正在使用 UPN，我們建議您設置 UPN 以匹配使用者的主要電子郵件地址，以改善其體驗。<br> **在混合環境中，使用者的 UPN 在本地目錄和 Azure 活動目錄中必須相同，這一點很重要**。

**本文假定您使用 UPN 作為使用者識別碼。它涉及 UPN 更改的規劃，以及從 UPN 更改可能導致的問題中恢復的問題。**

## <a name="learn-about-upns-and-upn-changes"></a>瞭解 UPN 和 UPN 更改
登錄頁面通常會提示使用者輸入其電子郵件地址時，所需的值實際上是他們的 UPN。 因此，您應該確保在使用者的主要電子郵件地址更改時更改其 UPN。

使用者的主要電子郵件地址可能會由於多種原因而更改：

* 公司重塑品牌

* 員工轉移到不同的公司部門 

* 合併和收購

* 員工姓名更改

### <a name="types-of-upn-changes"></a>UPN 更改的類型

您可以通過更改首碼、尾碼或兩者來更改 UPN。

* **更改首碼**。

   *  例如，如果某人的姓名已更改，您可以更改其帳戶名稱：  
BSimon@contoso.com到BJohnson@contoso.com

   * 您還可以更改首碼的公司標準：  
Bsimon@contoso.com到Britta.Simon@contoso.com

* **更改尾碼**。 <br>

    例如，如果一個人更改了部門，則可以更改他們的域： 

   * Britta.Simon@contoso.com 到 Britta.Simon@contosolabs.com <br>
     Or<br>
    *   Britta.Simon@corp.contoso.com 到 Britta.Simon@labs.contoso.com 

每次更新使用者的主電子郵件地址時，更改使用者的 UPN。 無論電子郵件更改的原因如何，必須始終更新 UPN 以匹配。

在從活動目錄到 Azure AD 的初始同步期間，確保使用者的電子郵件與其 UPN 相同

### <a name="upns-in-active-directory"></a>活動目錄中的 UPN

在活動目錄中，預設的 UPN 尾碼是創建使用者帳戶的域的 DNS 名稱。 在大多數情況下，這是您在互聯網上註冊為企業域的功能變數名稱。 如果在contoso.com域中創建使用者帳戶，則預設的 UPN 是

username@contoso.com

 但是，您可以使用活動目錄域和信任[添加更多 UPN 尾碼](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)。 

例如，您可能希望添加labs.contoso.com，並讓使用者的 UPN 和電子郵件反映這一點。 然後，他們將成為

username@labs.contoso.com.

>[!IMPORTANT]
> 如果活動目錄中的 UPN 和 Azure 活動目錄不匹配，則會出現問題。 如果要更改[Active Directory 中的尾碼](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)，必須確保[已在 Azure AD 上添加和驗證](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)了匹配的自訂功能變數名稱。 

![已驗證域的螢幕截圖](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure 活動目錄中的 UPN

使用者使用其使用者主體名稱屬性中的值登錄到 Azure AD。 

將 Azure AD 與本地活動目錄結合使用時，將使用 Azure AD 連接服務同步使用者帳戶。 預設情況下，Azure AD 連接嚮導使用本地活動目錄中的使用者主體名稱屬性作為 Azure AD 中的 UPN。 您可以在自訂安裝中將其更改為其他屬性。

更新單個使用者或整個組織的使用者主體名稱 （UPN） 時，必須擁有定義的流程。 

請參閱本文檔中的已知問題和解決方法。

將使用者帳戶從活動目錄同步到 Azure AD 時，請確保活動目錄中的 UPN 映射到 Azure AD 中的已驗證域。

![已驗證域的螢幕截圖](./media/howto-troubleshoot-upn-changes/verified-domains.png)

如果使用者主體名稱屬性的值與 Azure AD 中的已驗證域不對應，則同步過程將尾碼替換為預設的 .onmicrosoft.com 值。


### <a name="roll-out-bulk-upn-changes"></a>推出批量 UPN 更改

遵循批量 UPN 更改[的試點最佳實踐](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。 如果發現無法快速解決的問題，還可以制定用於恢復 UPN 的經過測試的回滾計畫。 運行試驗後，您可以開始定位具有各種組織角色的小使用者集及其特定應用或設備集。

通過這第一個使用者子集，您將清楚地瞭解使用者在更改中應該期待什麼。 在使用者通信中包括此資訊。

創建用於更改單個使用者上的 UPN 的已定義過程，作為正常操作的一部分。 我們建議採用一個經過測試的過程，其中包括有關已知問題和解決方法的文檔。

以下各節詳細介紹了更改 UPN 時的潛在已知問題和解決方法。

## <a name="user-provisioning-known-issues-and-workarounds"></a>使用者預配已知問題和解決方法

[軟體即服務 （SaaS）](https://azure.microsoft.com/overview/what-is-saas/)和業務線 （LoB） 應用程式通常依靠 UPN 來查找使用者並存儲使用者設定檔資訊，包括角色。 首次使用["及時"預配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)在使用者首次登錄應用時創建使用者設定檔的應用程式可能會受到 UPN 更改的影響。

**已知問題**<br>
更改使用者的 UPN 可能會中斷 Azure AD 使用者與在應用程式上創建的使用者設定檔之間的關係。 如果應用程式使用["及時"預配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)，則可能會創建全新的使用者設定檔。 這將要求應用程式管理員進行手動更改以修復此關係。

**解決 方案**<br>
[Azure AD 自動使用者預配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)允許您在支援的雲應用程式中自動創建、維護和刪除使用者標識。 在應用程式上配置自動使用者預配會自動更新應用程式中的 UPN。 將應用程式作為漸進式部署的一部分進行測試，以驗證它們是否不受 UPN 更改的影響。

## <a name="managed-devices-known-issues-and-workarounds"></a>託管設備已知問題和解決方法

通過將[設備帶到 Azure AD，](https://docs.microsoft.com/azure/active-directory/devices/overview)通過雲和本地資源的單一登入 （SSO） 來最大限度地提高使用者的工作效率。

### <a name="azure-ad-joined-devices"></a>Azure AD 加入裝置

[Azure AD 聯接](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join)的設備將直接加入 Azure AD，並允許使用者使用其組織的身份登錄到設備。

**已知問題** <br>
對於依賴于 Azure AD 進行身份驗證的應用程式，使用者可能會遇到單一登入問題。

**解決 方案** <br>
留出足夠的時間進行 UPN 更改以同步到 Azure AD。 驗證新的 UPN 是否反映在 Azure AD 門戶上後，請要求使用者選擇"其他使用者"磁貼以使用其新 UPN 登錄。 您也可以通過[PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)進行驗證。 使用新 UPN 登錄後，對舊 UPN 的引用可能仍出現在"訪問工作或學校"Windows 設置中。

![已驗證域的螢幕截圖](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

[混合 Azure AD 聯接](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid)的設備將連接到活動目錄和 Azure AD。 如果環境具有本地活動目錄佔用空間，並且還希望從 Azure AD 提供的功能中獲益，則可以實現混合 Azure AD 聯接。

**已知問題** 

Windows 10 混合 Azure AD 聯接的設備可能會遇到意外的重新開機和訪問問題。

如果使用者在新 UPN 已同步到 Azure AD 之前登錄到 Windows，或者繼續使用現有的 Windows 會話，則如果配置了條件訪問，則使用 Azure AD 進行身份驗證的應用程式可能會遇到單一登入問題強制使用混合聯接設備訪問資源。 

此外，將顯示以下消息，強制在一分鐘後重新開機。 

"您的電腦將在一分鐘內自動重新開機。 Windows 出現問題，需要重新開機。 您應立即關閉此消息並保存您的工作"。

**解決 方案** 

設備必須從 Azure AD 取消連接並重新啟動。 重新開機後，設備將自動再次加入 Azure AD，使用者必須通過選擇"其他使用者"磁貼使用新的 UPN 登錄。 要從 Azure AD 取消聯接設備，在命令提示符下運行以下命令：

**dsregcmd /離開**

如果使用者正在使用 Windows Hello 業務，則需要[重新註冊](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision)它。 在 UPN 更改後，Windows 7 和 8.1 設備不受此問題的影響。

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>微軟身份驗證器已知問題和解決方法

您的組織可能需要使用[Microsoft 身份驗證器應用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)來登錄和訪問組織應用程式和資料。 儘管應用中可能會出現使用者名，但在使用者完成註冊過程之前，帳戶不會設置為充當驗證方法。

[微軟身份驗證器應用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)有四個主要功能：

* 通過推送通知或驗證碼進行多重要素驗證

* 在 iOS 和 Android 設備上充當身份驗證代理，為使用[代理身份驗證](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)的應用程式提供單一登入

* 設備註冊（也稱為工作區加入）到 Azure AD，這是其他功能（如 Intune 應用保護和設備註冊/管理）的要求，

* 電話登錄，這需要 MFA 和設備註冊。

### <a name="multi-factor-authentication-with-android-devices"></a>使用 Android 設備的多重要素驗證

Microsoft 身份驗證器應用提供了帶外驗證選項。 [多因素身份驗證 （MFA）](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)在登錄期間不向使用者發送自動撥打電話或 SMS，而是將通知推送到使用者的智慧手機或平板電腦上的 Microsoft 身份驗證器應用。 使用者只需在應用中點擊"批准"（或輸入 PIN 或生物識別，然後點擊"身份驗證"即可完成其登錄。

更改使用者的 UPN 時，行動裝置可能會遇到以下問題：

**已知問題** 

舊的 UPN 仍顯示在使用者帳戶上，可能不會收到通知。 [驗證碼](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq)繼續工作。

**解決 方案**

如果收到通知，請指示使用者關閉通知，打開身份驗證器應用，點按"檢查通知"選項並批准 MFA 提示。 在此之後，帳戶上顯示的 UPN 將更新。 請注意，更新的 UPN 可能會顯示為新帳戶，這是因為正在使用其他身份驗證器功能。 有關詳細資訊，本文中還有其他已知問題。

### <a name="brokered-authentication"></a>代理身份驗證

在 Android 和 iOS 代理（如 Microsoft 身份驗證器）上啟用：

* 單次登錄 （SSO） - 您的使用者無需登錄到每個應用程式。

* 設備標識 - 代理訪問在加入工作區時在設備上創建的設備證書。

* 應用程式標識驗證 - 當應用程式調用代理時，它將傳遞其重定向 URL，並且代理驗證它。

此外，它允許應用程式參與更高級的功能，如[條件訪問](https://docs.microsoft.com/azure/active-directory/conditional-access/)，並支援 Microsoft [Intune 方案](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)。

**已知問題**<br>
由於應用程式傳遞的login_hint與存儲在代理上的 UPN 不匹配，使用者在使用代理輔助登錄的新應用程式上會收到更多的互動式身份驗證提示。

**解決 方案** <br> 使用者需要手動從 Microsoft 身份驗證器中刪除帳戶，並從代理輔助應用程式啟動新的登錄。 該帳戶將在初始身份驗證後自動添加。

### <a name="device-registration"></a>裝置註冊

Microsoft 身份驗證器應用負責將設備註冊到 Azure AD。 設備註冊允許設備對 Azure AD 進行身份驗證，並且是以下方案的要求：

* Intune 應用程式保護

* 調諧設備註冊

* 電話登錄

**已知問題**<br>
更改 UPN 時，Microsoft 身份驗證器應用上將顯示新 UPN 的新帳戶，而舊 UPN 的帳戶仍列出。 此外，舊的 UPN 顯示在應用設置上的"設備註冊"部分。 設備註冊的正常功能或受撫養方案沒有變化。

**解決 方案** <br> 要刪除 Microsoft 身份驗證器應用上對舊 UPN 的所有引用，請指示使用者手動從 Microsoft 身份驗證器中刪除新舊帳戶，重新註冊 MFA 並重新加入設備。

### <a name="phone-sign-in"></a>電話登錄

電話登錄允許使用者在沒有密碼的情況下登錄到 Azure AD。 要啟用電話登錄，使用者需要使用身份驗證器應用註冊 MFA，然後直接在身份驗證器上啟用電話登錄。 作為配置的一部分，設備註冊 Azure AD。

**已知問題** <br>
使用者無法使用電話登錄，因為他們沒有收到任何通知。 如果使用者點擊"檢查通知"，則會收到錯誤。

**解決 方案**<br>
使用者需要選擇啟用電話登錄的帳戶上的下拉式功能表，然後選擇"禁用電話登錄"。 如果需要，可以再次啟用電話登錄。

## <a name="security-key-fido2-known-issues-and-workarounds"></a>安全金鑰 （FIDO2） 已知問題和解決方法

**已知問題** <br>
當多個使用者在同一鍵上註冊時，登錄螢幕將顯示顯示舊 UPN 的帳戶選擇頁。 使用安全金鑰登錄不受 UPN 更改的影響。  

**解決 方案**<br>
要刪除對舊 UPN 的引用，使用者必須[重置安全金鑰並重新註冊](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)。

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive 已知問題和解決方法

眾所周知，OneDrive 使用者在 UPN 更改後會遇到問題。 有關詳細資訊，請參閱[UPN 更改如何影響 OneDrive URL 和 OneDrive 功能](https://docs.microsoft.com/onedrive/upn-changes)。

## <a name="next-steps"></a>後續步驟

請參閱以下資源：
* [Azure AD 連接：設計概念](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Azure AD UserPrincipalName 填入](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [微軟標識平臺 ID 權杖](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
