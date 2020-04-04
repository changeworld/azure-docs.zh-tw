---
title: IIS 認證與 Azure MFA 伺服器 - Azure 活動目錄
description: 部署 IIS 驗證與 Azure Multi-Factor Authentication Server。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6189e2bc6c3c8f28b767902b525b03cb72968bc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652904"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>針對 IIS Web 應用程式設定 Azure Multi-Factor Authentication Server

使用 Azure Multi-Factor Authentication (MFA) Server 的 [IIS 驗證] 區段來啟用及設定 IIS 驗證，以便與 Microsoft IIS Web 應用程式整合。 Azure MFA Server 會安裝一個外掛程式，可以篩選對 IIS Web 伺服器提出的要求，以新增 Azure Multi-Factor Authentication。 IIS 外掛程式支援表單架構驗證和整合式 Windows HTTP 驗證。 信任的 IP 也可以設定為將內部 IP 位址免除雙因素驗證。

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起,Microsoft 將不再為新部署提供 MFA 伺服器。 希望用戶進行多重身份驗證的新客戶應使用基於雲的 Azure 多重身份驗證。 在 7 月 1 日之前啟動 MFA 伺服器的現有客戶將能夠像往常一樣下載最新版本、將來的更新並生成啟動認證。

![MFA 伺服器中的 IIS 認證](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>搭配 Azure Multi-Factor Authentication Server 使用表單架構 IIS 驗證

為了保護使用表單架構驗證的 IIS Web 應用程式，請在 IIS Web 伺服器上安裝 Azure Multi-Factor Authentication Server，並依照下列程序設定伺服器：

1. 在 Azure Multi-Factor Authentication Server 中，按一下左功能表中的 [IIS 驗證] 圖示。
2. 按一下 [表單架構]**** 索引標籤。
3. 按一下 **[新增]**。
4. 要自動檢測使用者名、密碼和域變數,請在「自動設定基於表單的網站`https://localhost/contoso/auth/login.aspx`」對話框中輸入登錄 URL(如 ),然後單擊「**確定**」。
5. 如果所有使用者都已經或將要匯入到伺服器中，且必須接受多重要素驗證，請選取 [需要進行 Multi-Factor Authentication 使用者比對]**** 方塊。 如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。
6. 如果無法自動偵測頁面變數，請按一下 [自動設定表單架構網站] 對話方塊中的 [手動指定]****。
7. 在 [新增表單架構網站] 對話方塊中，於 [提交 URL] 欄位中輸入登入頁面的 URL，然後輸入 [應用程式名稱] \(選擇性)。 應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。
8. 選取正確的 [要求格式]。 對於大多數 Web 應用程式,這設置為**POST 或 GET。**
9. 輸入 [使用者名稱變數]、[密碼變數] 和 [網域變數] \(如果出現在登入頁面上)。 若要尋找輸入方塊的名稱，請在網頁瀏覽器中瀏覽至登入頁面，在該頁面上按一下滑鼠右鍵，然後選取 [檢視原始檔]****。
10. 如果所有使用者都已或即將導入伺服器並接受多重身份驗證,則選中 **「需要 Azure 多重身份驗證」使用者匹配**框。 如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。
11. 按一下 [進階]**** 按鈕以檢閱進階設定，包括：

    - 選取自訂拒絕頁面檔案
    - 使用 Cookie 快取一段時間對網站進行的成功驗證
    - 選取驗證主要認證時是要對照 Windows 網域、LDAP 目錄 還是 RADIUS 伺服器來進行驗證。

12. 按一下 [確定]**** 以返回 [新增表單架構網站] 對話方塊。
13. 按一下 [確定]  。
14. 偵測到或已輸入 URL 和頁面變數後，網站資料就會顯示在表單架構面板中。

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>搭配 Azure Multi-Factor Authentication Server 使用整合式 Windows 驗證

為了保護使用整合式 Windows HTTP 驗證的 IIS Web 應用程式，請在 IIS Web 伺服器上安裝 Azure MFA Server，並使用下列步驟來設定伺服器：

1. 在 Azure Multi-Factor Authentication Server 中，按一下左功能表中的 [IIS 驗證] 圖示。
2. 按一下 [HTTP]**** 索引標籤。
3. 按一下 **[新增]**。
4. 在"添加基本 URL"對話框中,輸入執行 HTTP 身份驗證的網站<http://localhost/owa>的 URL( 如 ),並提供應用程式名稱(可選)。 應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。
5. 調整 [閒置逾時] 和 [最長工作階段時間] \(如果預設值不足夠)。
6. 如果所有使用者都已經或將要匯入到伺服器中，且必須接受多重要素驗證，請選取 [需要進行 Multi-Factor Authentication 使用者比對]**** 方塊。 如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。
7. 如果需要,選中**Cookie 緩存**框。
8. 按一下 [確定]  。

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>啟用 Azure Multi-Factor Authentication Server 的 IIS 外掛程式

設定表單架構驗證或 HTTP 驗證 URL 及設定之後，請在 IIS 中選取應該載入和啟用 Azure Multi-Factor Authentication IIS 外掛程式的位置。 請使用下列程序：

1. 如果在 IIS 6 上運行,請按一下**ISAPI**選項卡。選擇 Web 應用程式執行下的網站(例如預設網站),以啟用該網站的 Azure 多重身份驗證 ISAPI 篩選器外掛程式。
2. 如果在IIS 7或更高版本上運行,請單擊**本機模組**選項卡。選擇伺服器、網站或應用程式以在所需級別啟用IIS外掛程式。
3. 按一下畫面頂端的 [啟用 IIS 驗證]**** 方塊。 Azure Multi-Factor Authentication 現在會保護選取的 IIS 應用程式。 確定使用者已匯入到「伺服器」。

## <a name="trusted-ips"></a>信任的 IP

信任的 IP 可讓使用者對源自特定 IP 位址或子網路的網站要求略過 Azure Multi-Factor Authentication。 例如，您可以讓使用者從辦公室登入時免除 Azure Multi-Factor Authentication。 為此，您可以將辦公室子網路指定為信任的 IP 項目。 若要設定「可信任 IP」，請使用下列程序：

1. 在 [IIS 驗證] 區段中，按一下 [信任的 IP]**** 索引標籤。
2. 按一下 **[新增]**。
3. 當"添加受信任的 IP"對話框出現時,選擇 **"單個 IP、IP****範圍**"或 **"子網**"單選按鈕。
4. 輸入應允許的 IP 位址、IP 位址範圍或子網。 如果要輸入子網路，請選取適當的「網路遮罩」，然後按一下 [確定]****。
