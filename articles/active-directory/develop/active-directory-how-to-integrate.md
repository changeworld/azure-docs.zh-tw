---
title: 如何與 Microsoft 身分識別平臺整合 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解將您的應用程式與 Microsoft 身分識別平臺整合的優點，並取得簡化登入、身分識別管理、多重要素驗證和存取控制等功能的資源。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7ee283ff61753a060e49a3340cd0a795b04faf9
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755927"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>與 Microsoft 身分識別平臺整合

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

在本文中，您將瞭解將應用程式與 Microsoft 身分識別平臺整合的優點，並取得整合的資源。 Microsoft 身分識別平臺和 Azure Active Directory (AD) 為組織提供適用于雲端應用程式的企業級身分識別管理。 Microsoft 身分識別平臺整合可讓您的使用者有簡化的登入體驗，並協助您的應用程式符合 IT 原則。

## <a name="how-to-integrate"></a>如何整合

有幾種方式可讓您的應用程式與 Microsoft 身分識別平臺整合。 充分利用適合您應用程式使用的這些案例。

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>支援 Microsoft 身分識別平臺，以登入您的應用程式

**減少登入的不便，並降低支援成本。** 藉由使用 Microsoft 身分識別平臺來登入您的應用程式，您的使用者將不會有另一個名稱和密碼需要記住。 身為開發人員，您會少一個要儲存及保護的密碼。 單單無需處理忘記密碼重設就可以省下大量時間與金錢。 Microsoft 身分識別平臺可讓某些全球最受歡迎的雲端應用程式（包括 Microsoft 365 和 Microsoft Azure）登入。 從數百萬個組織中有數十萬個使用者，您的使用者可能已經登入 Microsoft 身分識別平臺。 深入瞭解如何 [新增 Microsoft 身分識別平臺登入的支援](./authentication-vs-authorization.md)。

**簡化應用程式的註冊程序。**  註冊您的應用程式時，Microsoft 身分識別平臺可以傳送有關使用者的基本資訊，讓您可以預先填入註冊表單或完全排除。 使用者可以透過類似社交媒體和行動應用程式所使用的常見同意程序，使用其 Azure AD 帳戶來註冊應用程式。 任何使用者都可以註冊並登入與 Microsoft 身分識別平臺整合的應用程式，而不需要 IT 介入。 深入了解[註冊應用程式以進行 Azure AD 帳戶登入](../../app-service/configure-authentication-provider-aad.md)。

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>流覽使用者、管理使用者布建，以及控制應用程式的存取權

**瀏覽以尋找目錄中的使用者。**  使用 Microsoft Graph API 來協助使用者在邀請其他人或授與存取權時，搜尋並流覽其組織中的其他人員，而不需要使用者輸入電子郵件地址。 使用者可以使用常見的通訊錄樣式介面進行瀏覽，包括檢視組織階層的詳細資料。 深入瞭解 [MICROSOFT GRAPH API](/graph/overview)。

**重複使用您的客戶已經在管理的 Active Directory 群組與通訊群組清單。**   Azure AD 包含您的客戶已經用於電子郵件通訊群組和管理存取的群組。 使用 Microsoft Graph API 時，請重複使用這些群組，而不需要您的客戶在應用程式中建立和管理一組個別的群組。 群組資訊也可以透過登入權杖傳送至您的應用程式。 深入瞭解 [MICROSOFT GRAPH API](/graph/overview)。

**使用 Microsoft 身分識別平臺來控制誰可以存取您的應用程式。**   系統管理員和 Azure AD 中的應用程式擁有者可以將應用程式的存取權限指派給特定使用者和群組。 使用 Microsoft Graph API，您可以讀取這份清單，並用它來控制資源的布建和解除布建，以及應用程式內的存取。

**使用 Microsoft 身分識別平臺進行以角色為基礎的存取控制。**  系統管理員和應用程式擁有者可以將使用者和群組指派給當您在 Microsoft 身分識別平臺中註冊應用程式時定義的角色。 角色資訊會透過登入權杖傳送至您的應用程式，也可以使用 Microsoft Graph API 來讀取。 深入瞭解如何 [使用 Microsoft 身分識別平臺進行授權](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)。

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>取得使用者設定檔、行事曆、電子郵件、連絡人、檔案等的存取權

**Microsoft 身分識別平臺是 Microsoft 365 和其他 Microsoft 商務服務的授權伺服器。**  如果您支援 Microsoft 身分識別平臺來登入您的應用程式，或支援將目前的使用者帳戶連結至使用 OAuth 2.0 Azure AD 使用者帳戶，您可以要求使用者設定檔、行事曆、電子郵件、連絡人、檔案及其他資訊的讀取和寫入存取權。 您可以順利地將事件寫入使用者的行事曆，並讀取檔案或將檔案寫入使用者的 OneDrive。 深入瞭解 [Microsoft 365 api](/graph/overview)。

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>在 Azure 和 Microsoft 365 marketplace 中推廣您的應用程式

