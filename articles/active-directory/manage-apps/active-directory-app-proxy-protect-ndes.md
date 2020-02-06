---
title: 與 NDES 伺服器上的 AD 應用程式 Proxy 整合
titleSuffix: Azure Active Directory
description: 部署 Azure Active Directory 應用程式 Proxy 以保護 NDES 伺服器的指引。
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
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032253"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>與網路裝置註冊服務（NDES）伺服器上 Azure AD 應用程式 Proxy 整合

Azure Active Directory （AD）應用程式 Proxy 可讓您將應用程式發佈到您的網路內。 這些應用程式包括 SharePoint 網站、Microsoft Outlook Web 應用程式，以及其他 Web 應用程式。 它也可讓您透過 Azure，安全地存取您網路外部的使用者。

如果您不熟悉 Azure AD 應用程式 Proxy，而且想要深入瞭解，請參閱[透過 Azure AD 應用程式 Proxy 遠端存取內部部署應用程式](application-proxy.md)。

Azure AD 應用程式 Proxy 建置於 Azure 上。 它提供您大量的網路頻寬和伺服器基礎結構，以提供更佳的保護，以抵禦分散式阻斷服務（DDOS）攻擊和卓越的可用性。 此外，不需要對您的內部部署網路開啟外部防火牆埠，也不需要 DMZ 伺服器。 所有流量都是源自輸入。 如需輸出埠的完整清單，請參閱[教學課程：在 Azure Active Directory 中新增透過應用程式 Proxy 進行遠端存取的內部部署應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)。

> Azure AD 應用程式 Proxy 是一項功能，只有在您使用 Premium 或 Basic 版本的 Azure Active Directory 時才能使用。 如需詳細資訊，請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。 
> 如果您有企業行動套件（EMS）授權，就有資格使用此解決方案。
> Azure AD 應用程式 Proxy 連接器只會安裝在 Windows Server 2012 R2 或更新版本上。 這也是 NDES 伺服器的需求。

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>在 NDES 伺服器上安裝並註冊連接器

1. 以目錄 (使用應用程式 Proxy) 的應用程式管理員身分，登入 [Azure 入口網站](https://portal.azure.com/)。 例如，如果租用戶網域為 contoso.com，則系統管理員應該是 admin@contoso.com ，或該網域上的其他管理員別名。
1. 在右上角選取您的使用者名稱。 請確認您已登入使用應用程式 Proxy 的目錄。 如果您需要變更目錄，請選取 [切換目錄]，然後選擇會使用應用程式 Proxy 的目錄。
1. 在左瀏覽窗格中，選取 [Azure Active Directory]。
1. 在 [管理] 底下，選取 [應用程式 Proxy]。
1. 選取 [下載連接器服務]。

    ![下載連接器服務以查看服務條款](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 閱讀服務條款。 當您準備好時，選取 [接受條款並下載]。
1. 將 Azure AD 應用程式 Proxy 連接器安裝程式檔案複製到您的 NDES 伺服器。 
   > 您可以使用 NDES 的存取權，在公司網路內的任何伺服器上安裝連接器。 您不需要將它安裝在 NDES 伺服器本身。
1. 執行安裝檔，例如*aadapplicationproxyconnectorinstaller.exe。* 接受軟體授權條款。
1. 在安裝期間，系統會提示您在 Azure AD 目錄中向應用程式 Proxy 註冊連接器。
   * 在您的 Azure AD 目錄中，提供全域或應用程式系統管理員的認證。 Azure AD 全域或應用程式系統管理員認證可能與您在入口網站中的 Azure 認證不同。

        > [!NOTE]
        > 用來註冊連接器的全域或應用程式系統管理員帳戶，必須屬於您啟用應用程式 Proxy 服務的相同目錄。
        >
        > 例如，如果 Azure AD 網域是*contoso.com*，則全域/應用程式系統管理員應該 `admin@contoso.com` 或該網域上的另一個有效別名。

   * 如果您安裝連接器的伺服器已開啟 [Internet Explorer 增強式安全性設定]，則可能會封鎖註冊畫面。 若要允許存取，請遵循錯誤訊息中的指示，或在安裝過程中關閉 Internet Explorer 增強式安全性。
   * 如果連接器註冊失敗，請參閱針對[應用程式 Proxy 進行疑難排解](application-proxy-troubleshoot.md)。
1. 在安裝結束時，會顯示具有輸出 proxy 之環境的附注。 若要設定 Azure AD 應用程式 Proxy 連接器以透過輸出 Proxy 運作，請執行提供的腳本，例如 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`。
1. 在 Azure 入口網站的 [應用程式 proxy] 頁面上，新的連接器會列示為 [*作用中] 狀態，如*下列範例所示：

    ![新的 Azure AD 應用程式 Proxy 連接器在 Azure 入口網站中顯示為作用中](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > 若要為透過 Azure AD 應用程式 Proxy 進行驗證的應用程式提供高可用性，您可以在多個 Vm 上安裝連接器。 重複上一節所列的相同步驟，在加入 Azure AD DS 受控網域的其他伺服器上安裝連接器。

1. 安裝成功之後，請回到 [Azure 入口網站]。

1. 選取 [企業應用程式]。

   ![請確定您參與的是正確的專案關係人](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. 選取 [ **+ 新增應用程式**]，然後選取 [**內部部署應用程式**]。 

1. 在 [**新增您自己的內部部署應用程式**] 上，設定下欄欄位：

   * **名稱**：輸入應用程式的名稱。
   * **內部 url**：輸入您在其上安裝連接器之 NDES 伺服器的內部 URL/FQDN。
   * **預先驗證**：選取 [**通過**]。 不可能使用任何形式的預先驗證。 憑證要求所使用的通訊協定（SCEP）不會提供這種選項。
   * 將提供的**外部 URL**複製到剪貼簿。

1. 選取 [ **+ 新增**] 以儲存您的應用程式。

1. 藉由將您在步驟10中複製的連結貼到瀏覽器中，測試您是否可以透過 Azure AD 應用程式 proxy 存取 NDES 伺服器。 您應該會看到預設的 IIS [歡迎使用] 頁面。

1. 作為最後的測試，將*mscep*路徑新增至您在上一個步驟中貼上的現有 URL：

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. 您應該會看到**HTTP 錯誤403–禁止**的回應。

1. 將提供的 NDES URL （透過 Microsoft Intune）變更為裝置，這項變更可能會在 Microsoft 端點設定中心或 Intune 雲端中。

   * 針對 [設定中心]，移至憑證登錄點（CRP）並調整 URL。 此 URL 是裝置向外呼叫，並呈現其挑戰的內容。
   * 僅適用于 Intune 雲端，也稱為 Intune 獨立版，請編輯或建立新的 SCEP 原則，並新增新的 URL。

## <a name="next-steps"></a>後續步驟

使用與 NDES 整合的 Azure AD 應用程式 Proxy，發佈應用程式供使用者存取。 如需詳細資訊，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。
