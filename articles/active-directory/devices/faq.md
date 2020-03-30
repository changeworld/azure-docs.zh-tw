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
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497529"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 裝置管理常見問題集

## <a name="general-faq"></a>一般常見問題解答

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>問：我最近註冊了裝置。 為什麼我無法在 Azure 門戶中的使用者資訊下看到設備？ 或為什麼就已加入混合式 Azure Active Directory (Azure AD) 的裝置而言，裝置擁有者被標示為 N/A？

**答：** 混合 Azure AD 聯接的 Windows 10 設備不會顯示在**USER 設備**下。
請使用 Azure 入口網站中的 [所有裝置]**** 檢視。 您也可以使用 PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) Cmdlet。

只有下列裝置會列在 [使用者裝置]**** 底下：

- 所有未加入混合式 Azure AD 的個人裝置。 
- 所有非 Windows 10 或 Windows Server 2016 裝置。
- 所有非 Windows 裝置。 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>問：我要如何知道用戶端的裝置註冊狀態為何？

**答：** 在 Azure 門戶中，轉到**所有設備**。 使用裝置識別碼來搜尋裝置。 檢查加入類型資料行中的值。 有時，可能會將裝置重設或重新安裝映像。 因此，檢查裝置上的裝置註冊狀態也很重要：

- 針對 Windows 10 和 Windows Server 2016 或更新的裝置，請執行 `dsregcmd.exe /status`。
- 針對舊版 OS 版本，請執行`%programFiles%\Microsoft Workplace Join\autoworkplace.exe`。

