---
title: 針對 Azure Active Directory 中的企業狀態漫遊進行疑難排解
description: 回答 IT 系統管理員可能會遇到的設定和應用程式資料同步處理的一些問題。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: e427a68751cc348a7b0024fb01402bd15ca87d2a
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705719"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>針對 Azure Active Directory 中的企業狀態漫遊設定進行疑難排解

本主題提供有關如何針對企業狀態漫遊問題進行疑難排解及診斷的資訊，並提供已知問題的清單。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> 本文適用于2015年7月以 Windows 10 啟動的舊版 Microsoft Edge HTML 瀏覽器。 本文不適用於2020年1月15日發行的新 Microsoft Edge Chromium 式瀏覽器。 如需新 Microsoft Edge 之同步處理行為的詳細資訊，請參閱 [Microsoft Edge 同步](/deployedge/microsoft-edge-enterprise-sync)處理的文章。

## <a name="preliminary-steps-for-troubleshooting"></a>疑難排解的預備步驟 

在開始進行疑難排解之前，請先確認使用者和裝置已正確設定，且裝置和使用者符合企業狀態漫遊的所有需求。 

1. 裝置上已安裝 Windows 10，包含最新更新且最低版本為 1511 (作業系統組建 10586 或更新版本)。 
1. 裝置已加入 Azure AD 或已加入混合式 Azure AD。 如需詳細資訊，請參閱[如何取得在 Azure AD 控制之下的裝置](overview.md)。
1. 請確定 Azure AD 中的租用戶已啟用**企業狀態漫遊**，如[啟用企業狀態漫遊](enterprise-state-roaming-enable.md)所述。 您可以對所有使用者或只對選取的使用者群組啟用漫遊。
1. 系統會將 Azure Active Directory Premium 授權指派給使用者。  
1. 必須重新啟動裝置，且使用者必須再次登入，才能存取企業狀態漫遊功能。

## <a name="information-to-include-when-you-need-help"></a>您需要協助時應包含的資訊
如果在進行過下列指導方針後仍無法解決問題，請連絡我們的支援工程師。 當您連絡他們時，請提供下列資訊：

* **錯誤的一般描述**：出現使用者看到的錯誤訊息嗎？ 如果沒有任何錯誤訊息，請詳細說明您所注意到的未預期行為。 已允許哪些功能進行同步處理，以及使用者預期同步處理哪些項目？ 是否有多項功能未同步處理，或已將功能隔離以個別同步處理？
* **受影響的使用者** – 是有一位還是多位使用者的同步處理成功/失敗？ 每個使用者涉及多少裝置？ 它們是否都沒有同步處理，或它們之間部分已同步處理，部分沒有同步處理？
* **使用者的相關資訊** – 使用者是使用什麼身分識別來登入裝置？ 使用者如何登入裝置？ 它們是否屬於允許同步處理的所選安全性群組中的裝置？ 
* **裝置的相關資訊** – 此裝置是否已經加入 Azure AD 或已經加入網域？ 此裝置使用哪一個組建？ 最新的更新是？
* **日期/時間/時區** –您看到錯誤的精確日期和時間 (包含時區) ？

包含這項資訊有助於我們盡快為您解決問題。

## <a name="troubleshooting-and-diagnosing-issues"></a>疑難排解和診斷問題

本節提供有關如何針對企業狀態漫遊相關問題進行疑難排解及診斷的建議。

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>確認同步處理，以及 [同步您的設定] 設定頁面 

1. 將您的 Windows 10 電腦加入已設定為允許企業狀態漫遊的網域之後，使用您的公司帳戶登入。 移至**設定**  >  **帳戶**  >  **同步處理您的設定**，並確認同步處理和個別設定都已開啟，且 [設定] 頁面頂端指出您正在與工作帳戶同步。 確認您的 [**設定**] 中的登入帳戶也會使用相同的帳戶來  >  **帳戶**  >  **您的資訊**。 
1. 透過在原始電腦上進行一些變更 (例如將工作列移至畫面右側或頂端)，來確認同步處理可跨多部電腦運作。 監看變更是否在 5 分鐘內傳送至第二部電腦。 

   * 將畫面鎖定和解除鎖定 (Win + L) 有助於觸發同步處理。
   * 您必須在兩部電腦上使用相同的帳戶登入，同步處理才能運作 – 因為「企業狀態漫遊」是繫結至使用者帳戶，而不是電腦帳戶。

