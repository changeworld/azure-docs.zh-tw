---
title: 使用 Azure MFA 伺服器搭配 AD FS 2.0-Azure Active Directory
description: 這是說明如何開始使用 Azure MFA 和 AD FS 2.0 的 Azure Multi-Factor Authentication 頁面。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13cfed6bf378ff9dacc4b6446cdfc246a87949bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994225"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>設定 Azure Multi-Factor Authentication Server 以與 AD FS 2.0 搭配運作

本文適用對象為使用 Azure Active Directory 進行同盟，並想要保護內部部署資源或雲端資源的組織。 請使用 Azure Multi-factor Authentication Server 保護您的資源，並將其設定為可與 AD FS 搭配運作，以便觸發雙步驟驗證來獲得高價值的端點。

這份文件將說明如何搭配 AD FS 2.0 使用 Azure Multi-Factor Authentication Server。 如需 AD FS 的相關資訊，請參閱[搭配 Windows Server 2012 R2 AD FS 使用 Azure Multi-Factor Authentication Server 保護雲端和內部部署資源](howto-mfaserver-adfs-2012.md)。

> [!IMPORTANT]
> 從2019年7月1日起，Microsoft 不再為新的部署提供 MFA Server。 想要在登入事件期間 (MFA) 要求多重要素驗證的新客戶應該使用雲端式 Azure AD Multi-Factor Authentication。
>
> 若要開始使用雲端式 MFA，請參閱 [教學課程：使用 Azure AD Multi-Factor Authentication 保護使用者登入事件](tutorial-enable-azure-mfa.md)。
>
> 如果您使用以雲端為基礎的 MFA，請參閱 [使用 Azure AD Multi-Factor Authentication 保護雲端資源和 AD FS](howto-mfa-adfs.md)。
>
> 在2019年7月1日前啟用 MFA Server 的現有客戶，可以下載最新版本、未來的更新，並照常產生啟用認證。

## <a name="secure-ad-fs-20-with-a-proxy"></a>使用 Proxy 保護 AD FS 2.0

若要使用 Proxy 保護 AD FS 2.0，請在 ADFS Proxy 伺服器上安裝 Azure Multi-Factor Authentication Server。

### <a name="configure-iis-authentication"></a>設定 IIS 驗證

1. 在 Azure Multi-Factor Authentication Server 中，按一下左側功能表中的 [ **IIS 驗證** ] 圖示。
2. 按一下 [表單架構] 索引標籤。
3. 按一下 [新增] 。

   ![MFA Server IIS 驗證視窗](./media/howto-mfaserver-adfs-2/setup1.png)

4. 若要自動偵測使用者名稱、密碼和網域變數，請在 `https://sso.contoso.com/adfs/ls` [自動設定 Form-Based 網站] 對話方塊中輸入登入 URL (例如) ，然後按一下 **[確定]**。
5. 如果所有使用者都已經或將要匯入到「伺服器」，且必須接受雙步驟驗證，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對] 方塊。 如果有大量使用者尚未匯入伺服器及/或將免除雙步驟驗證，請勿核取此方塊。
6. 如果無法自動偵測頁面變數，請按一下 [自動設定表單架構網站] 對話方塊中的 [手動指定...] 按鈕。
7. 在 [新增 Form-Based 網站] 對話方塊中，于 [提交 URL] 欄位中輸入 AD FS 登入頁面的 URL (例如 `https://sso.contoso.com/adfs/ls`) ，然後輸入 (選擇性) 的應用程式名稱。 應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。
8. 將要求格式設定為 **POST 或 GET**。
9. 輸入使用者名稱變數 (ctl00$ContentPlaceHolder1$UsernameTextBox) 和密碼變數 (ctl00$ContentPlaceHolder1$PasswordTextBox)。 如果表單架構登入頁面顯示網域文字方塊，請輸入網域變數。 若要尋找登入頁面內的輸入方塊名稱，請移至網頁瀏覽器中的登入頁面，在頁面上按一下滑鼠右鍵，並選取 [檢視來源]。
10. 如果所有使用者都已經或將要匯入到「伺服器」，且必須接受雙步驟驗證，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對] 方塊。 如果有大量使用者尚未匯入伺服器及/或將免除雙步驟驗證，請勿核取此方塊。

    ![將以表單為基礎的網站新增至 MFA Server](./media/howto-mfaserver-adfs-2/manual.png)

11. 按一下 [ **Advanced] ...** 以檢閱進階設定。 您可以進行的設定包括︰

    - 選取自訂拒絕頁面檔案
    - 使用 Cookie 快取網站的成功驗證
    - 選取如何驗證主要認證

