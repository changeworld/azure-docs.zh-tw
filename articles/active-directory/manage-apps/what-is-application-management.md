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
ms.date: 07/01/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 293aa80336b259a760a835e3ece56d50287f06ed
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299876"
---
# <a name="what-is-application-management"></a>什麼是應用程式管理？

Azure AD 是身分識別與存取權管理 (IAM) 系統。 其提供單一位置來儲存數位身分識別的相關資訊。 您可以將軟體應用程式設定為使用 Azure AD 作為儲存使用者資訊的位置。 

Azure AD 必須設定為與應用程式整合。 換句話說，Azure AD 需要知道哪些應用程式使用其作為身分識別系統。 讓 Azure AD 知道這些應用程式，以及其處理方式，稱為應用程式管理。

您可以在位於 Azure Active Directory 入口網站 [管理] 區段中的 [企業應用程式] 刀鋒視窗上，管理應用程式。

![Azure AD 入口網站 [管理] 區段下的 [企業應用程式] 選項。](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>身分識別與存取權管理 (IAM) 系統是什麼？
應用程式是一種用於某些用途的軟體。 大部分的應用程式都需要使用者登入，讓應用程式可以為該特定使用者提供量身打造的體驗。 換句話說，應用程式必須知道使用應用程式的使用者身分識別。 因為應用程式知道要為使用者提供或移除哪些功能。

如果每個應用程式分別追蹤使用者，則結果會是每個應用程式不同使用者名稱和登入的定址接收器。 其中一個應用程式不知道其他應用程式中使用者的任何資訊。

集中式身分識別系統會藉由提供單一位置來儲存使用者資訊，供所有應用程式使用，來解決這個問題。 這些系統都稱為身分識別與存取權管理 (IAM) 系統。 Azure Active AD 是 Microsoft 雲端的 IAM 系統。

>[!TIP]
>IAM 系統提供單一位置來追蹤使用者身分識別。 Azure AD 是 Microsoft 雲端的 IAM 系統。


## <a name="why-manage-applications-with-a-cloud-solution"></a>為何要以雲端解決方案來管理應用程式？

組織通常會有使用者完成工作所需的數百個應用程式。 使用者會從許多裝置及位置存取這些應用程式。 每天都有許多應用程式被新增、開發及淘汰。 由於有這麼多的應用程式及存取點，使用雲端式解決方案來管理所有應用程式的使用者存取就變得更加重要。

>[!TIP]
>Azure AD 應用程式資源庫包含許多已預先設定為以識別提供者身分使用 Azure AD 的熱門應用程式。

## <a name="how-does-azure-ad-work-with-applications"></a>Azure AD 如何與應用程式搭配使用？

Azure AD 可藉由為雲端和內部部署應用程式提供單一的身分識別系統，來簡化應用程式的管理方式。 您可以在 Azure AD 中新增軟體即服務 (SaaS) 應用程式、內部部署應用程式和企業營運 (LOB) 應用程式。 然後，使用者僅需登入一次，便可以安全且順暢地存取這些應用程式以及 Office 365 和其他來自 Microsoft 的商務應用程式。 您可以藉由[將使用者佈建自動化](../app-provisioning/user-provisioning.md)來降低管理成本。 您也可以使用多重要素驗證及條件式存取原則來提供安全的應用程式存取。

![此圖表顯示透過 Azure AD 同盟的應用程式](media/what-is-application-management/app-management-overview.png)

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>哪些類型的應用程式可與 Azure AD 整合？

可在**企業應用程式**中新增並使用 Azure AD 來管理的應用程式有四大類型：

- **Azure AD 資源庫應用程式** – Azure AD 擁有資源庫，內含數千個已預先整合而能使用 Azure AD 來進行單一登入的應用程式。 您組織使用的某些應用程式可能就在資源庫中。 [請了解如何規劃應用程式整合](plan-an-application-integration.md)，或在 [SaaS 應用程式教學課程](https://docs.microsoft.com/azure/active-directory/saas-apps/)中取得個別應用程式的詳細整合步驟。

- **內部部署應用程式搭配應用程式 Proxy** – 使用 Azure AD 應用程式 Proxy 時，您可以整合內部部署 Web 應用程式與 Azure AD 以支援單一登入。 然後，使用者可以使用存取 Office 365 和其他 SaaS 應用程式的相同方式來存取內部部署 Web 應用程式，請參閱[透過 Azure AD 的應用程式 Proxy，提供內部部署應用程式的遠端存取](application-proxy.md)。

- **自訂開發的應用程式** – 在建置您自己的企業營運應用程式時，可將其與 Azure AD 進行整合以支援單一登入。 藉由向 Azure AD 註冊應用程式，您便可掌控應用程式的驗證原則。 如需詳細資訊，請參閱[開發人員指導方針](developer-guidance-for-integrating-applications.md)。

- **不在資源庫內的應用程式** – 引進您自己的應用程式！ 支援藉由將其他應用程式新增至 Azure AD 來啟用其單一登入功能。 有多種方式可以整合應用程式，以下列出其中部分。 如需詳細資訊，請參閱[設定 SAML 單一登入](configure-saml-single-sign-on.md)。

>[!TIP]
>即使應用程式尚未預先設定且未在應用程式資源庫中，您也可以整合 Azure AD 與應用程式。 您可以**整合 Azure AD 與任何**下列項目
> - 會轉譯**使用者名稱和密碼欄位**的任何 Web 連結或應用程式。
> - 支援 **SAML 或 OpenID Connect 通訊協定**的任何應用程式。
> - 支援**跨網域身分識別管理系統 (SCIM)** 標準的任何應用程式。

## <a name="manage-risk-with-conditional-access-policies"></a>使用條件式存取原則來管理風險

結合 Azure AD 單一登入 (SSO) 及[條件式存取](../conditional-access/concept-conditional-access-cloud-apps.md)，將能針對應用程式的存取提供高層級的安全性。 安全性功能包括雲端規模的身分識別保護、以風險為基礎的存取控制、原生的多重要素驗證，以及條件式存取原則。 這些功能可提供以應用程式為基礎的細微控制原則，或是針對需要較高安全性層級的群組提供。

## <a name="improve-productivity-with-single-sign-on"></a>使用單一登入改善生產力

在應用程式和 Office 365 上啟用單一登入 (SSO)，能透過減少或消除登入提示，來為現有的使用者提供更優異的登入體驗。 透過免除看見多次提示，或是管理多組密碼的需求，使用者的作業環境將會變得更為一致，且較不容易令人分心。 商務群組可透過自助服務及動態成員資格來管理及核准存取權。 透過僅允許公司中的適當人員來管理應用程式的存取權，便能改善身分識別系統的安全性。

SSO 能改善安全性。 「在沒有單一登入的情況下」，系統管理員便需要逐一針對個別應用程式建立並更新使用者帳戶，這需要花費許多時間。 此外，使用者必須記住多個認證來存取其應用程式。 如此一來，使用者會傾向將密碼寫下，或使用其他密碼管理解決方案，而這會帶來資料安全性上的風險。 [深入了解單一登入](what-is-single-sign-on.md)。

## <a name="address-governance-and-compliance"></a>處理治理和合規性

透過 Azure AD，您可以透過能運用安全性事件與事件監視 (SIEM) 工具的報告，來監視應用程式登入。 您可以從入口網站或 API 存取這些報告。 以程式設計方式對存取您應用程式的人員進行稽核，並透過存取權檢閱來移除非使用中使用者的存取權。

## <a name="manage-costs"></a>管理成本

透過移轉至 Azure AD，您可以降低成本並免除自行管理內部部署基礎結構的麻煩。 Azure AD 也能針對應用程式提供自助存取，這能同時為系統管理員和使用者節省寶貴時間。 單一登入可免除應用程式專用密碼的使用。 這種只需登入一次的功能，可省下因重設應用程式密碼，以及擷取密碼時喪失生產力所導致的相關成本。

對於著重在人力資源的應用程式或其他具有大量使用者的應用程式，您可以利用應用程式佈建來自動化佈建和解除佈建使用者的程序，請參閱[應用程式佈建是什麼？](../app-provisioning/user-provisioning.md)。

## <a name="next-steps"></a>後續步驟

- [應用程式管理快速入門系列](view-applications-portal.md)
- [開始使用應用程式整合](plan-an-application-integration.md)
- [了解如何自動化佈建](../app-provisioning/user-provisioning.md)
