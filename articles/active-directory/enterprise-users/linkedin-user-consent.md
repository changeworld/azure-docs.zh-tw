---
title: LinkedIn 資料共用和同意-Azure Active Directory |Microsoft Docs
description: 說明 LinkedIn 整合如何在 Azure Active Directory 中透過 Microsoft 應用程式共用資料
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d90391dd5512b08ee6995a32bcd0b0f111b0f2b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375027"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn 帳戶連接資料共用和同意

您可以讓 Active Directory (Azure AD) 組織的使用者同意將其 Microsoft 公司或學校帳戶與其 LinkedIn 帳戶連線。 在使用者連線其帳戶之後，某些 Microsoft 應用程式和服務中會提供 LinkedIn 的資訊和醒目顯示。 使用者也可以預期本身使用 LinkedIn 的網路體驗會得到改善，也會獲得更多 Microsoft 的資訊。

若要查看 Microsoft 應用程式和服務中的 LinkedIn 資訊，使用者必須同意連接自己的 Microsoft 與 LinkedIn 帳戶。 使用者首次按一下來查看某人在 Outlook、OneDrive 或 SharePoint Online 的個人檔案卡片上顯示的 LinkedIn 資訊時，系統會提示使用者連接其帳戶。 LinkedIn 帳戶連線不會向您的使用者完全啟用，必須等到使用者同意接受此體驗並連接自己帳戶。

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>共用 LinkedIn 資訊的優點

可存取 Microsoft 應用程式和服務中的 LinkedIn 資訊，使用者就更容易與組織內外的同事、客戶和合作夥伴溝通互動，並建立起專業的人際關係。 新的使用者可以加快與同事建立聯繫的速度，更加深入了解同事，並輕鬆獲得更多資訊。 下列範例顯示了 LinkedIn 資訊在 Microsoft 應用程式中的個人檔案卡片上的顯示方式：

![在您的組織中啟用 LinkedIn 整合](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>啟用並宣佈 LinkedIn 整合

您必須是 Azure Active Directory 管理員，才能管理您組織的設定。 您可以啟用所有使用者的連線，也可以僅啟用特定一組使用者的設定。

1. 若要啟用或停用整合，請依照 [您 Azure AD 組織同意 LinkedIn 整合](linkedin-integration.md)的步驟進行。
2. 您在組織中宣佈整合 LinkedIn 時，請告知使用者參閱 [Microsoft 應用程式和服務中的 LinkedIn 資訊](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381) (英文) 的常見問題集。 本文提供 LinkedIn 資訊的顯示位置、 [資料共用和隱私權](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400)、 [如何連接帳戶](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) 等等的資訊。

您必須向您的使用者宣告 Linkedin 整合，提供他們所有與資料共用相關的資訊 [，以及與 Linkedin 整合](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400)相關的隱私權。 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>使用者同意 Microsoft 與 LinkedIn 的資料存取

從 LinkedIn 存取的資料不會永久儲存於 Microsoft 服務中。 從 Microsoft 存取的資料不會與 LinkedIn 一起永久儲存，但連絡人除外。 Microsoft 連絡人會儲存在 LinkedIn 上，直到使用者移除為止，如[管理匯入的聯絡人](https://www.linkedin.com/help/linkedin/answer/43377)一文所述。

使用者連接其帳戶時，某些 Microsoft 應用程式會提供 LinkedIn 的資訊和見解，例如個人檔案卡片。 使用者也可以預期本身使用 LinkedIn 的網路體驗會得到改善，也會獲得更多 Microsoft 的資訊。
當貴組織中的使用者連線其 LinkedIn 和 Microsoft 公司或學校帳戶時，有兩個選項可用：

* 授予從這兩個帳戶存取資料的權限。 這表示他們授予 Microsoft 帳戶或公司帳戶權限，因此可存取其 LinkedIn 帳戶的資料，而且[其 LinkedIn 帳戶可存取其 Microsoft 公司或學校帳戶的資料](https://www.linkedin.com/help/linkedin/answer/84077) (英文)。
* 授予僅允許其 Microsoft 工作和學校帳戶存取 LinkedIn 資料的權限。

使用者可隨時中斷帳戶的連線並移除資料存取權限，而且[使用者可以控制自有 LinkedIn 設定檔的檢視方式](https://www.linkedin.com/help/linkedin/answer/83) (英文)，包括是否可以在 Microsoft 應用程式中檢視其設定檔。

### <a name="linkedin-account-data"></a>LinkedIn 帳戶資料

您連接您的 Microsoft 和 LinkedIn 帳戶時，表示您允許 LinkedIn 向 Microsoft 提供下列資料：

* 設定檔資料，包括 LinkedIn 的身分識別、連絡資訊，以及您在 [LinkedIn 設定檔](https://www.linkedin.com/help/linkedin/answer/15493) (英文) 對其他人分享的資訊。
* 興趣資料，包含在 LinkedIn 上的興趣，例如您關注的人員和主題、課程群組，以及您喜歡並分享的內容。
* 訂用帳戶資料，包含 LinkedIn 應用程式和服務以及相關聯資料的訂閱。 
* 連線資料，包含您的[linkedin 人脈網路](https://www.linkedin.com/help/linkedin/answer/110) (英文)，包括個人檔案以及您的第 1 度連線連絡資訊。

從 LinkedIn 存取的資料不會永久儲存於 Microsoft 服務中。 如需 Microsoft 使用個人資料的詳細資訊，請參閱 [Microsoft 隱私權聲明](https://privacy.microsoft.com/privacystatement/)。

### <a name="microsoft-work-or-school-account-data"></a>Microsoft 工作或學校帳戶資料

您連接您的 Microsoft 和 LinkedIn 帳戶時，表示您允許 Microsoft 向 LinkedIn 提供下列資料：

* 個人檔案資料，包括您的名字、姓氏、個人檔案相片、電子郵件地址、管理員以及您管理的人員等等的資訊。
* 行事曆資料，包過您的行事曆中的會議、會議的時間、地點和出席者的連絡資訊。 行事曆資料中未包含的會議資訊，例如議程、內容或會議主題。
* 興趣資料，包含根據您使用 Microsoft 服務的情況而與您的帳戶有關的興趣，例如 Cortana 和 Bing for Business。
* 訂用帳戶資料-包括您的組織提供給 Microsoft 應用程式和服務（例如 Microsoft 365）的訂閱。
* 連絡人資料，包括 Outlook、Skype 和其他 Microsoft 帳戶服務中的連絡人清單，包括您經常通訊或共事的人本身的連絡資訊。 連絡人會由 LinkedIn 定期匯入、儲存和使用，以便建議連接、協助組織連絡人，並顯示連絡人相關的更新等等。

從 Microsoft 存取的資料不會與 LinkedIn 一起永久儲存，但連絡人除外。 Microsoft 連絡人會儲存在 LinkedIn 上，直到使用者移除為止。 深入了解[從 LinkedIn 刪除匯入的連絡人](https://www.linkedin.com/help/linkedin/answer/43377) (英文)。

如需 LinkedIn 使用個人資料的詳細資訊，請參閱 [LinkedIn 隱私權原則](https://www.linkedin.com/legal/privacy-policy) (英文)。 針對 LinkedIn 服務、資料傳輸和儲存，資料可以從歐盟流向美國和上一頁流動，而您的隱私權會受到保護，如 [歐洲聯集資料傳輸](https://www.linkedin.com/help/linkedin/answer/62533)中所述。

## <a name="next-steps"></a>後續步驟

* [Microsoft 應用程式中與您的公司或學校帳戶關聯的 LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077) (英文)