**可能的問題**：如果 [設定]**** 頁面中的控制項無法使用，您會看到「只有當您使用 Microsoft 帳戶或公司帳戶時，某些 Windows 功能才能使用。」訊息。 如果裝置設定成加入網域並向 Azure AD 註冊，但裝置尚未成功向 Azure AD 驗證，可能就會發生這個問題。 可能的原因是必須套用裝置原則，但這個套用作業是非同步的，而且可能會延遲幾個小時。 

### <a name="verify-the-device-registration-status"></a>確認裝置註冊狀態

企業狀態漫遊需要在 Azure AD 註冊裝置。 雖然並非針對企業狀態漫遊，但遵循下列指示有助於確認 Windows 10 用戶端是否已經註冊，並確認憑證指紋、Azure AD 設定 URL、NGC 狀態及其他資訊。

1. 在未提高權限的情況下開啟命令提示字元。 若要在 Windows 中執行此操作，請開啟「執行」啟動程式 (Win + R) 並輸入 "cmd" 來開啟。
1. 命令提示字元開啟後，輸入 *dsregcmd.exe /status*。
1. 若要獲得預期的輸出，[AzureAdJoined]**** 欄位值應該是 [YES]、[WamDefaultSet]**** 欄位值應該是 [YES]，而 [WamDefaultGUID]**** 欄位值則應該是一個結尾為 “(AzureAd)” 的 GUID。

**潛在問題**： **WamDefaultSet** 和 **>azureadjoined** 在域值中都有「否」、裝置已加入網域並已向 Azure AD 註冊，且裝置未同步。如果顯示此檔案，裝置可能需要等待套用原則，或在連接至 Azure AD 時，裝置的驗證失敗。 使用者可能需等待幾個小時來等待原則套用。 其他疑難排解步驟可能包括透過登出和重新登入來重試自動註冊，或在工作排程器中啟動工作。 在某些情況下，於已提升權限的命令提示字元視窗中執行 *dsregcmd.exe /leave*、重新開機，然後再試一次註冊，可能有助於解決此問題。

**潛在問題**： **>settingsurl** 的欄位是空的，且裝置未同步。使用者上次登入裝置之前可能已登入，企業狀態漫遊在 Azure Active Directory 入口網站中啟用。 重新啟動裝置並讓使用者登入。 （選擇性）在入口網站中，嘗試讓 IT 系統管理員流覽至**Azure Active Directory**  >  **裝置**，  >  **企業狀態漫遊**停用並重新啟用**使用者可以跨裝置同步設定和應用程式資料**。 重新啟用之後，請重新啟動裝置並讓使用者登入。 如果這無法解決問題，如果裝置憑證不正確， **>settingsurl** 可能會是空的。 在此情況下，於已提升權限的命令提示字元視窗中執行 *dsregcmd.exe /leave*、重新開機，然後再試一次註冊，可能有助於解決此問題。

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>企業狀態漫遊與 Multi-Factor Authentication 

