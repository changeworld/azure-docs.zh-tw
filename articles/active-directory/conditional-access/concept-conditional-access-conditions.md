---
title: 條件式存取原則中的條件-Azure Active Directory
description: Azure AD 條件式存取原則中的條件為何
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: dff80d849268c770e4227ff8c99b8f4d133c4d78
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620721"
---
# <a name="conditional-access-conditions"></a>條件式存取：條件

在條件式存取原則中，系統管理員可以利用來自風險、裝置平臺或位置等條件的信號來增強其原則決策。 

![定義條件式存取原則並指定條件](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

您可以結合多個條件來建立更細緻和特定的條件式存取原則。

例如，當存取敏感性應用程式時，系統管理員可能會將登入風險資訊從身分識別保護和位置納入其存取決策中，除了其他控制項（例如多重要素驗證）。

## <a name="sign-in-risk"></a>登入風險

對於具有身分[識別保護](../identity-protection/overview-identity-protection.md)存取權的客戶，可以將登入風險評估為條件式存取原則的一部分。 登入風險表示指定的驗證要求未被身分識別擁有者授權的機率。 如需登入風險的詳細資訊，請參閱文章、[風險](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)和[如何：設定及啟用風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md)。

## <a name="device-platforms"></a>裝置平台

裝置平臺是以裝置上執行的作業系統為特徵。 Azure AD 使用裝置提供的資訊（例如使用者代理程式字串）來識別平臺。 因為可以修改使用者代理字串，所以這是未經驗證的資訊。 裝置平臺應與 Microsoft Intune 裝置合規性原則搭配使用，或作為 block 語句的一部分。 預設為套用至所有裝置平臺。

Azure AD 條件式存取支援下列裝置平臺：

- Android
- iOS
- Windows Phone
- Windows
- macOS

如果您使用 [**其他用戶端**] 條件封鎖舊版驗證，則也可以設定裝置平臺條件。

## <a name="locations"></a>位置

將 [位置] 設定為 [條件] 時，組織可以選擇要包含或排除位置。 這些命名位置可能包括公用 IPv4 網路資訊、國家或地區，或甚至是未對應到特定國家或地區的未知區域。 只有 IP 範圍可以標示為信任的位置。

當包含**任何位置**時，此選項會包含網際網路上的任何 IP 位址，而不只是設定的命名位置。 當您選取**任何位置**時，系統管理員可以選擇排除**所有信任**或**選取的位置**。

例如，某些組織可能會選擇在其使用者連線到受信任位置（例如其實體總部）中的網路時，不需要多重要素驗證。 系統管理員可以建立包含任何位置的原則，但不包括其總部網路的所選位置。

如需位置的詳細資訊，請參閱[Azure Active Directory 條件式存取中的位置條件](location-condition.md)。

## <a name="client-apps-preview"></a>用戶端應用程式（預覽）

條件式存取原則預設適用于使用新式驗證通訊協定的瀏覽器型應用程式和應用程式。 除了這些應用程式之外，系統管理員還可以選擇包含 Exchange ActiveSync 用戶端，以及其他使用舊版通訊協定的用戶端。

- 瀏覽器
   - 其中包括使用 SAML、WS-同盟、OpenID Connect 等通訊協定的 web 應用程式，或註冊為 OAuth 機密用戶端的服務。
- 行動裝置應用程式和桌面用戶端
   - 新式驗證用戶端
      - 此選項包含 Office desktop 和 phone 應用程式等應用程式。
   - Exchange ActiveSync 用戶端
      - 根據預設，這包括所有 Exchange ActiveSync （EAS）通訊協定的使用。 選擇 [**僅將原則套用至支援的平臺**] 將會限制為支援的平臺，例如 IOS、Android 和 Windows。
      - 當原則封鎖 Exchange ActiveSync 的使用時，受影響的使用者將會收到單一的隔離電子郵件。 這封電子郵件會提供封鎖原因的相關資訊，並包含修復指示（如果可以的話）。
   - 其他用戶端
      - 此選項包括使用不支援新式驗證之基本/舊版驗證通訊協定的用戶端。
         - 已驗證的 SMTP-POP 和 IMAP 用戶端用來傳送電子郵件訊息。
         - 自動探索-供 Outlook 和 EAS 用戶端用來尋找並聯機到 Exchange Online 中的信箱。
         - Exchange Online PowerShell-用來透過遠端 PowerShell 連線到 Exchange Online。 如果您封鎖 Exchange Online PowerShell 的基本驗證，您需要使用 Exchange Online PowerShell 模組來連接。 如需指示，請參閱[使用多重要素驗證連接到 Exchange Online PowerShell](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
         - Exchange Web 服務（EWS）-Outlook、Outlook for Mac 及協力廠商應用程式所使用的程式設計介面。
         - IMAP4-供 IMAP 電子郵件客戶程式使用。
         - MAPI over HTTP （MAPI/HTTP）-由 Outlook 2010 和更新版本使用。
         - 離線通訊錄（OAB）-Outlook 下載並使用的地址清單集合複本。
         - Outlook Anywhere （RPC over HTTP）-由 Outlook 2016 和更早版本使用。
         - Outlook 服務-適用于 Windows 10 的電子郵件和行事曆應用程式。
         - POP3-由 POP 電子郵件客戶程式使用。
         - 報表 Web 服務-用來在 Exchange Online 中取出報表資料。

這些條件通常會在需要受控裝置、封鎖舊版驗證，以及封鎖 web 應用程式，但允許行動或桌面應用程式時使用。

### <a name="supported-browsers"></a>支援的瀏覽器

此設定適用於所有瀏覽器。 不過，為了滿足像是符合裝置需求規範的裝置原則，因此支援下列作業系統和瀏覽器：

| OS | 瀏覽器 |
| :-- | :-- |
| Windows 10 | Microsoft Edge、Internet Explorer、Chrome |
| Windows 8 / 8.1 | Internet Explorer、Chrome |
| Windows 7 | Internet Explorer、Chrome |
| iOS | Microsoft Edge、Intune Managed Browser、Safari |
| Android | Microsoft Edge、Intune Managed Browser、Chrome |
| Windows Phone | Microsoft Edge、Internet Explorer |
| Windows Server 2019 | Microsoft Edge、Internet Explorer、Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome、Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>為什麼我會在瀏覽器中看到憑證提示

在 Windows 7、iOS、Android 和 macOS Azure AD 使用向 Azure AD 註冊裝置時所布建的用戶端憑證來識別裝置。  當使用者第一次透過瀏覽器登入時，系統會提示使用者選取憑證。 使用者必須先選取此憑證，才能使用瀏覽器。

#### <a name="chrome-support"></a>Chrome 支援

如需**windows 10 建立者更新（版本1703）** 或更新版本中的 Chrome 支援，請安裝[windows 10 帳戶延伸](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)模組。 當條件式存取原則需要裝置特定的詳細資料時，需要此延伸模組。

若要自動將此擴充功能部署到 Chrome 瀏覽器，請建立下列登錄機碼：

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| 名稱 | 1 |
| 類型 | REG_SZ (字串) |
| 資料 | ppnbnpeolgkicgegkbkbjmhlideopiji; HTTPs\://clients2.google.com/service/update2/crx |

如需 **Windows 8.1 和 7** 中的 Chrome 支援，請建立下列登錄機碼：

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| 名稱 | 1 |
| 類型 | REG_SZ (字串) |
| 資料 | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

這些瀏覽器支援裝置驗證，因此可以根據原則來識別和驗證裝置。 如果瀏覽器在私用模式中執行，裝置檢查將會失敗。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支援的行動裝置應用程式和桌面用戶端

組織可以選取 [行動**應用程式] 和 [桌面用戶端**] 作為用戶端應用程式。

這項設定會影響從下列行動裝置應用程式和桌面用戶端進行的存取嘗試：

| 用戶端應用程式 | 目標服務 | 平台 |
| --- | --- | --- |
| Dynamics CRM 應用程式 | Dynamics CRM | Windows 10、Windows 8.1、iOS 和 Android |
| [電子郵件]/[行事曆]/[人員] 應用程式、Outlook 2016、Outlook 2013 (使用新式驗證)| Office 365 Exchange Online | Windows 10 |
| 應用程式的 MFA 和位置原則。 不支援裝置型原則。| 任何 My Apps 應用程式服務 | Android 和 iOS |
| Microsoft Teams Services - 這會控制支援 Microsoft Teams 及其所有用戶端應用程式的所有服務 - Windows 桌面、iOS、Android、WP 和 Web 用戶端 | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android 及 macOS |
| Office 2016 應用程式、Office 2013 （具備新式驗證）、 [OneDrive 同步處理用戶端](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 應用程式、通用 Office 應用程式、Office 2013 （具備新式驗證）、 [OneDrive 同步處理用戶端](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (僅限 Word、Excel、PowerPoint、OneNote)。 | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10，macOS |
| Office 行動應用程式 | Office 365 SharePoint Online | Android、iOS |
| Office Yammer 應用程式 | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10，macOS |
| Outlook 2016 (macOS 版 Office) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證) | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook 行動應用程式 | Office 365 Exchange Online | Android、iOS |
| Power BI 應用程式 | Power BI 服務 | Windows 10、Windows 8.1、Windows 7、Android 和 iOS |
| 商務用 Skype | Office 365 Exchange Online| Android、iOS |
| Visual Studio Team Services 應用程式 | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS 和 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 用戶端

- 將原則指派給使用者或群組時，組織只能選取 Exchange ActiveSync 用戶端。 選取 [**所有使用者**]、[**所有來賓] 和 [外部使用者**] 或 [**目錄角色**]，會使所有使用者遭到封鎖。
- 建立指派給 Exchange ActiveSync 用戶端的原則時， **Office 365 Exchange Online**應該是唯一指派給該原則的雲端應用程式。 
- 組織可以使用**裝置平臺**條件，將此原則的範圍縮小至特定平臺。

如果指派給原則的存取控制使用 [**需要核准的用戶端應用程式**]，則會將使用者導向安裝和使用 Outlook mobile client。 如果需要**多重要素驗證**，受影響的使用者會遭到封鎖，因為基本驗證不支援多重要素驗證。

如需詳細資訊，請參閱下列文章：

- [使用條件式存取封鎖舊版驗證](block-legacy-authentication.md)
- [要求經核准的用戶端應用程式使用條件式存取](app-based-conditional-access.md)

### <a name="other-clients"></a>其他用戶端

藉由選取 [其他用戶端]，您可以指定會影響搭配使用基本驗證和郵件通訊協定 (如 IMAP、MAPI、POP、SMTP) 之應用程式的條件，這些條件也會影響不是使用新式驗證的舊版 Office 應用程式。

## <a name="device-state-preview"></a>裝置狀態（預覽）

[裝置狀態] 條件可以用來排除已加入混合式 Azure AD 的裝置，以及（或）從組織的條件式存取原則中標示為符合 Microsoft Intune 合規性政策的裝置。

例如，所有存取「 *Microsoft Azure 管理*」雲端應用程式的*使用者*，包括裝置**混合式 Azure AD 聯結**和**裝置標示為符合規範**，以及適用于*存取控制*、**封鎖**的**所有裝置狀態**。 
   - 這個範例會建立一個原則，只允許從已加入混合式 Azure AD 的裝置和/或已標記為符合規範的裝置存取 Microsoft Azure 管理。

## <a name="next-steps"></a>後續步驟

- [條件式存取：授與](concept-conditional-access-grant.md)

- [條件式存取的一般原則](concept-conditional-access-policy-common.md)
