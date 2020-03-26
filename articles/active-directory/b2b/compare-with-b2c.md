---
title: 比較 B2B 共同作業與 B2C - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 共同作業和 Azure AD B2C 有何不同？
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/22/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e7f5aa324db869e30a8b2fe214416129baca8d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "68380744"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>比較 Azure Active Directory 的 B2B 共同作業和 B2C

Azure Active Directory (Azure AD) B2B 共同作業和 Azure AD B2C 皆可讓您在 Azure AD 中使用外部使用者進行操作。 但它們究竟差在哪兒？

**Azure AD B2B** 適用於希望與外部使用者安全地共用檔案及資源，以便他們可以共同作業的企業。 Azure 系統管理員會在 Azure 入口網站中設定 B2B，而 Azure AD 會處理您的企業與外部夥伴之間的同盟。 使用者透過他們的公司或學校帳戶，或任何電子郵件帳戶，使用簡單的邀請與兌換程序登入共用資源。
 
**Azure AD B2C** 主要適用於建立客戶面向之應用程式的開發人員。 使用 Azure AD B2C，開發人員可以將 Azure AD 作為完整的身分識別系統，同時讓客戶使用他們已經建立的身分識別 (例如 Facebook 或 Gmail) 登入。

下表提供詳細的比較。


B2B 共同作業 |     Azure AD B2C 獨立供應項目
-------- | --------
適用對象：想要能夠驗證來自合作夥伴組織 (無論是否為識別提供者) 之使用者的組織。 | 適用對象：邀請您的行動和 Web 應用程式的客戶 (無論是個人、機構或組織客戶) 加入您的 Azure AD。
支援的身分識別︰本機有工作或學校帳戶的員工、本機有工作或學校帳戶的合作夥伴、或任何電子郵件地址。 即將支援直接聯盟。  | 支援的身分識別︰具有本機應用程式帳戶的取用者使用者 (任何電子郵件地址或使用者名稱) 或任何以直接聯盟支援的社交身分識別。
外部使用者與員工在相同的目錄中管理的，但特別註解。 管理他們的方式可以和員工相同、他們可以加入到相同的群組等等  | 外部使用者在應用程式目錄中管理的。 他們和組織的員工及合作夥伴目錄 (若有) 分開管理。
支援單一登入 (SSO) 至所有和 Azure AD 連線的應用程式。 例如，您可以提供 Office 365 或內部部署應用程式的存取權，以及其他 SaaS 應用程式 (例如 Salesforce 或 Workday) 的存取權。  |  支援 SSO 至客戶在 Azure AD B2C 租用戶內擁有的應用程式。 不支援 SSO 至 Office 365 或其他 Microsoft SaaS 應用程式。
合作夥伴生命週期：由主控/邀請組織管理。  | 客戶生命週期︰自助式管理或由應用程式管理。
安全性原則和合規性：由主控/邀請組織管理 (例如，使用[條件式存取原則](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access))。  | 安全性原則和合規性：受應用程式管理。
商標：使用主控/邀請組織的品牌。  |    商標：受應用程式管理。 通常多半會是產品品牌加上組織在背景淡出的效果。
其他資訊：[部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文件](what-is-b2b.md)  | 其他資訊：[產品頁面](https://azure.microsoft.com/services/active-directory-b2c/)、[文件](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [B2B 共同作業使用者屬性](user-properties.md)

