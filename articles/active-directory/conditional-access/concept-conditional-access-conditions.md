---
title: 條件訪問策略中的條件 - Azure 活動目錄
description: Azure AD 條件訪問策略中的條件是什麼
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295340"
---
# <a name="conditional-access-conditions"></a>條件訪問：條件

在條件訪問策略中，管理員可以利用來自風險、設備平臺或位置等條件的信號來增強其策略決策。 

![定義條件訪問策略並指定條件](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

可以組合多個條件以創建細細微性和特定的條件訪問策略。

例如，在訪問敏感應用程式時，管理員除了考慮其他控制項（如多重要素驗證）外，還可以將身份保護和位置的登錄風險資訊納入其訪問決策中。

## <a name="sign-in-risk"></a>登入風險

對於有權訪問[身份保護](../identity-protection/overview-identity-protection.md)的客戶，登錄風險可以作為條件訪問策略的一部分進行評估。 登錄風險表示給定身份驗證請求未由標識擁有者授權的概率。 有關登錄風險的詳細資訊，請參閱文章"[什麼是風險](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)以及如何[：配置和啟用風險策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)"。

## <a name="device-platforms"></a>裝置平台

設備平臺的特點是在設備上運行的作業系統。 Azure AD 使用設備提供的資訊（如使用者代理字串）標識平臺。 由於可以修改使用者代理字串，因此此資訊未經驗證。 設備平臺應與 Microsoft Intune 設備合規性策略一起使用，或作為塊語句的一部分使用。 預設值是應用於所有設備平臺。

Azure AD 條件訪問支援以下設備平臺：

- Android
- iOS
- Windows Phone
- Windows
- macOS

如果使用**其他用戶端**條件阻止舊版身份驗證，也可以設置設備平臺條件。

## <a name="locations"></a>位置

將位置配置為條件時，組織可以選擇包括或排除位置。 這些命名位置可能包括公共 IPv4 網路資訊、國家或地區，甚至未映射到特定國家或地區的未知區域。 只有 IP 範圍可以標記為受信任的位置。

當包括**任何位置**時，此選項包括互聯網上的任何 IP 位址，而不僅僅是配置命名位置。 選擇**任何位置**時，管理員可以選擇排除**所有受信任的**位置或**選定位置**。

例如，當使用者連接到受信任位置（如其物理總部）的網路時，某些組織可能選擇不要求多重要素驗證。 管理員可以創建包含任何位置但排除其總部網路所選位置的策略。

有關位置的詳細資訊，請參閱["Azure 活動目錄條件訪問中的位置條件是什麼](location-condition.md)"

## <a name="client-apps-preview"></a>用戶端應用（預覽版）

預設情況下，條件訪問策略適用于使用現代身份驗證協定的基於瀏覽器的應用程式和應用程式。 除了這些應用程式之外，管理員還可以選擇包括 Exchange ActiveSync 用戶端和其他使用舊協定的用戶端。

- 瀏覽器
   - 其中包括使用 SAML、WS-聯合、OpenID Connect 或註冊為 OAuth 機密用戶端的服務等協定的基於 Web 的應用程式。
- 行動裝置應用程式和桌面用戶端
   - 現代身份驗證用戶端
      - 此選項包括 Office 桌面和電話應用程式等應用程式。
   - Exchange ActiveSync 用戶端
      - 預設情況下，這包括所有使用 Exchange ActiveSync （EAS） 協定。 **僅選擇"僅應用於受支援的平臺應用策略**"將限制為受支援的平臺（如 iOS、Android 和 Windows）。
      - 當策略阻止使用 Exchange ActiveSync 時，受影響的使用者將收到一封隔離電子郵件。 此電子郵件提供有關它們被阻止的原因的資訊，並包括修復說明（如果可以）。
   - 其他用戶端
      - 此選項包括使用不支援現代身份驗證的基本/遺留身份驗證協定的用戶端。
         - 經過身份驗證的 SMTP - POP 和 IMAP 用戶端用於發送電子郵件。
         - 自動探索 - Outlook 和 EAS 用戶端用於在"線上交換"中查找和連接到郵箱。
         - 交換線上電源外殼 - 用於與遠端 PowerShell 連接到線上交換。 如果阻止 Exchange 線上 PowerShell 的基本驗證，則需要使用 Exchange 線上 PowerShell 模組進行連接。 有關說明，請參閱[使用多重要素驗證連接到交換線上 PowerShell。](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
         - 交換 Web 服務 （EWS） - Outlook、Mac Outlook 和協力廠商應用使用的程式設計介面。
         - IMAP4 - IMAP 電子郵件用戶端使用。
         - 通過 HTTP（MAPI/HTTP） 的 MAPI - 由 Outlook 2010 及更高版本使用。
         - 離線通訊錄 （OAB） - Outlook 下載和使用的地址清單集合的副本。
         - 隨時隨地的 Outlook（HTTP 的 RPC） - 由 Outlook 2016 及更早版本使用。
         - Outlook 服務 - 由 Windows 10 的郵件和日曆應用使用。
         - POP3 - 由 POP 電子郵件用戶端使用。
         - 報告 Web 服務 - 用於在"連線交換"中檢索報表資料。

這些條件通常用於需要託管設備、阻止舊版身份驗證和阻止 Web 應用程式，但允許移動或桌面應用。

### <a name="supported-browsers"></a>支援的瀏覽器

此設定適用於所有瀏覽器。 不過，為了滿足像是符合裝置需求規範的裝置原則，因此支援下列作業系統和瀏覽器：

| OS | 瀏覽器 |
| :-- | :-- |
| Windows 10 | 微軟邊緣， 互聯網瀏覽器， 鉻 |
| Windows 8 / 8.1 | Internet Explorer、Chrome |
| Windows 7 | Internet Explorer、Chrome |
| iOS | 微軟邊緣， Intune託管瀏覽器， Safari |
| Android | 微軟邊緣， Intune託管瀏覽器， 鉻 |
| Windows Phone | 微軟邊緣，互聯網瀏覽器 |
| Windows Server 2019 | 微軟邊緣， 互聯網瀏覽器， 鉻 |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome、Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>為什麼我在瀏覽器中看到證書提示

在 Windows 7 上，iOS、Android 和 macOS Azure AD 使用在設備註冊 Azure AD 時預配的用戶端憑證標識設備。  當使用者首次通過瀏覽器登錄時，系統會提示使用者選擇證書。 在使用瀏覽器之前，使用者必須選擇此證書。

#### <a name="chrome-support"></a>Chrome 支援

有關 Windows **10 建立者更新（版本 1703）** 或更高版本的 Chrome 支援，請安裝[Windows 10 帳戶副檔名](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 當條件訪問策略需要特定于設備的詳細資訊時，需要此擴展。

若要自動將此擴充功能部署到 Chrome 瀏覽器，請建立下列登錄機碼：

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| 名稱 | 1 |
| 類型 | REG_SZ (字串) |
| 資料 | ppnbnpeolgkicgegkbjmlideopiji;HTTPs\://clients2.google.com/service/update2/crx |

如需 **Windows 8.1 和 7** 中的 Chrome 支援，請建立下列登錄機碼：

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| 名稱 | 1 |
| 類型 | REG_SZ (字串) |
| 資料 | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

這些瀏覽器支援裝置驗證，因此可以根據原則來識別和驗證裝置。 如果瀏覽器在私用模式中執行，裝置檢查將會失敗。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支援的行動裝置應用程式和桌面用戶端

組織可以選擇**移動應用和桌面用戶端**作為用戶端應用。

這項設定會影響從下列行動裝置應用程式和桌面用戶端進行的存取嘗試：

| 用戶端應用程式 | 目標服務 | Platform |
| --- | --- | --- |
| Dynamics CRM 應用程式 | Dynamics CRM | Windows 10、Windows 8.1、iOS 和 Android |
| [電子郵件]/[行事曆]/[人員] 應用程式、Outlook 2016、Outlook 2013 (使用新式驗證)| Office 365 Exchange Online | Windows 10 |
| 應用程式的 MFA 和位置原則。 不支援裝置型原則。| 任何 My Apps 應用程式服務 | Android 和 iOS |
| Microsoft Teams Services - 這會控制支援 Microsoft Teams 及其所有用戶端應用程式的所有服務 - Windows 桌面、iOS、Android、WP 和 Web 用戶端 | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android 及 macOS |
| Office 2016 應用，Office 2013（具有現代身份驗證[）、OneDrive 同步用戶端](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 應用、通用 Office 應用、Office 2013（具有現代身份驗證[）、OneDrive 同步用戶端](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (僅限 Word、Excel、PowerPoint、OneNote)。 | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10，macOS |
| Office 行動應用程式 | Office 365 SharePoint Online | Android、iOS |
| Office Yammer 應用程式 | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10，macOS |
| Outlook 2016 (macOS 版 Office) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證) | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook 行動應用程式 | Office 365 Exchange Online | Android、iOS |
| Power BI 應用程式 | Power BI 服務 | Windows 10、Windows 8.1、Windows 7、安卓和 iOS |
| 商務用 Skype | Office 365 Exchange Online| Android、iOS |
| Visual Studio Team Services 應用程式 | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS 和 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 用戶端

- 組織只能在將策略分配給使用者或組時選擇 Exchange ActiveSync 用戶端。 選擇**所有使用者**、**所有來賓和外部使用者**或**目錄角色**將導致所有使用者被阻止。
- 創建分配給 Exchange ActiveSync 用戶端的策略時 **，Office 365 線上交換**應該是分配給該策略的唯一雲應用程式。 
- 組織可以使用**設備平臺**條件將此策略的範圍縮小到特定平臺。

如果分配給策略的存取控制使用 **"需要批准的用戶端應用**"，則使用者將定向安裝和使用 Outlook 移動用戶端。 如果需要**多重要素驗證**，受影響的使用者將被阻止，因為基本驗證不支援多重要素驗證。

如需詳細資訊，請參閱下列文章：

- [使用條件訪問阻止舊版身份驗證](block-legacy-authentication.md)
- [要求具有條件訪問的已批准用戶端應用](app-based-conditional-access.md)

### <a name="other-clients"></a>其他用戶端

藉由選取 [其他用戶端]****，您可以指定會影響搭配使用基本驗證和郵件通訊協定 (如 IMAP、MAPI、POP、SMTP) 之應用程式的條件，這些條件也會影響不是使用新式驗證的舊版 Office 應用程式。

## <a name="device-state-preview"></a>設備狀態（預覽）

設備狀態條件可用於從組織的條件訪問策略中排除混合 Azure AD 聯接和/或標記為符合 Microsoft Intune 合規性策略的設備。

例如，訪問 Microsoft Azure*管理*雲應用*的所有使用者*，包括除**設備混合 Azure AD 聯接**的所有**設備狀態**，**以及標記為"已相容的設備"** 和 *"訪問控制項*"**塊**"。 
   - 此示例將創建一個策略，該策略只允許從混合 Azure AD 聯接的設備/或標記為合規的設備訪問 Microsoft Azure 管理。

## <a name="next-steps"></a>後續步驟

- [條件訪問：授予](concept-conditional-access-grant.md)

- [條件訪問通用策略](concept-conditional-access-policy-common.md)