**將您的應用程式擴廣到數百萬個已使用 Azure AD 的組織。**   搜尋和瀏覽 Marketplace 的使用者已經使用一或多項雲端服務，讓他們成為合格的雲端服務客戶。 深入了解如何在 [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)中推廣您的應用程式。

**當使用者註冊您的應用程式時，它會出現在其 Azure AD 存取面板和 Microsoft 365 應用程式啟動器中。**   使用者將能夠迅速且輕鬆地返回您的應用程式，以增加使用者參與度。 深入了解 [Azure AD 存取面板](../user-help/my-apps-portal-end-user-access.md)。

### <a name="secure-device-to-service-and-service-to-service-communication"></a>保護裝置對服務和服務對服務的通訊

**使用 Microsoft 身分識別平臺進行服務和裝置的身分識別管理，可減少您需要撰寫的程式碼，並讓它能夠管理存取權。**  服務和裝置可以使用 OAuth 從 Microsoft 身分識別平臺取得權杖，並使用這些權杖來存取 web Api。 使用 Microsoft 身分識別平臺，您可以避免撰寫複雜的驗證程式代碼。 因為服務和裝置的身分識別會儲存在 Azure AD 中，IT 可以在同一個地方管理金鑰和撤銷，而無需在您的應用程式中分別執行此動作。

## <a name="benefits-of-integration"></a>整合的優點

與 Microsoft 身分識別平臺整合的優點是，您不需要撰寫額外的程式碼。

### <a name="integration-with-enterprise-identity-management"></a>與企業身分識別管理整合

**協助您的應用程式符合 IT 原則。**  組織會將其企業身分識別管理系統與 Microsoft 身分識別平臺整合，如此一來，當某人離開組織時，他們就會自動失去對您應用程式的存取權，而不需要採取額外的步驟。 IT 可以管理誰可以存取您的應用程式，並判斷需要哪些存取原則 (例如，多因素驗證)，以降低撰寫程式碼以符合複雜公司原則的需求。 Azure AD 提供系統管理員詳盡的稽核記錄，內容包括登入應用程式的使用者，以便 IT 可以追蹤使用情況。

**Azure AD 將 Active Directory 延伸到雲端，讓您的應用程式可以與 AD 整合。**   世界各地許多組織使用 Active Directory 作為其主要登入和身分識別管理系統，並要求他們的應用程式使用 AD。 與 Azure AD 整合即可將 Active Directory 和您的應用程式整合。

### <a name="advanced-security-features"></a>Advanced security 功能

**多重要素驗證。**  Microsoft 身分識別平臺提供原生多重要素驗證。 IT 系統管理員可以要求多因素驗證才能存取應用程式，因此您無需自行撰寫這項支援的程式碼。 深入瞭解 [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)。

**登入偵測異常。**  Microsoft 身分識別平臺一天處理超過10億個登入，同時使用機器學習演算法來偵測可疑活動，並通知 IT 系統管理員可能發生的問題。 藉由支援 Microsoft 身分識別平臺登入，您的應用程式就能獲得這項保護的優點。 深入了解[檢視 Azure Active Directory 存取報告](../reports-monitoring/overview-reports.md)。

**條件式存取。**   除了多因素驗證，系統管理員還可以要求使用者在登入您的應用程式之前必須符合特定條件。 可以設定的條件包括用戶端裝置的 IP 位址範圍、指定群組的成員資格，以及用於存取的裝置狀態。 深入瞭解 [Azure Active Directory 條件式存取](../conditional-access/overview.md)。

### <a name="easy-development"></a>簡易開發

**業界標準通訊協定。**   Microsoft 致力於支援業界標準。 Microsoft 身分識別平臺支援業界標準 OAuth 2.0 和 OpenID Connect 1.0 通訊協定。 深入瞭解 Microsoft 身分 [識別平臺驗證通訊協定](active-directory-v2-protocols.md)。

**開放原始碼程式庫。**   Microsoft 針對常用的語言和平台提供完整支援的開放原始碼程式庫，進而加快開發速度。 原始碼採用 Apache 2.0 授權，您可以任何分岔以及貢獻回饋給專案。 深入瞭解 [Microsoft 驗證程式庫 (MSAL) ](reference-v2-libraries.md)。

### <a name="worldwide-presence-and-high-availability"></a>全球存在及高可用性

**Azure AD 會在世界各地的資料中心內進行部署，並受到全天候的管理和監控。**  Azure AD 是 Microsoft Azure 和 Microsoft 365 的身分識別管理系統，並部署在全球28個資料中心。 保證將目錄資料複寫到至少三個資料中心。 全域負載平衡器會確保使用者可存取包含其資料的最接近 Azure AD 複本，並在偵測到問題時，自動將要求重新路由到其他資料中心。

## <a name="next-steps"></a>後續步驟

[開始撰寫程式碼](v2-overview.md#getting-started)

[使用 Microsoft 身分識別平臺來登入使用者](./authentication-vs-authorization.md)