---
title: 什麼是 Azure Active Directory 中的應用程式管理
description: 概述如何使用 Azure Active Directory (AD) 作為雲端和內部部署應用程式的身分識別與存取權管理 (IAM) 系統。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: ad572188ceb15a948e4242d0521b8304db45e65b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732336"
---
# <a name="what-is-application-management"></a>什麼是應用程式管理？

Azure AD 是身分識別與存取權管理 (IAM) 系統。 其提供單一位置來儲存數位身分識別的相關資訊。 您可以將軟體應用程式設定為使用 Azure AD 作為儲存使用者資訊的位置。 

Azure AD 必須設定為與應用程式整合。 換句話說，它必須知道哪些應用程式會使用它來作為身分識別。 讓 Azure AD 知道這些應用程式，以及它應該如何處理它們，就稱為應用程式管理。

您可以在 [ **企業應用程式** ] 頁面上的 [管理] Azure Active Directory 區段中，管理應用程式。

![Azure AD 入口網站 [管理] 區段下的 [企業應用程式] 選項。](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>身分識別與存取權管理 (IAM) 系統是什麼？
應用程式是一種用於某些用途的軟體。 大部分的應用程式都需要使用者登入。

集中式身分識別系統提供單一位置來儲存使用者資訊，然後可供所有應用程式使用。 這些系統都稱為身分識別與存取權管理 (IAM) 系統。 Azure Active Directory 是 Microsoft 雲端的 IAM 系統。

>[!TIP]
>IAM 系統提供單一位置來追蹤使用者身分識別。 Azure AD 是 Microsoft 雲端的 IAM 系統。

## <a name="why-manage-applications-with-a-cloud-solution"></a>為何要以雲端解決方案來管理應用程式？

組織通常會有使用者完成工作所需的數百個應用程式。 使用者會從許多裝置及位置存取這些應用程式。 新的應用程式會隨時新增、開發和終止。 有了這麼多應用程式和存取點，請務必使用可搭配使用的身分識別解決方案。

>[!TIP]
>Azure AD 應用程式資源庫包含許多已預先設定為以識別提供者身分使用 Azure AD 的熱門應用程式。

## <a name="how-does-azure-ad-work-with-apps"></a>Azure AD 如何與應用程式搭配運作？

Azure AD 位於中央，並提供雲端和內部部署應用程式的身分識別管理。 

![此圖表顯示透過 Azure AD 同盟的應用程式](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>藉由將使用者布建 [自動化](../app-provisioning/user-provisioning.md) 來減少系統管理成本，讓使用者在您將使用者新增至公司的 HR 系統時自動新增至 Azure AD。 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>哪些類型的應用程式可與 Azure AD 整合？

您可以使用 Azure AD 作為您的身分識別系統，就像任何應用程式一樣。 許多應用程式都已預先設定，且可透過最少量的工作進行設定。 這些預先設定的應用程式會發佈在 [Azure AD App 資源庫](/azure/active-directory/saas-apps/)中。 

如果您的應用程式還不在資源庫中，您可以手動設定大部分的應用程式進行單一登入。 Azure AD 提供數個 SSO 選項。 其中一些最受歡迎的是以 SAML 為基礎的 SSO 和 OIDC 型 SSO。 若要深入瞭解如何整合應用程式以啟用 SSO，請參閱 [單一登入選項](sso-options.md)。 

您的組織是否使用內部部署應用程式？ 您可以使用應用程式 Proxy 來整合它們。 若要深入瞭解，請參閱 [透過 Azure AD 的應用程式 Proxy 提供對內部部署應用程式的遠端存取](application-proxy.md)。

>[!TIP]
>建立您自己的企業營運應用程式時，您可以將它們與 Azure AD 整合以支援單一登入。 若要深入瞭解如何開發 Azure AD 的應用程式，請參閱 [Microsoft 身分識別平臺](..//develop/v2-overview.md)。

## <a name="manage-risk-with-conditional-access-policies"></a>使用條件式存取原則來管理風險

結合 Azure AD 單一登入 (SSO) 及[條件式存取](../conditional-access/concept-conditional-access-cloud-apps.md)，將能針對應用程式的存取提供高層級的安全性。 條件式存取原則會根據您設定的條件，對應用程式提供細微的控制。 

## <a name="improve-productivity-with-single-sign-on"></a>使用單一登入改善生產力

單一登入 (SSO) 可在 Microsoft 365 與您使用的所有其他應用程式之間提供一致的使用者體驗。 說，要不斷地輸入您的使用者名稱和密碼！

若要深入瞭解單一登入，請參閱 [什麼是單一登入](what-is-single-sign-on.md)。

## <a name="address-governance-and-compliance"></a>處理治理和合規性

透過使用安全性事件和事件監視 (SIEM) 工具的報告來監視應用程式。 您可以從入口網站或 API 存取這些報告。 以程式設計方式對存取您應用程式的人員進行稽核，並透過存取權檢閱來移除非使用中使用者的存取權。

## <a name="manage-costs"></a>管理成本

透過移轉至 Azure AD，您可以降低成本並免除自行管理內部部署基礎結構的麻煩。 Azure AD 也能針對應用程式提供自助存取，這能同時為系統管理員和使用者節省寶貴時間。 單一登入可免除應用程式專用密碼的使用。 這種只需登入一次的功能，可省下因重設應用程式密碼，以及擷取密碼時喪失生產力所導致的相關成本。

針對人力資源導向的應用程式，或具有大量使用者的其他應用程式，您可以使用應用程式布建讓您的生活更輕鬆。 應用程式布建可將新增和移除使用者的流程自動化。 若要深入瞭解，請參閱[什麼是應用程式](../app-provisioning/user-provisioning.md)布建？

## <a name="next-steps"></a>後續步驟

- [應用程式管理快速入門系列](view-applications-portal.md)
- [開始使用應用程式整合](plan-an-application-integration.md)
- [了解如何自動化佈建](../app-provisioning/user-provisioning.md)