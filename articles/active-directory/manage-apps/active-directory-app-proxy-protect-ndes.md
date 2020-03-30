---
title: 在 NDES 伺服器上與 AD 應用程式代理集成
titleSuffix: Azure Active Directory
description: 有關部署 Azure 活動目錄應用程式代理以保護 NDES 伺服器的指導。
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032253"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>在網路設備註冊服務 （NDES） 伺服器上與 Azure AD 應用程式代理集成

Azure 活動目錄 （AD） 應用程式代理允許您在網路內發佈應用程式。 這些應用程式是共用點網站、Microsoft Outlook Web 應用和其他 Web 應用程式。 它還通過 Azure 提供對網路外部使用者的安全訪問。

如果您是 Azure AD 應用程式代理的新功能，並且想要瞭解詳細資訊，請參閱[通過 Azure AD 應用程式代理對本地應用程式進行遠端存取](application-proxy.md)。

Azure AD 應用程式代理在 Azure 上生成。 它為您提供了大量的網路頻寬和伺服器基礎設施，以更好地抵禦分散式阻斷服務 （DDOS） 攻擊和卓越的可用性。 此外，無需向本地網路打開外部防火牆埠，也不需要 DMZ 伺服器。 所有流量都源自入站。 有關出站埠的完整清單，請參閱[教程：在 Azure 活動目錄中通過應用程式代理添加用於遠端存取的本地應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)。

> Azure AD 應用程式代理是一項功能，僅在使用 Azure 活動目錄的高級版或基本版時才可用。 如需詳細資訊，請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。 
> 如果您有企業移動套件 （EMS） 許可證，則有資格使用此解決方案。
> Azure AD 應用程式代理連接器僅在 Windows 伺服器 2012 R2 或更高版本中安裝。 這也是 NDES 伺服器的要求。

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>在 NDES 伺服器上安裝和註冊連接器

1. 以目錄 (使用應用程式 Proxy) 的應用程式管理員身分，登入 [Azure 入口網站](https://portal.azure.com/)。 例如，如果租用戶網域為 contoso.com，則系統管理員應該是 admin@contoso.com ，或該網域上的其他管理員別名。
1. 在右上角選取您的使用者名稱。 請確認您已登入使用應用程式 Proxy 的目錄。 如果您需要變更目錄，請選取 [切換目錄]****，然後選擇會使用應用程式 Proxy 的目錄。
1. 在左瀏覽窗格中，選取 [Azure Active Directory]****。
1. 在 [管理]**** 底下，選取 [應用程式 Proxy]****。
1. 選取 [下載連接器服務]****。

    ![下載連接器服務以查看服務條款](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 閱讀服務條款。 當您準備好時，選取 [接受條款並下載]****。
1. 將 Azure AD 應用程式代理連接器設置檔案複製到 NDES 伺服器。 
   > 您可以在公司網路內具有對 NDES 存取權限的任何伺服器上安裝連接器。 您不必將其安裝在 NDES 伺服器本身上。
1. 回合設定檔，如*AAD應用程式代理連接器安裝程式.exe*。 接受軟體授權條款。
1. 在安裝過程中，系統會提示您將連接器註冊到 Azure AD 目錄中的應用程式代理。
   * 在 Azure AD 目錄中為全域或應用程式管理員提供憑據。 Azure AD 全域或應用程式管理員憑據可能及閘戶中的 Azure 憑據不同。

        > [!NOTE]
        > 用於註冊連接器的全域或應用程式管理員帳戶必須屬於啟用應用程式代理服務的同一目錄。
        >
        > 例如，如果 Azure AD 域*contoso.com，* 則全域/應用程式管理員應為`admin@contoso.com`該域上的另一個有效別名。

   * 如果安裝連接器的伺服器啟用 Internet Explorer 增強的安全配置，則註冊螢幕可能會被阻止。 要允許訪問，請按照錯誤訊息中的說明操作，或在安裝過程中關閉 Internet Explorer 增強的安全性。
   * 如果連接器註冊失敗，請參閱[排除應用程式代理故障](application-proxy-troubleshoot.md)。
1. 在設置結束時，將顯示一個注釋，用於具有出站代理的環境。 要將 Azure AD 應用程式代理連接器配置為通過出站代理工作，請運行提供的腳本，`C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`如 。
1. 在 Azure 門戶中的應用程式代理頁上，新連接器以*活動*狀態列出，如以下示例所示：

    ![在 Azure 門戶中顯示為活動的新 Azure AD 應用程式代理連接器](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > 要為通過 Azure AD 應用程式代理進行身份驗證的應用程式提供高可用性，可以在多個 VM 上安裝連接器。 重複上一節中列出的相同步驟，以在加入到 Azure AD DS 託管域的其他伺服器上安裝連接器。

1. 成功安裝後，返回 Azure 門戶。

1. 選取 [企業應用程式]****。

   ![確保您與合適的利益相關者接洽](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. 選擇 **+新應用程式**，然後選擇**本地應用程式**。 

1. 在 **"添加您自己的本地應用程式中**"，配置以下欄位：

   * **名稱**：輸入應用程式的名稱。
   * **內部 URL**： 輸入您安裝連接器的 NDES 伺服器的內部 URL/FQDN。
   * **預身份驗證**：選擇**傳遞**。 無法使用任何形式的預身份驗證。 用於證書請求 （SCEP） 的協定不提供此類選項。
   * 將提供的**外部 URL**複製到剪貼簿。

1. 選擇 **"添加"** 以保存應用程式。

1. 通過將步驟 10 中複製的連結粘貼到瀏覽器中，測試是否可以通過 Azure AD 應用程式代理訪問 NDES 伺服器。 您應該會看到預設的 IIS 歡迎頁面。

1. 作為最終測試，將*mscep.dll*路徑添加到上一步驟中粘貼的現有 URL：

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. 您應該會看到**HTTP 錯誤 403 = 禁止**回應。

1. 將提供的 NDES URL（通過 Microsoft Intune）更改為設備，此更改可能位於 Microsoft 端點配置中心或 Intune 雲中。

   * 對於配置中心，請轉到憑證註冊點 （CRP） 並調整 URL。 此 URL 是設備向它們發出並提出其挑戰的內容。
   * 對於僅 Intune 雲（也稱為 Intune 獨立），編輯或創建新的 SCEP 策略並添加新 URL。

## <a name="next-steps"></a>後續步驟

將 Azure AD 應用程式代理與 NDES 集成後，發佈應用程式供使用者訪問。 有關詳細資訊，請參閱使用[Azure AD 應用程式代理髮布應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。
