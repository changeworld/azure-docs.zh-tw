---
title: Azure Active Directory 裝置管理常見問題集 | Microsoft Docs
description: Azure Active Directory 裝置管理常見問題集。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7637a4280d725aa8cd3482641645dbe19cb56210
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84689039"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 裝置管理常見問題集

## <a name="general-faq"></a>一般常見問題

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>問：我最近註冊了裝置。 為什麼我在 Azure 入口網站中看不到 [我的使用者資訊] 下的裝置？ 或為什麼就已加入混合式 Azure Active Directory (Azure AD) 的裝置而言，裝置擁有者被標示為 N/A？

**答：** 已加入混合式 Azure AD 的 Windows 10 裝置不會顯示在 [**使用者裝置**] 底下。
請使用 Azure 入口網站中的 [所有裝置]**** 檢視。 您也可以使用 PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) Cmdlet。

只有下列裝置會列在 [使用者裝置]**** 底下：

- 所有未加入混合式 Azure AD 的個人裝置。 
- 所有非 Windows 10 或 Windows Server 2016 裝置。
- 所有非 Windows 裝置。 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>問：我要如何知道用戶端的裝置註冊狀態為何？

**答：** 在 [Azure 入口網站中，移至 [**所有裝置**]。 使用裝置識別碼來搜尋裝置。 檢查加入類型資料行中的值。 有時，可能會將裝置重設或重新安裝映像。 因此，檢查裝置上的裝置註冊狀態也很重要：

- 針對 Windows 10 和 Windows Server 2016 或更新的裝置，請執行 `dsregcmd.exe /status`。
- 針對舊版 OS 版本，請執行`%programFiles%\Microsoft Workplace Join\autoworkplace.exe`。

**答：** 如需疑難排解資訊，請參閱下列文章：
- [使用 dsregcmd 命令針對裝置進行疑難排解](troubleshoot-device-dsregcmd.md)
- [針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)
- [針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>問：我在 Azure 入口網站的 [使用者資訊] 底下看到裝置記錄。 並且看到該裝置上的狀態為已註冊。 我是否已正確設定使用條件式存取？

**答：** 以**deviceID**顯示的裝置加入狀態必須符合 Azure AD 上的狀態，並符合條件式存取的任何評估準則。 如需詳細資訊，請參閱透過[條件式存取要求雲端應用程式存取的受管理裝置](../conditional-access/require-managed-devices.md)。

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>問：為什麼我的使用者會看到一則錯誤訊息，指出「您的組織已刪除裝置」或其 Windows 10 裝置上的「您的組織已停用裝置」？

**答：** 在已加入或向 Azure AD 註冊的 Windows 10 裝置上，系統會發出可啟用單一登入的主要重新整理[權杖（PRT）](concept-primary-refresh-token.md) 。 PRT 的有效性是以裝置本身的有效性為基礎。 如果裝置在 Azure AD 中遭到刪除或停用，則使用者會看到此訊息，而不需從裝置本身起始動作。 在下列其中一個案例中，您可以刪除或停用裝置 Azure AD： 

- 使用者從我的應用程式入口網站停用裝置。 
- 系統管理員（或使用者）在 Azure 入口網站中或使用 PowerShell 來刪除或停用裝置
- 僅混合式 Azure AD 聯結：系統管理員移除超出同步範圍的 [裝置] OU，導致從 Azure AD 刪除裝置
- 升級 Azure AD 連接到 1.4. x 版。 [瞭解 Azure AD Connect 1.4. x 和裝置消失](/azure/active-directory/hybrid/reference-connect-device-disappearance)。


請參閱下文以瞭解如何修正這些動作。

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>問：我已在 Azure 入口網站中停用或刪除我的裝置，或使用 Windows PowerShell。 但裝置上的本機狀態顯示裝置仍處於已註冊狀態。 我該怎麼辦？

**答：** 這項作業是依設計進行。 在此情況下，裝置無法存取雲端中的資源。 系統管理員可以針對過時、遺失或遭竊的裝置執行此動作，以防止未經授權的存取。 如果不小心執行此動作，您必須重新啟用或重新註冊裝置，如下所述

- 如果裝置已在 Azure AD 中停用，具備足夠許可權的系統管理員可以從 Azure AD 入口網站啟用它  
  > [!NOTE]
  > 如果您使用 Azure AD Connect 同步處理裝置，則會在下一次同步處理週期期間自動重新啟用混合式 Azure AD 聯結裝置。 因此，如果您需要停用已加入混合式 Azure AD 裝置，您必須將它從內部部署 AD 中停用

 - 如果 Azure AD 中刪除裝置，您必須重新註冊裝置。 若要重新註冊，您必須在裝置上採取手動動作。 請參閱下方的指示，以瞭解如何根據裝置狀態重新註冊。 

      若要重新註冊已加入混合式 Azure AD Windows 10 和 Windows Server 2016/2019 裝置，請執行下列步驟：

      1. 以系統管理員身分開啟命令提示字元。
      1. 輸入 `dsregcmd.exe /debug /leave`。
      1. 登出後再登入，以觸發向 Azure AD 重新註冊裝置的排定工作。 

      針對已加入混合式 Azure AD 的舊版 Windows OS 版本，請執行下列步驟：

      1. 以系統管理員身分開啟命令提示字元。
      1. 輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`。
      1. 輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`。

      若為已加入 Azure AD 的裝置 Windows 10 裝置，請執行下列步驟：

      1. 以系統管理員身分開啟命令提示字元
      1. 輸入 `dsregcmd /forcerecovery` （注意：您必須是系統管理員才能執行此動作）。
      1. 在開啟的對話方塊中按一下 [登入]，然後繼續進行登入程式。
      1. 登出並登入裝置以完成復原。

      針對 Azure AD 已註冊的 Windows 10 裝置，請執行下列步驟：

      1. 前往 [**設定**] [帳戶] [  >  **Accounts**  >  **存取公司或學校**]。 
      1. 選取帳戶，然後選取 **[中斷連線]**。
      1. 按一下 [+ 連接]，然後透過登入程式重新註冊裝置。

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>問：為什麼我會在 Azure 入口網站中看到重複的裝置專案？

**為**

- 就 Windows 10 和 Windows Server 2016 而言，如果重複嘗試將同一個裝置取消加入再重新加入，就可能導致產生重複的項目。 
- 每個使用 [新增工作或學校帳戶]**** 的 Windows 使用者都會以相同的裝置名稱建立一個新裝置記錄。
- 針對已加入內部部署 Active Directory 網域的舊版 Windows OS 版本，自動註冊會為每個登入裝置的網域使用者，以相同的裝置名稱建立一個新裝置記錄。 
- 已加入 Azure AD 的機器如果經過抹除、重新安裝，然後再以相同名稱重新加入，就會顯示成另一個具有相同裝置名稱的記錄。

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>問： Windows 10 裝置是否會在 Azure AD 支援 FIPS 模式中進行 Tpm？

**答：** 只有符合 FIPS 規範的 TPM 2.0 才支援 Windows 10 裝置註冊，TPM 1.2 則不支援。 如果您的裝置具有 FIPS 相容的 TPM 1.2，您必須先停用它們，再繼續進行 Azure AD 聯結或混合式 Azure AD 聯結。 Microsoft 不會提供任何工具來停用 Tpm 的 FIPS 模式，因為它相依于 TPM 製造商。 請洽詢您的硬體 OEM 以取得支援。 

---

**問：為什麼使用者仍然可以從我在 Azure 入口網站中停用的裝置存取資源？**

**答：** 從 Azure AD 裝置標記為停用的時間起，最多需要一小時才能套用撤銷。

>[!NOTE] 
>針對已註冊的裝置，建議您將裝置抹除，以確保使用者無法存取資源。 如需詳細資訊，請參閱[什麼是裝置註冊？](/mem/intune/user-help/use-managed-devices-to-get-work-done)。 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>問：為什麼在 Azure 入口網站中的已註冊資料行底下，會將裝置標示為「擱置」？

**答**： [暫止] 表示裝置未註冊。 此狀態表示已使用 Azure AD 從內部部署 AD 連線並已準備好進行裝置註冊，來同步處理裝置。 這些裝置的 [聯結類型] 設定為 [已加入混合式 Azure AD]。 深入瞭解[如何規劃您的混合式 Azure Active Directory 聯結執行](hybrid-azuread-join-plan.md)。

>[!NOTE]
>裝置也可以從已註冊的狀態變更為「擱置」
>* 如果先從 Azure AD 刪除裝置，並從內部部署 AD 重新同步處理。
>* 如果裝置已從 Azure AD Connect 上的同步處理範圍中移除，並已新增回去。
>
>在這兩種情況下，您都必須在每個裝置上手動重新註冊裝置。 若要檢查裝置先前是否已註冊，您可以[使用 dsregcmd.exe 命令對裝置進行疑難排解](troubleshoot-device-dsregcmd.md)。

---
## <a name="azure-ad-join-faq"></a>Azure AD Join 常見問題集

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>問：如何? 在裝置本機退出已加入 Azure AD 的裝置嗎？

**答：** 針對純 Azure AD 加入的裝置，請確定您具有離線的本機系統管理員帳戶，或建立一個。 您無法使用任何 Azure AD 使用者認證來登入。 接下來，移至 [**設定**] [帳戶] [  >  **Accounts**  >  **存取公司或學校**]。 選取您的帳戶，然後選取 [中斷連線]****。 遵循提示，然後在系統提示您時提供本機系統管理員認證。 重新啟動裝置以完成退出程序。

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>問：我的使用者是否可以登入已在 Azure AD 中刪除或停用 Azure AD 加入的裝置？

**答：** 是。 Windows 具有快取使用者名稱和密碼的功能，可讓先前曾登入的使用者即使沒有網路連線，也能快速存取桌面。 

當裝置在 Azure AD 中被刪除或停用時，Windows 裝置並不知道此狀況。 所以先前曾登入的使用者能夠繼續以快取的使用者名稱和密碼存取桌面。 但當裝置遭到刪除或停用時，使用者將無法存取任何受裝置型條件式存取保護的資源。 

先前未曾登入的使用者無法存取裝置。 沒有任何已為他們啟用的快取使用者名稱和密碼。 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>問：已停用或已刪除的使用者是否可以登入已加入 Azure AD 的裝置

**答：** 是，但僅限時間使用。 當使用者在 Azure AD 中被刪除或停用時，Windows 裝置並不會立即得知此狀況。 所以先前曾登入的使用者能夠以快取的使用者名稱和密碼存取桌面。 

一般而言，裝置會在四小時內得知使用者狀態。 然後 Windows 就會封鎖這些使用者對桌面的存取。 由於使用者已在 Azure AD 中被刪除或停用，因此系統會撤銷他們的所有權杖。 所以他們無法存取任何資源。 

已被刪除或停用的使用者如果先前未曾登入，便無法存取裝置。 沒有任何已為他們啟用的快取使用者名稱和密碼。 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>問：為什麼我的使用者在變更其 UPN 之後，會在 Azure AD 加入的裝置上發生問題？

**答：** 目前，在已加入 Azure AD 的裝置上，不完全支援 UPN 變更。 因此，他們在變更其 UPN 之後，會在向 Azure AD 驗證時失敗。 所以使用者會在其裝置上遇到 SSO 和條件式存取的問題。 目前，使用者必須透過 [其他使用者] 圖格以其新的 UPN 登入 Windows 來解決此問題。 我們目前正努力解決此問題。 不過，使用 Windows Hello 企業版來登入的使用者不會遇到這個問題。 

Windows 10 2004 update 支援 UPN 變更。 具有此更新之裝置上的使用者在變更其 Upn 之後，將不會有任何問題

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>問：我的使用者無法從已加入 Azure AD 的裝置搜尋印表機。 如何從這些裝置啟用列印功能？

**答：** 若要為已加入 Azure AD 的裝置部署印表機，請參閱[使用預先驗證來部署 Windows Server 混合式雲端列印](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)。 您需要有內部部署的 Windows Server，才能部署混合式雲端列印。 目前尚無法使用雲端式列印服務。 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>問：如何? 連接到遠端 Azure AD 加入的裝置嗎？

**答：** 請參閱[連接到已加入遠端 Azure Active Directory 的電腦](/windows/client-management/connect-to-remote-aadj-pc)。

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>問：為什麼我*的使用者會看到您無法從這裡取得*資訊？

**答：** 您是否設定某些條件式存取規則來要求特定的裝置狀態？ 如果裝置不符合準則，使用者就會被封鎖而看到該訊息。 評估條件式存取原則規則。 確定裝置符合準則，以避免收到該訊息。

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>問：為什麼我的使用者在已加入 Azure AD 的裝置上，無法取得 Azure 多重要素驗證提示？

**答：** 使用者可以使用多重要素驗證來加入或註冊具有 Azure AD 的裝置。 如此一來，裝置本身就會變成該使用者的第二受信任要素。 每當同一個使用者登入裝置並存取應用程式時，Azure AD 都會將該裝置視為第二個要素。 它可讓該使用者順暢地存取應用程式，而不會出現額外的 Multi-Factor Authentication 提示。 

此行為：

- 適用於 Azure AD 已加入和 Azure AD 已註冊裝置，但不適用於混合式 Azure AD 已加入裝置。
- 它不適用於任何其他登入該裝置的使用者。 所以所有其他存取該裝置的使用者都會收到 Multi-Factor Authentication 挑戰。 然後，他們便可存取要求 Multi-Factor Authentication 的應用程式。

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>問：為什麼我會針對我剛加入 Azure AD 的裝置，取得使用者*名稱或密碼不正確*的訊息？

**答：** 這種情況的常見原因如下：

- 您的使用者認證已經無效。
- 您的電腦無法與 Azure Active Directory 進行通訊。 請檢查是否有任何網路連線問題。
- 同盟登入會要求同盟伺服器支援已啟用並可供存取的 WS-Trust 端點。 
- 您已啟用傳遞驗證。 所以當您登入時，必須變更暫時密碼。

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>問：為什麼我會看到*糟糕 .。。發生錯誤！* 當我嘗試 Azure AD 加入我的電腦] 時，會出現對話方塊嗎？

**答：** 當您使用 Intune 設定 Azure Active Directory 註冊時，就會發生此錯誤。 請確定嘗試加入 Azure AD 的使用者已獲指派正確的 Intune 授權。 如需詳細資訊，請參閱[設定 Windows 裝置的註冊](/intune/windows-enroll)。  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>問：為什麼我的嘗試 Azure AD 加入電腦失敗，雖然沒有收到任何錯誤資訊嗎？

**答：** 有可能的原因是您已使用本機內建的系統管理員帳戶登入裝置。 請在使用 Azure Active Directory Join 來完成設定之前，先建立一個不同的本機帳戶。 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>問：我們 Windows 10 裝置上有哪些 MS-Organization-P2P-Access 憑證？

**答：** Azure AD 加入和混合式 Azure AD 聯結裝置的 Azure AD，都會發出 MS 組織 P2P 存取憑證。 這些憑證可用來啟用相同租用戶中裝置間的信任，以進行遠端桌面存取。 一個憑證簽發給裝置，另一個憑證則簽發給使用者。 裝置憑證位於 `Local Computer\Personal\Certificates` 中，且有效期為一天。 如果裝置在 Azure AD 中仍處於作用中，系統就會更新此憑證 (透過簽發新憑證的方式)。 使用者憑證位於 `Current User\Personal\Certificates` 中，此憑證的有效期也是一天，但它是在使用者嘗試對另一個已加入 Azure AD 的裝置建立遠端桌面工作階段時視需要簽發的憑證。 系統並不會在其到期時予以更新。 這兩種憑證都是使用 `Local Computer\AAD Token Issuer\Certificates` 中的 MS-Organization-P2P-Access 憑證來簽發的。 而此憑證則是由 Azure AD 在裝置註冊期間所簽發的。 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>問：為什麼在我們的 Windows 10 裝置上看到多個由 MS-Organization-P2P-Access 簽發的過期憑證？ 如何刪除它們？

**答：** 在 Windows 10 1709 版和更低版本上發現問題，因為密碼編譯問題，電腦存放區上已過期的 MS-組織 P2P 存取憑證已繼續存在。 如果您使用任何無法處理大量過期憑證的 VPN 用戶端（例如 Cisco AnyConnect），您的使用者可能會面臨網路連線的問題。 此問題在 Windows 10 1803 版中已修正，可自動刪除任何這類過期的 MS-Organization-P2P-Access 憑證。 您可以將裝置更新至 Windows 10 1803 來解決此問題。 如果無法更新，您可以刪除這些憑證，而不會產生任何負面影響。  

---

## <a name="hybrid-azure-ad-join-faq"></a>混合式 Azure AD Join 常見問題集

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>問：如何? 在裝置本機退出已加入混合式 Azure AD 的裝置嗎？

**答：** 針對已加入混合式 Azure AD 的裝置，請務必關閉自動註冊。 如此一來，排定的工作就不會重新註冊該裝置。 接著，以系統管理員身分開啟命令提示字元，然後輸入 `dsregcmd.exe /debug /leave`。 或者，若要進行大量退出，請以指令碼方式跨多個裝置執行此命令。

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>問：哪裡可以找到診斷混合式 Azure AD 聯結失敗的疑難排解資訊？

**答：** 如需疑難排解資訊，請參閱下列文章：

- [針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)
- [針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>問：為什麼我會在 [Azure AD 裝置] 清單中看到 Windows 10 混合式 Azure AD 已加入裝置的重複 Azure AD 註冊的記錄？

**答：** 當您的使用者在已加入網域的裝置上將其帳戶新增至應用程式時，可能會出現 [**將帳戶新增至 Windows** ] 的提示？ 如果他們在出現提示時輸入**是**，裝置就會向 Azure AD 註冊。 信任類型會標示為 [Azure AD 已註冊]。 在您於組織中啟用混合式 Azure AD Join 之後，裝置也會加入混合式 Azure AD。 如此一來，針對同一個裝置就會顯示兩個裝置狀態。 

混合式 Azure AD Join 的優先順序會高於 Azure AD 已註冊狀態。 因此，您的裝置會被視為混合式 Azure AD 以進行任何驗證和條件式存取評估。 您可以從 Azure AD 入口網站中放心地刪除 Azure AD 已註冊裝置記錄。 了解如何[在 Windows 10 機器上避免或清除此雙重狀態](hybrid-azuread-join-plan.md#review-things-you-should-know)。 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>問：為什麼我的使用者在變更其 UPN 之後，會在 Windows 10 混合式 Azure AD 加入的裝置上發生問題？

**答：** 目前，混合式 Azure AD 加入的裝置不完全支援 UPN 變更。 雖然使用者可以登入裝置，並存取其內部部署應用程式，但在變更 UPN 之後，向 Azure AD 進行驗證會失敗。 所以使用者會在其裝置上遇到 SSO 和條件式存取的問題。 此時，您必須從 Azure AD 退出裝置（以較高的許可權執行 "dsregcmd.exe/leave"），然後重新加入（會自動進行）以解決問題。 我們目前正努力解決此問題。 不過，使用 Windows Hello 企業版來登入的使用者不會遇到這個問題。 

Windows 10 2004 update 支援 UPN 變更。 具有此更新之裝置上的使用者在變更其 Upn 之後，將不會有任何問題

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>問： Windows 10 混合式 Azure AD 加入的裝置是否需要網域控制站的視線，才能存取雲端資源？

**答：** 否，但使用者的密碼變更時除外。 當 Windows 10 混合式 Azure AD 聯結完成，而且使用者至少已登入一次之後，裝置就不需要讓網域控制站看到存取雲端資源的行為。 Windows 10 可以透過網際網路連線從任何地方取得 Azure AD 應用程式的單一登入，但密碼變更時除外。 即使在變更密碼之後，使用 Windows Hello 企業版登入的使用者仍會繼續取得 Azure AD 應用程式的單一登入，即使他們沒有網域控制站的視線也一樣。 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>問：如果使用者變更其密碼，並嘗試登入公司網路外部的 Windows 10 混合式 Azure AD 聯結裝置，會發生什麼事？

**答：** 如果密碼在公司網路外部變更（例如，藉由使用 Azure AD SSPR），則使用新密碼登入的使用者將會失敗。 針對已加入混合式 Azure AD 的裝置，內部部署 Active Directory 是主要授權單位。 當裝置無法看見網域控制站時，就無法驗證新密碼。 因此，使用者必須建立與網域控制站的連線（透過 VPN 或在公司網路中），才能夠使用新密碼登入裝置。 否則，他們只能使用舊密碼登入，因為 Windows 中有快取的登入功能。 不過，舊密碼在權杖要求期間 Azure AD 會失效，因此，會防止單一登入，而且任何裝置型條件式存取原則也會失敗。 如果您使用 Windows Hello 企業版，則不會發生此問題。 

---

## <a name="azure-ad-register-faq"></a>Azure AD 註冊常見問題集

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>問：如何? 在本機移除裝置的 Azure AD 已註冊狀態嗎？

**為** 
- 若為 Windows 10 Azure AD 註冊的裝置，請移至 [**設定**] [帳戶] [  >  **Accounts**  >  **存取公司或學校**]。 選取您的帳戶，然後選取 [中斷連線]****。 裝置註冊是 Windows 10 上的每個使用者設定檔。
- 針對 iOS 和 Android，您可以使用 [Microsoft Authenticator 應用程式**設定**]  >  **裝置註冊**，然後選取 [**取消註冊裝置**]。
- 針對 macOS，您可以使用 Microsoft Intune 公司入口網站應用程式，將裝置從管理中取消註冊，並移除任何註冊。 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>問：如何防止使用者在我的公司 Windows 10 裝置上新增額外的工作帳戶（Azure AD 註冊）？

**答：** 啟用下列登錄來封鎖使用者，將其他工作帳戶新增到已加入公司網域、Azure AD 加入，或加入混合式 Azure AD Windows 10 裝置。 此原則也可以用來封鎖已加入網域的電腦不小心取得使用相同使用者帳戶註冊 Azure AD。 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>問：可以註冊 Android 或 iOS BYOD 裝置嗎？

**答：** 是，但僅適用于 Azure 裝置註冊服務和混合式客戶。 不支援使用「Active Directory 同盟服務」(AD FS) 中的內部部署裝置註冊服務。

---
### <a name="q-how-can-i-register-a-macos-device"></a>問：我要如何註冊 macOS 裝置？

**答：** 請執行下列步驟：

1.    [建立合規性政策](/intune/compliance-policy-create-mac-os)
1.    [定義 macOS 裝置的條件式存取原則](../active-directory-conditional-access-azure-portal.md) 

**標記**

- 條件式存取原則中包含的使用者需要[支援的 Office 版本 macOS](../conditional-access/concept-conditional-access-conditions.md) ，才能存取資源。 
- 在第一次嘗試存取時，系統會提示使用者使用公司入口網站來註冊裝置。

---
## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure AD 裝置註冊](concept-azure-ad-register.md)
- 深入了解[已加入 Azure AD 的裝置](concept-azure-ad-join.md)
- 深入了解[已加入混合式 Azure AD 的裝置](concept-azure-ad-join-hybrid.md)