12. 因為 AD FS Proxy 伺服器不可能加入網域，您可以使用 LDAP 連接到您的網域控制站，以便使用者匯入和預先驗證。 在 [Advanced Form-Based 網站] 對話方塊中，按一下 [ **主要驗證** ] 索引標籤，然後針對 [預先驗證驗證類型] 選取 [ **LDAP** 系結]。
13. 完成時，按一下 [確定] 以返回 [新增表單架構網站] 對話方塊。
14. 按一下 **[確定]** ，關閉對話方塊。
15. 偵測到或已輸入 URL 和頁面變數後，網站資料就會顯示在表單架構面板中。
16. 按一下 [ **原生模組** ] 索引標籤，然後選取伺服器、AD FS proxy 執行所在的網站 (例如「預設的網站」 ) ，或 AD FS proxy 應用程式 (例如 "adfs" 下的 "ls"，以在所需的層級啟用 IIS 外掛程式。
17. 按一下畫面頂端的 [啟用 IIS 驗證] 方塊。

現已啟用 IIS 驗證。

### <a name="configure-directory-integration"></a>設定目錄整合

您已啟用 IIS 驗證，但必須設定網域控制站的 LDAP 連線，才能透過 LDAP 執行 Active Directory (AD) 的預先驗證。

1. 按一下 [目錄整合] 圖示。
2. 在 [設定] 索引標籤上，選取 [使用特定 LDAP 設定] 選項按鈕。

   ![設定特定 LDAP 設定的 LDAP 設定](./media/howto-mfaserver-adfs-2/ldap1.png)

3. 按一下 **[編輯]** 。
4. 在 [編輯 LDAP 設定] 對話方塊的欄位中，填入連接到 AD 網域控制站所需的資訊。 欄位說明包含在 Azure Multi-Factor Authentication Server 說明檔中。
5. 按一下 [測試] 按鈕來測試 LDAP 連接。

   ![在 MFA Server 中測試 LDAP 設定](./media/howto-mfaserver-adfs-2/ldap2.png)

6. 如果 LDAP 連接測試成功，請按一下 [確定]。

### <a name="configure-company-settings"></a>進行公司設定

1. 接著，按一下 [公司設定] 圖示，然後選取 [使用者名稱解析] 索引標籤。
2. 選取 [使用 LDAP 唯一識別碼屬性來比對使用者名稱] 選項按鈕。
3. 如果使用者以「網域 \ 使用者名稱」的格式輸入使用者名稱，則在建立 LDAP 查詢時，伺服器必須能夠從使用者名稱中移除網域。 這個動作可透過登錄設定完成。
4. 開啟登錄編輯程式並移至 64 位元伺服器上的 HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor。 如果是在32位伺服器上，請將 "Wow6432Node" 從路徑中取出。 建立名為 "UsernameCxz_stripPrefixDomain" 的 DWORD 登錄機碼，並將值設定為1。 Azure Multi-Factor Authentication 現已保護 AD FS Proxy。

確定已將使用者從 Active Directory 匯入伺服器中。 如果您想要允許內部 IP 位址，以便在從那些位置登入網站時不需要雙步驟驗證，請參閱 [信任的 ip 區段](#trusted-ips) 。

![設定公司設定的登錄編輯程式](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>沒有 Proxy 的 AD FS 2.0 Direct

您可以在未使用 AD FS Proxy 的情況下保護 AD FS。 請在 AD FS 伺服器上安裝 Azure Multi-Factor Authentication Server，並依照下列步驟設定伺服器：

1. 在 Azure Multi-Factor Authentication Server 內，按一下左功能表中的 [IIS 驗證] 圖示。
2. 按一下 [HTTP] 索引標籤。
3. 按一下 [新增] 。
4. 在 [新增基底 URL] 對話方塊中，輸入執行 HTTP 驗證之 AD FS 網站的 URL (例如 `https://sso.domain.com/adfs/ls/auth/integrated` [基底 URL] 欄位中的) 。 然後，輸入應用程式名稱 (選擇性)。 應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。
5. 如有需要，請調整 [閒置逾時] 和 [最大工作階段] 時間。
6. 如果所有使用者都已經或將要匯入到「伺服器」，且必須接受雙步驟驗證，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對] 方塊。 如果有大量使用者尚未匯入伺服器及/或將免除雙步驟驗證，請勿核取此方塊。
7. 如有需要，請核取 Cookie 快取方塊。

   ![沒有 Proxy 的 AD FS 2.0 Direct](./media/howto-mfaserver-adfs-2/noproxy.png)

8. 按一下 [確定]。
9. 按一下 [ **原生模組** ] 索引標籤，然後選取伺服器、網站 (例如「預設的網站」 ) ，或 AD FS 應用程式 (例如 "adfs ) " 下的 "ls"，以在所需的層級啟用 IIS 外掛程式。
10. 按一下畫面頂端的 [啟用 IIS 驗證] 方塊。

Azure Multi-Factor Authentication 現已保護 AD FS。

確定已將使用者從 Active Directory 匯入伺服器中。 如果您想要允許內部 IP 位址，以便在從那些位置登入網站時不需要雙步驟驗證，請參閱信任的 Ip 區段。

## <a name="trusted-ips"></a>信任的 IP

信任的 IP 可讓使用者對源自特定 IP 位址或子網路的網站要求略過 Azure Multi-Factor Authentication。 例如，您可能想讓使用者在從辦公室登入時不必進行雙步驟驗證。 為此，您可以將辦公室子網路指定為信任的 IP 項目。

### <a name="to-configure-trusted-ips"></a>設定信任的 IP

1. 在 [IIS 驗證] 區段中，按一下 [信任的 IP] 索引標籤。
2. 按一下 [**新增 ...** ] 按鈕。
3. 當 [新增信任的 IP] 對話方塊出現時，請選取 [單一 IP]、[IP 範圍] 或 [子網路] 選項按鈕的其中一個。
4. 輸入應允許的 IP 位址、IP 位址範圍或子網。 如果輸入子網路，請選取適當的網路遮罩，然後按一下 [確定] 按鈕。

![設定 MFA Server 的信任 Ip](./media/howto-mfaserver-adfs-2/trusted.png)
