---
title: 將 PhoneFactor 升級至 Azure MFA 伺服器-Azure Active Directory
description: 當您從舊版的 PhoneFactor Agent 升級時，開始使用 Azure MFA Server。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808109e00decf5c4084be5be550b49e2e7d4628c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742352"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>將 PhoneFactor Agent 升級為 Azure Multi-Factor Authentication Server

若要將 PhoneFactor Agent v5.x 或更舊版本升級至 Azure Multi-factor Authentication Server，請先解除安裝 PhoneFactor Agent 和附屬元件。 然後即可安裝 Multi-Factor Authentication Server 與其附屬元件。

> [!IMPORTANT]
> 從2019年7月1日起，Microsoft 不再為新的部署提供 MFA Server。 想要在登入事件期間 (MFA) 要求多重要素驗證的新客戶應該使用雲端式 Azure AD Multi-Factor Authentication。
>
> 若要開始使用雲端式 MFA，請參閱 [教學課程：使用 Azure AD Multi-Factor Authentication 保護使用者登入事件](tutorial-enable-azure-mfa.md)。
>
> 在2019年7月1日前啟用 MFA Server 的現有客戶，可以下載最新版本、未來的更新，並照常產生啟用認證。

## <a name="uninstall-the-phonefactor-agent"></a>解除安裝 PhoneFactor Agent

1. 首先，備份 PhoneFactor 資料檔案。 預設安裝位置是 C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata。

2. 如果已安裝使用者入口網站：
   1. 瀏覽至安裝資料夾，然後備份 web.config 檔案。 預設安裝位置是 C:\inetpub\wwwroot\PhoneFactor。

   2. 如果您已在入口網站中加入自訂佈景主題，請備份 C:\inetpub\wwwroot\PhoneFactor\App_Themes 目錄下的自訂資料夾。

   3. 透過 PhoneFactor Agent 解除安裝使用者入口網站 (唯有安裝在與 PhoneFactor Agent 相同的伺服器時才適用) 或透過 Windows [程式和功能]。

3. 如果已安裝 Mobile App Web 服務：

   1. 移至安裝資料夾，然後備份 web.config 檔案。 預設安裝位置是 C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService。

   2. 透過 Windows [程式和功能] 解除安裝 Mobile App Web 服務。

4. 如果您已安裝 Web 服務 SDK，請透過 PhoneFactor Agent 或透過 Windows [程式和功能] 解除安裝。

5. 透過 Windows [程式和功能] 解除安裝 PhoneFactor Agent。

## <a name="install-the-multi-factor-authentication-server"></a>安裝 Multi-Factor Authentication Server

系統會從先前 PhoneFactor Agent 安裝的登錄接續安裝路徑，因此它應該會安裝在相同位置 (例如，C:\Program Files\PhoneFactor)。 新安裝將會有不同的預設安裝路徑 (例如，C:\Program Files\Multi-Factor Authentication Server)。 先前 PhoneFactor Agent 所留下的資料檔案應該會在安裝期間升級，因此在安裝新的 Multi-Factor Authentication Server 之後，使用者和設定應該會留在原地。

1. 如果出現提示，請啟用 Multi-Factor Authentication Server，並確認已將它指派給正確的複寫群組。

2. 如果您之前已安裝 Web 服務 SDK，請透過 Multi-Factor Authentication Server 使用者介面安裝新的 Web 服務 SDK。

   預設虛擬目錄名稱現在是 **MultiFactorAuthWebServiceSdk** ，而不是 **PhoneFactorWebServiceSdk**。 如果您想要使用先前的名稱，必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝使用新預設名稱，必須變更所有參考 Web 服務 SDK 之應用程式中的 URL (例如使用者入口網站和 Mobile App Web 服務)，使其指向正確位置。

3. 如果您之前將使用者入口網站安裝在 PhoneFactor Agent Server 上，請透過 Multi-Factor Authentication Server 使用者介面安裝新的 Multi-Factor Authentication 使用者入口網站。

   預設虛擬目錄名稱現在是 **MultiFactorAuth** ，而不是 **PhoneFactor**。 如果您想要使用先前的名稱，必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝使用新預設名稱，應該要在 Multi-Factor Authentication Server 中按一下 [使用者入口網站] 圖示，然後更新 [設定] 索引標籤中的使用者入口網站 URL。

4. 如果您之前將使用者入口網站和/或 Mobile App Web 服務安裝在 PhoneFactor Agent 之外的伺服器上：

   1. 移至安裝位置 (例如，C:\Program Files\PhoneFactor)，然後將一個或多個安裝程式複製到其他伺服器。 使用者入口網站和 Mobile App Web 服務備有 32 位元和 64 位元安裝程式。 它們稱為 MultiFactorAuthenticationUserPortalSetupXX.msi 和 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。

   2. 若要將使用者入口網站安裝在 Web 伺服器上，請以系統管理員身分開啟命令提示字元，然後執行 MultiFactorAuthenticationUserPortalSetupXX.msi。

      預設虛擬目錄名稱現在是 **MultiFactorAuth** ，而不是 **PhoneFactor**。 如果您想要使用先前的名稱，必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝使用新的預設名稱，您應該在 Multi-Factor Authentication Server 中按一下 [使用者入口網站] 圖示，並更新 [設定] 索引標籤上的使用者入口網站 URL。現有的使用者必須收到新 URL 的通知。

   3. 移至使用者入口網站安裝位置 (例如，C:\inetpub\wwwroot\MultiFactorAuth)，然後編輯 web.config 檔案。 在將 web.config 檔案升級為新 web.config 檔案之前備份的原始檔案中，複製 appSettings 和 applicationSettings 區段中的值。 在安裝 Web 服務 SDK 時，如果新的預設虛擬目錄名稱已保留，請變更 applicationSettings 區段中的 URL，以指向正確位置。 如果先前 web.config 檔案中有其他任何經過變更的預設值，請將這些相同變更套用到新的 web.config 檔案。

> [!NOTE]
> 從 Azure MFA Server 8.0 至 8.0+ 以前的版本升級時，可以在升級之後解除安裝行動裝置應用程式 Web 服務

## <a name="next-steps"></a>後續步驟

- 針對 Azure Multi-Factor Authentication Server [安裝使用者入口網站](howto-mfaserver-deploy-userportal.md)。

- 為您的應用程式[設定 Windows 驗證](howto-mfaserver-windows.md)。 
