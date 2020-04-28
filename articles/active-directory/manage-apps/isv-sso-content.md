---
title: 為多租用戶應用程式啟用 SSO
description: 與 Azure active Directory 整合的獨立軟體廠商指引
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67795172"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>為您的多租使用者應用程式啟用單一登入  

當您提供應用程式供其他公司透過購買或訂用帳戶使用時，您可以將應用程式提供給他們自己的 Azure 租使用者中的客戶。 這就是所謂的建立多租使用者應用程式。 如需此概念的總覽，請參閱 Azure 中的多租使用者[應用程式](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)和[Azure Active Directory 中](../develop/single-and-multi-tenant-apps.md)的租用。

## <a name="what-is-single-sign-on"></a>什麼是單一登入

當使用者使用 Azure Active Directory 和其他身分識別登入應用程式時，單一登入（SSO）可增加安全性和便利性。 當應用程式啟用 SSO 時，使用者不需要輸入個別的認證來存取該應用程式。 以取得單一登入的完整說明。 [請參閱 Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)。

## <a name="why-enable-single-sign-on-in-your-application"></a>為什麼要在您的應用程式中啟用單一登入？

在多租使用者應用程式中啟用 SSO 有許多優點。 當您為應用程式啟用 SSO 時：

* 您的應用程式可以列在 Azure Marketplace 中，使用 Azure Active Directory 的數百萬個組織可探索您的應用程式。
  * 可讓客戶使用 Azure AD 來快速設定應用程式。

* 您的應用程式可在 Office 365 應用程式資源庫、Office 365 應用程式啟動器中，以及 Office.com 上的 Microsoft 搜尋中找到

* 您的應用程式可以使用 Microsoft Graph REST API 來存取資料，以驅動 Microsoft Graph 提供的使用者生產力。

* 您可以讓客戶更輕鬆地降低支援成本。
  * 與 Azure AD 小組 coproduced 的應用程式特定檔，可讓我們的相互客戶輕鬆採用。
  * 如果已啟用單鍵 SSO，您客戶的 IT 系統管理員就不需要瞭解如何設定您的應用程式以在其組織中使用。

* 您可以讓客戶能夠完全管理其員工和來賓身分識別的驗證和授權。

  * 將所有帳戶管理和合規性責任放在這些身分識別的客戶擁有者上。

  * 為特定身分識別提供者、群組或使用者提供啟用或停用 SSO 以滿足其商務需求的能力。

* 您會增加 marketability 和 adoptability。 許多大型組織要求（或渴望）其員工在所有應用程式中都有順暢的 SSO 體驗。 讓 SSO 變得簡單很重要。

* 您可以減少使用者的摩擦，這可能會增加使用者的使用方式並增加您的收益。

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>如何在已發行的應用程式中啟用單一登入

1. [為您的多租使用者應用程式選擇正確的同盟通訊協定](isv-choose-multi-tenant-federation.md)。
1. 在您的應用程式中執行 SSO
   - 請參閱[驗證模式的指引](../develop/v2-app-types.md)
   - 請參閱適用于 OIDC 和 OAuth 通訊協定的[Azure Active Directory 程式碼範例](../develop/sample-v2-code.md)
1. [建立您的 Azure 租](isv-tenant-multi-tenant-app.md)使用者並測試您的應用程式
1. [在您的網站上建立併發布 SSO 檔](isv-create-sso-documentation.md)。
1. 向 Microsoft[提交您的應用程式清單](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)和合作夥伴，以在 microsoft 網站上建立檔。
1. [加入 Microsoft 合作夥伴網路（免費），並建立您的進入市場計畫](https://partner.microsoft.com/en-us/explore/commercial#gtm)。