在某些情況下，如果設定了 Azure Multi-Factor Authentication，「企業狀態漫遊」可能會無法同步處理資料。 如需這些徵兆的詳細資訊，請參閱支援檔 [>kb3193683](https://support.microsoft.com/kb/3193683)。 

**可能的原因**：如果您的裝置已設定為在 Azure Active Directory 入口網站上需要 Multi-Factor Authentication，則使用密碼登入 Windows 10 裝置時，可能無法同步處理設定。 這類型的 Multi-Factor Authentication 組態是用來保護 Azure 系統管理員帳戶。 系統管理員使用者仍可使用其 Microsoft Passport for Work PIN 來登入 Windows 10 裝置，或在存取其他 Azure 服務（例如 Microsoft 365）時完成 Multi-Factor Authentication，以進行同步處理。

**可能的問題**：如果系統管理員設定 Active Directory 同盟服務 Multi-Factor Authentication 條件式存取原則，且裝置上的存取權杖到期，則同步處理可能會失敗。 請確定您使用 Microsoft Passport for Work PIN 來登入並登出，或在存取其他 Azure 服務（例如 Microsoft 365）時完成 Multi-Factor Authentication。

### <a name="event-viewer"></a>事件檢視器

進行進階疑難排解時，可以使用「事件檢視器」來找出特定錯誤。 這些錯誤記載在下面的表格中。 這些事件可以在事件檢視器 >**應用程式和服務記錄**檔中找到  >  **microsoft**  >  **windows**  >  **SettingSync-Azure** ，以及同步處理**應用程式和服務記錄**  >  **microsoft**  >  **windows**  >  **AAD**的身分識別相關問題。

## <a name="known-issues"></a>已知問題

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>同步處理在使用 MDM 軟體側載應用程式的裝置上無法運作

會影響執行 Windows 10 年度更新版 (版本 1607) 的裝置。 在 [事件檢視器] 中的 [SettingSync-Azure 記錄] 底下，常會看到事件識別碼 6013 與錯誤 80070259。

**建議的動作**  
確定 Windows 10 v1607 用戶端具有 2016 年 ８ 月 23 日累積更新 ([KB3176934](https://support.microsoft.com/kb/3176934) 作業系統組建 14393.82)。 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Internet Explorer 我的最愛不會同步

會影響執行 Windows 10 11 月更新版 (版本 1511) 的裝置。

**建議的動作**  
確定 Windows 10 v1511 用戶端具有 2016 年 7 月累積更新 ([KB3172985](https://support.microsoft.com/kb/3172985) 作業系統組建 10586.494)。

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>佈景主題和使用「Windows 資訊保護」保護的資料不會同步處理 

為了避免資料洩漏，以 [Windows 資訊保護](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) 保護的資料不會透過使用 Windows 10 年度更新版的裝置企業狀態漫遊進行同步處理。

**建議的動作**  
無。 未來的 Windows 更新可能會解決這個問題。

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>已加入網域的裝置上不會同步處理日期、時間及地區設定 
  
已加入網域的裝置將不會同步處理日期、時間及地區：自動時間設定。 使用自動時間可能會複寫其他日期、時間及地區設定，並導致那些設定不會同步處理。 

**建議的動作**  
無。 

---

### <a name="uac-prompts-when-syncing-passwords"></a>同步密碼時出現 UAC 提示

會影響執行 Windows 10 11 月更新版 (版本 1511) 並具備無線 NIC 且已設定為同步處理密碼的裝置。

**建議的動作**  
確定 Windows 10 v1511 用戶端具有累積更新 ([KB3140743](https://support.microsoft.com/kb/3140743) 作業系統組建 10586.494)。

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>同步處理不會在使用智慧卡登入的裝置上運作

如果您嘗試使用智慧卡或虛擬智慧卡來登入您的 Windows 裝置，設定同步處理將會停止運作。     

**建議的動作**  
無。 未來的 Windows 更新可能會解決這個問題。

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>已加入網域的裝置不會在離開公司網路後同步處理     

如果已加入網域並已向 Azure AD 註冊的裝置離站很長的時間，同步處理可能會失敗且無法完成網域驗證。

**建議的動作**  
請將裝置連線到公司網路，以便讓同步處理能夠繼續執行。

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD 聯結裝置未同步處理，而且使用者的使用者主體名稱混用大小寫。

如果使用者具有混合的案例 UPN (例如，使用者名稱，而不是使用者名稱) 且使用者位於已加入 Azure AD 的裝置上，而該裝置已從 Windows 10 組建10586升級到14393，則使用者的裝置可能無法同步。 

**建議的動作**  
使用者必須退出裝置並重新加入到雲端。 若要這樣做，請以本機系統管理員使用者身分登入，然後前往 [**設定**系統] 將裝置退出，  >  **System**  >  **About**然後選取 [管理或中斷與公司或學校的連線]。 清除下列檔案，然後 Azure AD 在 [**設定**系統] 中再次加入裝置，然後選取 [連線  >  **System**  >  **About**到公司或學校]。 繼續將裝置加入 Azure Active Directory，並完成流程。

在清除步驟中，清除下列檔案：
- `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\` 中的 Settings.dat
- 資料夾 `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account` 下的所有檔案

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>事件識別碼 6065：80070533 此使用者無法登入，因為此帳戶目前已停用  

在「事件檢視器」的 SettingSync/Debug 記錄底下，當使用者的認證過期時，可能會看到此錯誤。 此外，當租用戶未自動佈建 AzureRMS 時，可能會發生此錯誤。 

**建議的動作**  
在第一個案例中，讓使用者更新其認證，並使用新的認證登入到裝置。 若要解決 AzureRMS 問題，請繼續執行 [KB3193791](https://support.microsoft.com/kb/3193791) 中列出的步驟。 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>事件識別碼 1098：錯誤：0xCAA5001C 權杖代理人操作失敗  

在「事件檢視器」中的 AAD/作業記錄底下，可能會在事件 1104：AAD 雲端 AP 外掛程式呼叫 Get token 但傳回錯誤：0xC000005F 時看到此錯誤。 如果缺少權限或擁有權屬性，就會發生這個問題。  

**建議的動作**  
請繼續執行 [KB3196528](https://support.microsoft.com/kb/3196528) 中列出的步驟。  

## <a name="next-steps"></a>後續步驟

如需概觀，請參閱[企業狀態漫遊概觀](enterprise-state-roaming-overview.md)。