**答：** 有關故障排除資訊，請參閱以下文章：
- [使用 dsregcmd 命令對設備進行故障排除](troubleshoot-device-dsregcmd.md)
- [針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)
- [故障排除混合 Azure 活動目錄聯接的低級設備](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>問：我在 Azure 門戶中的 USER 資訊下看到設備記錄。 並且看到該裝置上的狀態為已註冊。 我是否正確設置以使用條件訪問？

**答：** 設備聯接狀態（由設備**ID**顯示）必須與 Azure AD 上的狀態匹配，並滿足條件訪問的任何評估條件。 有關詳細資訊，請參閱[使用條件訪問需要託管設備進行雲應用訪問](../conditional-access/require-managed-devices.md)。

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>問：為什麼我的使用者在其 Windows 10 設備上看到一條錯誤訊息，指出"您的組織已刪除設備"或"您的組織已禁用設備"？

**答：** 在加入或註冊 Azure AD 的 Windows 10 設備上，將發出啟用單一登入[的主刷新權杖 （PRT）。](concept-primary-refresh-token.md) PRT 的有效性基於設備本身的有效性。 如果在 Azure AD 中刪除或禁用設備，而不從設備本身啟動操作，則使用者會看到此消息。 可以在 Azure AD 中刪除或禁用設備，這是以下方案之一： 

- 使用者從"我的應用"門戶禁用設備。 
- 管理員（或使用者）在 Azure 門戶或使用 PowerShell 刪除或禁用設備
- 僅加入混合 Azure AD：管理員刪除設備 OU 不同步範圍，導致設備從 Azure AD 中刪除
- 升級 Azure AD 連接到版本 1.4.xx.x。 [瞭解 Azure AD 連接 1.4.xx.x 和設備消失](/azure/active-directory/hybrid/reference-connect-device-disappearance)。


請參閱下文，瞭解如何糾正這些操作。

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>問：我在 Azure 門戶或使用 Windows PowerShell 禁用或刪除了設備。 但裝置上的本機狀態顯示裝置仍處於已註冊狀態。 我該怎麼辦？

**答：** 此操作是設計操作。 在這種情況下，設備無法訪問雲中的資源。 管理員可以針對陳舊、丟失或被盜的設備執行此操作，以防止未經授權的訪問。 如果此操作是無意執行的，則需要重新啟用或重新註冊設備，如下所述

- 如果在 Azure AD 中禁用了該設備，則具有足夠許可權的管理員可以從 Azure AD 門戶啟用該設備  
  > [!NOTE]
  > 如果使用 Azure AD 連接同步設備，則混合 Azure AD 聯接設備將在下一個同步週期內自動重新啟用。 因此，如果需要禁用混合 Azure AD 聯接設備，則需要從本地 AD 禁用該設備

 - 如果在 Azure AD 中刪除了設備，則需要重新註冊設備。 要重新註冊，您必須對設備執行手動操作。 有關根據設備狀態重新註冊的說明，請參閱下文。 

      要重新註冊混合 Azure AD 加入 Windows 10 和 Windows 伺服器 2016/2019 設備，請執行以下步驟：

      1. 以系統管理員身分開啟命令提示字元。
      1. 輸入 `dsregcmd.exe /debug /leave`。
      1. 登出後再登入，以觸發向 Azure AD 重新註冊裝置的排定工作。 

      對於混合 Azure AD 聯接的低級 Windows 作業系統版本，請執行以下步驟：

      1. 以系統管理員身分開啟命令提示字元。
      1. 輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`。
      1. 輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`。

      對於 Azure AD 聯接設備 Windows 10 設備，請執行以下步驟：

      1. 以系統管理員身分開啟命令提示字元
      1. 輸入`dsregcmd /forcerecovery`（注意：您需要是管理員才能執行此操作）。
      1. 按一下對話方塊中的"登錄"，該對話方塊將打開並繼續登錄過程。
      1. 登出並登錄到設備以完成恢復。

      對於 Azure AD 註冊的 Windows 10 設備，請執行以下步驟：

      1. 轉到**設置** > **帳戶** > **訪問工作或學校**。 
      1. 選擇帳戶並選擇 **"斷開連接**"。
      1. 按一下"+ 連接"，通過登錄過程再次註冊設備。

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>問：為什麼我在 Azure 門戶中看到重複的設備條目？

**答：**

- 就 Windows 10 和 Windows Server 2016 而言，如果重複嘗試將同一個裝置取消加入再重新加入，就可能導致產生重複的項目。 
- 每個使用 [新增工作或學校帳戶]**** 的 Windows 使用者都會以相同的裝置名稱建立一個新裝置記錄。
- 針對已加入內部部署 Active Directory 網域的舊版 Windows OS 版本，自動註冊會為每個登入裝置的網域使用者，以相同的裝置名稱建立一個新裝置記錄。 
- 已加入 Azure AD 的機器如果經過抹除、重新安裝，然後再以相同名稱重新加入，就會顯示成另一個具有相同裝置名稱的記錄。

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>問：Azure AD 中的 Windows 10 設備註冊是否支援 FIPS 模式下的 TM？

**答：** Windows 10 設備註冊僅支援符合 FIPS 的 TPM 2.0，不支援 TPM 1.2。 如果設備具有符合 FIPS 的 TPM 1.2，則必須在繼續 Azure AD 聯接或混合 Azure AD 聯接之前禁用它們。 Microsoft 不提供任何用於禁用 TPM FIPS 模式的工具，因為它依賴于 TPM 製造商。 請與您的硬體 OEM 聯繫以獲取支援。 

---

**問：為什麼使用者仍可以從我在 Azure 門戶中禁用的設備訪問資源？**

**答：** 從 Azure AD 設備標記為禁用時開始，最多需要一個小時才能應用撤銷。

>[!NOTE] 
>針對已註冊的裝置，建議您將裝置抹除，以確保使用者無法存取資源。 如需詳細資訊，請參閱[什麼是裝置註冊？](/mem/intune/user-help/use-managed-devices-to-get-work-done)。 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>問：為什麼 Azure 門戶中的"已註冊"列下有設備標記為"掛起"？

**A**：掛起表示設備未註冊。 此狀態表示設備已使用從本地 AD 連接的 Azure AD 進行同步，並可供設備註冊。 這些設備的 JOIN 類型設置為"混合 Azure AD 已加入"。 詳細瞭解[如何規劃混合 Azure 活動目錄聯接實現](hybrid-azuread-join-plan.md)。

>[!NOTE]
>設備也可以從註冊狀態更改為"掛起"
>* 如果設備首先從 Azure AD 中刪除，並從本地 AD 重新同步。
>* 如果設備從 Azure AD 連接上的同步作用域中刪除並添加回來。
>
>在這兩種情況下，都必須在每台設備上手動重新註冊設備。 要查看設備是否以前已註冊，可以使用[dsregcmd 命令對設備進行故障排除](troubleshoot-device-dsregcmd.md)。

---
## <a name="azure-ad-join-faq"></a>Azure AD Join 常見問題集

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>問：如何在設備上本地取消加入 Azure AD 加入的設備？

**答：** 對於純 Azure AD 聯接的設備，請確保具有離線本地管理員帳戶或創建一個。 您無法使用任何 Azure AD 使用者認證來登入。 接下來，轉到**設置** > **帳戶** > **訪問工作或學校**。 選取您的帳戶，然後選取 [中斷連線]****。 遵循提示，然後在系統提示您時提供本機系統管理員認證。 重新啟動裝置以完成退出程序。

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>問：我的使用者能否登錄到 Azure AD 中已刪除或禁用的 Azure AD 加入的設備？

**答：** 是的。 Windows 具有快取使用者名稱和密碼的功能，可讓先前曾登入的使用者即使沒有網路連線，也能快速存取桌面。 

當裝置在 Azure AD 中被刪除或停用時，Windows 裝置並不知道此狀況。 所以先前曾登入的使用者能夠繼續以快取的使用者名稱和密碼存取桌面。 但是，由於設備被刪除或禁用，使用者無法訪問受基於設備的條件訪問保護的任何資源。 

先前未曾登入的使用者無法存取裝置。 沒有任何已為他們啟用的快取使用者名稱和密碼。 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>問：禁用或刪除的使用者能否登錄到 Azure AD 加入的設備

**答：** 是的，但只限時間。 當使用者在 Azure AD 中被刪除或停用時，Windows 裝置並不會立即得知此狀況。 所以先前曾登入的使用者能夠以快取的使用者名稱和密碼存取桌面。 

一般而言，裝置會在四小時內得知使用者狀態。 然後 Windows 就會封鎖這些使用者對桌面的存取。 由於使用者已在 Azure AD 中被刪除或停用，因此系統會撤銷他們的所有權杖。 所以他們無法存取任何資源。 

已被刪除或停用的使用者如果先前未曾登入，便無法存取裝置。 沒有任何已為他們啟用的快取使用者名稱和密碼。 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>問：為什麼我的使用者在更改其 UPN 後 Azure AD 加入設備上出現問題？

**答：** 目前，Azure AD 加入的設備並不完全支援 UPN 更改。 因此，他們在變更其 UPN 之後，會在向 Azure AD 驗證時失敗。 所以使用者會在其裝置上遇到 SSO 和條件式存取的問題。 目前，使用者必須透過 [其他使用者] 圖格以其新的 UPN 登入 Windows 來解決此問題。 我們目前正努力解決此問題。 不過，使用 Windows Hello 企業版來登入的使用者不會遇到這個問題。 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>問：我的使用者無法從 Azure AD 聯接的設備搜索印表機。 如何從這些裝置啟用列印功能？

**答：** 要為 Azure AD 聯接的設備部署印表機，請參閱[使用預身份驗證部署 Windows 伺服器混合雲列印](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)。 您需要有內部部署的 Windows Server，才能部署混合式雲端列印。 目前尚無法使用雲端式列印服務。 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>問：如何連接到遠端 Azure AD 連接設備？

**答：** 請參閱[連接到遠端 Azure 活動目錄加入的 PC](/windows/client-management/connect-to-remote-aadj-pc)。

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>問：為什麼我的使用者看到*您無法從這裡到達那裡*？

**答：** 您是否將某些條件訪問規則配置為需要特定的設備狀態？ 如果裝置不符合準則，使用者就會被封鎖而看到該訊息。 評估條件訪問策略規則。 確定裝置符合準則，以避免收到該訊息。

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>問：為什麼我的某些使用者在 Azure AD 加入的設備上未收到 Azure 多重要素驗證提示？

**答：** 使用者可以使用多重要素驗證加入或註冊 Azure AD 的設備。 如此一來，裝置本身就會變成該使用者的第二受信任要素。 每當同一個使用者登入裝置並存取應用程式時，Azure AD 都會將該裝置視為第二個要素。 它可讓該使用者順暢地存取應用程式，而不會出現額外的 Multi-Factor Authentication 提示。 

此行為：

- 適用於 Azure AD 已加入和 Azure AD 已註冊裝置，但不適用於混合式 Azure AD 已加入裝置。
- 它不適用於任何其他登入該裝置的使用者。 所以所有其他存取該裝置的使用者都會收到 Multi-Factor Authentication 挑戰。 然後，他們便可存取要求 Multi-Factor Authentication 的應用程式。

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>問：為什麼對於剛剛加入 Azure AD 的設備，我收到*使用者名或密碼的消息不正確*？

**答：** 此方案的常見原因如下：

- 您的使用者認證已經無效。
- 您的電腦無法與 Azure Active Directory 進行通訊。 請檢查是否有任何網路連線問題。
- 同盟登入會要求同盟伺服器支援已啟用並可供存取的 WS-Trust 端點。 
- 您已啟用傳遞驗證。 所以當您登入時，必須變更暫時密碼。

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>問：為什麼我會看到*哎呀...發生錯誤！* 嘗試 Azure AD 加入電腦時，請進行對話？

**答：** 使用 Intune 設置 Azure 活動目錄註冊時，將發生此錯誤。 請確定嘗試加入 Azure AD 的使用者已獲指派正確的 Intune 授權。 如需詳細資訊，請參閱[設定 Windows 裝置的註冊](/intune/windows-enroll)。  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>問：為什麼我嘗試 Azure AD 加入 PC 失敗，儘管我沒有得到任何錯誤資訊？

**答：** 一個可能的原因是您使用本地內置管理員帳戶登錄到設備。 請在使用 Azure Active Directory Join 來完成設定之前，先建立一個不同的本機帳戶。 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>問：我們 Windows 10 裝置上有哪些 MS-Organization-P2P-Access 憑證？

**答：** MS-組織-P2P-訪問證書由 Azure AD 頒發給 Azure AD 聯接的設備以及混合 Azure AD 聯接的設備。 這些憑證可用來啟用相同租用戶中裝置間的信任，以進行遠端桌面存取。 一個憑證簽發給裝置，另一個憑證則簽發給使用者。 裝置憑證位於 `Local Computer\Personal\Certificates` 中，且有效期為一天。 如果裝置在 Azure AD 中仍處於作用中，系統就會更新此憑證 (透過簽發新憑證的方式)。 使用者憑證位於 `Current User\Personal\Certificates` 中，此憑證的有效期也是一天，但它是在使用者嘗試對另一個已加入 Azure AD 的裝置建立遠端桌面工作階段時視需要簽發的憑證。 系統並不會在其到期時予以更新。 這兩種憑證都是使用 `Local Computer\AAD Token Issuer\Certificates` 中的 MS-Organization-P2P-Access 憑證來簽發的。 而此憑證則是由 Azure AD 在裝置註冊期間所簽發的。 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>問：為什麼在我們的 Windows 10 裝置上看到多個由 MS-Organization-P2P-Access 簽發的過期憑證？ 如何刪除它們？

**答：** 在 Windows 10 版本 1709 上發現問題，在由於加密問題而電腦存儲中仍然存在過期的 MS-組織-P2P-Access 證書的較低版本。 如果您正在使用無法處理大量過期證書的任何 VPN 用戶端（例如，Cisco AnyConnect），則使用者可能面臨網路連接問題。 此問題在 Windows 10 1803 版中已修正，可自動刪除任何這類過期的 MS-Organization-P2P-Access 憑證。 您可以將裝置更新至 Windows 10 1803 來解決此問題。 如果無法更新，您可以刪除這些憑證，而不會產生任何負面影響。  

---

## <a name="hybrid-azure-ad-join-faq"></a>混合式 Azure AD Join 常見問題集

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>問：如何在設備上本地取消加入混合 Azure AD 加入的設備？

**答：** 對於混合 Azure AD 聯接設備，請確保關閉自動註冊。 如此一來，排定的工作就不會重新註冊該裝置。 接著，以系統管理員身分開啟命令提示字元，然後輸入 `dsregcmd.exe /debug /leave`。 或者，若要進行大量退出，請以指令碼方式跨多個裝置執行此命令。

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>問：在哪裡可以找到故障排除資訊來診斷混合 Azure AD 聯接失敗？

**答：** 有關故障排除資訊，請參閱以下文章：

- [針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)
- [故障排除混合 Azure 活動目錄聯接的低級設備](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>問：為什麼在 Azure AD 設備清單中看到 Windows 10 混合 Azure AD 加入設備的重複 Azure AD 註冊記錄？

**答：** 當使用者將帳戶添加到加入域的設備上的應用時，可能會提示使用者**將帳戶添加到 Windows？** 如果他們在出現提示時輸入**是**，裝置就會向 Azure AD 註冊。 信任類型會標示為 [Azure AD 已註冊]。 在您於組織中啟用混合式 Azure AD Join 之後，裝置也會加入混合式 Azure AD。 如此一來，針對同一個裝置就會顯示兩個裝置狀態。 

混合式 Azure AD Join 的優先順序會高於 Azure AD 已註冊狀態。 因此，您的設備被視為混合 Azure AD 加入用於任何身份驗證和條件訪問評估。 您可以從 Azure AD 入口網站中放心地刪除 Azure AD 已註冊裝置記錄。 了解如何[在 Windows 10 機器上避免或清除此雙重狀態](hybrid-azuread-join-plan.md#review-things-you-should-know)。 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>問：為什麼我的使用者在更改其 UPN 後 Windows 10 混合 Azure AD 加入設備上出現問題？

**答：** 目前，混合 Azure AD 聯接設備未完全支援 UPN 更改。 雖然使用者可以登入裝置，並存取其內部部署應用程式，但在變更 UPN 之後，向 Azure AD 進行驗證會失敗。 所以使用者會在其裝置上遇到 SSO 和條件式存取的問題。 此時，您需要從 Azure AD 中取消加入設備（使用較高的權限運行"dsregcmd /leave"）並重新加入（自動）以解決此問題。 我們目前正努力解決此問題。 不過，使用 Windows Hello 企業版來登入的使用者不會遇到這個問題。 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>問：Windows 10 混合 Azure AD 聯接設備是否需要網域控制站的視線才能訪問雲資源？

**答：** 否，除非更改使用者的密碼。 Windows 10 混合 Azure AD 聯接完成後，使用者至少登錄過一次，設備不需要網域控制站的視線來訪問雲資源。 Windows 10 可以從具有 Internet 連接的任意位置對 Azure AD 應用程式進行單一登入，除非更改密碼。 使用 Windows Hello 為業務登錄的使用者即使在密碼更改後仍繼續獲得 Azure AD 應用程式的單一登入，即使他們的網域控制站沒有視線。 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>問：如果使用者更改其密碼並嘗試登錄到其 Windows 10 混合 Azure AD 加入設備，則商業網路外部會發生什麼情況？

**答：** 如果在公司網路之外更改了密碼（例如，通過使用 Azure AD SSPR），則使用新密碼登錄的使用者將失敗。 對於混合 Azure AD 聯接設備，本地活動目錄是主要許可權。 當設備對網域控制站沒有視線時，它無法驗證新密碼。 因此，使用者需要建立與網域控制站的連接（通過 VPN 或在公司網路中），然後才能使用新密碼登錄到設備。 否則，由於 Windows 中的緩存登錄功能，他們只能使用舊密碼登錄。 但是，在權杖請求期間，Azure AD 將無效使用舊密碼，從而阻止單次登錄並失敗任何基於設備的條件訪問策略。 如果您為企業使用 Windows Hello，則不會出現此問題。 

---

## <a name="azure-ad-register-faq"></a>Azure AD 註冊常見問題集

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>問：如何刪除本地設備的 Azure AD 註冊狀態？

**答：** 
- 對於 Windows 10 Azure AD 註冊設備，轉到**設置** > **帳戶** > **訪問工作或學校**。 選取您的帳戶，然後選取 [中斷連線]****。 設備註冊是 Windows 10 上的每個使用者設定檔。
- 對於 iOS 和 Android，您可以使用 Microsoft 身份驗證器應用程式**設置** > **設備註冊**並選擇 **"取消註冊設備**"。
- 對於 macOS，您可以使用 Microsoft Intune 公司門戶應用程式從管理中取消註冊設備並刪除任何註冊。 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>問：如何阻止使用者在我的公司 Windows 10 設備上添加其他工作帳戶（Azure AD 已註冊）？

**答：** 啟用以下註冊表可阻止使用者將其他工作帳戶添加到已加入、Azure AD 加入或混合 Azure AD 加入 Windows 10 設備。 此策略還可用於阻止加入域的電腦無意中將 Azure AD 註冊到同一使用者帳戶。 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>問：可以註冊 Android 或 iOS BYOD 裝置嗎？

**答：** 是，但僅適用于 Azure 設備註冊服務和混合客戶。 不支援使用「Active Directory 同盟服務」(AD FS) 中的內部部署裝置註冊服務。

---
### <a name="q-how-can-i-register-a-macos-device"></a>問：我要如何註冊 macOS 裝置？

**答：** 執行以下步驟：

1.    [建立裝置相容性原則](/intune/compliance-policy-create-mac-os)
1.    [為 macOS 裝置定義條件訪問策略](../active-directory-conditional-access-azure-portal.md) 

**言論：**

- 條件訪問策略中包含的使用者需要[支援版本的 Office 才能](../conditional-access/concept-conditional-access-conditions.md)訪問資源。 
- 在第一次嘗試存取時，系統會提示使用者使用公司入口網站來註冊裝置。

---
## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure AD 裝置註冊](concept-azure-ad-register.md)
- 深入了解[已加入 Azure AD 的裝置](concept-azure-ad-join.md)
- 深入了解[已加入混合式 Azure AD 的裝置](concept-azure-ad-join-hybrid.md)
