---
title: 比較外部身分識別 - Azure Active Directory | Microsoft Docs
description: Azure AD 外部身分識別可讓貴組織外部的人員使用自己的身分識別來存取您的應用程式和資源。 比較外部身分識別的解決方案，包括 Azure Active Directory B2B 共同作業和 Azure AD B2C。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08623e6d69ac4db1790c1e9b46089f0c72c0526d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87910453"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Azure Active Directory 中的外部身分識別為何？

透過 Azure AD 中的外部身分識別，您可允許組織外部的人員存取您的應用程式和資源，同時讓他們使用自己偏好的任何身分識別進行登入。 您的合作夥伴、經銷商、供應商、廠商和其他來賓使用者都可以「自備身分識別」。 無論是屬於 Azure AD 或另一個 IT 管理的系統，還是具有非受控的社交身分識別 (例如 Google 或 Facebook)，他們都可以使用自己的認證進行登入。 識別提供者會管理外部使用者的身分識別，而您可使用 Azure AD 來管理對應用程式的存取，讓您的資源受到保護。 

## <a name="external-identities-scenarios"></a>外部身分識別案例

Azure AD 外部身分識別比較不注重使用者與貴組織的關聯性，而比較著重於個人想要登入您應用程式和資源的方式。 在此架構中，Azure AD 支援企業對企業 (B2B) 共同作業的各種案例，以供客戶和消費者 (企業對消費者或 B2C) 進行應用程式開發。

- **與外部使用者 (B2B 共同作業) 共用應用程式**。 邀請外部使用者加入您自己的租用戶，成為可對其指派權限 (進行授權)，同時允許他們使用其現有認證 (進行驗證) 的「來賓」使用者。 使用者透過他們的公司帳戶、學校帳戶或任何電子郵件帳戶，使用簡單的邀請與兌換程序登入共用資源。 現在利用自助式註冊使用者流程 (預覽)，您也可透過您想共用的應用程式，為外部使用者提供登入體驗。 您可以設定使用者流程設定，以控制使用者註冊應用程式的方式，並可讓他們使用其工作帳戶、學校帳戶或他們想要使用的任何社交身分識別 (例如 Google 或 Facebook)。  如需詳細資訊，請參閱 [Azure AD B2B 文件](index.yml)。

- **開發適用於其他 Azure AD 租用戶 (單一租用戶或多租用戶) 的應用程式**。 開發適用於 Azure AD 的應用程式時，您的開發對象可以是來自單一組織 (單一租用戶) 的使用者，或是來自任何已有 Azure AD 租用戶之組織的使用者 (稱為多租用戶應用程式)。 這些多租用戶應用程式會由您在自己的 Azure AD 中自行註冊一次，但之後可供任何組織中的任何 Azure AD 使用者使用，而您不需要執行任何額外的工作。

- **為消費者和客戶 (Azure AD B2C) 開發白標應用程式**。 如果您是建立客戶對應應用程式的企業或開發人員，則可使用 Azure AD B2C 來擴展至消費者、客戶或公民。 開發人員可以將 Azure AD 作為完整的身分識別系統，同時讓客戶使用他們已經建立的身分識別 (例如 Facebook 或 Gmail) 登入。 使用 Azure AD B2C，您可以全完自訂及控制客戶註冊、登入的方式，以及在他們使用您的應用程式時管理其設定檔。 如需詳細資訊，請參閱 [Azure AD B2C 文件](https://docs.microsoft.com/azure/active-directory-b2c/)。

## <a name="compare-external-identities-solutions"></a>比較外部身分識別解決方案

下表提供您可使用 Azure AD 外部身分識別啟用的各種案例詳細比較。

| 多租用戶應用程式  | 外部使用者共同作業 (B2B) | 消費者或客戶的應用程式 (B2C)  |
| ---- | --- | --- |
| 主要案例：企業軟體即服務 (SaaS) | 主要案例：使用 Microsoft 應用程式 (Microsoft 365、Teams、...) 或自己的共同作業軟體進行共同作業。  | 主要案例：使用自訂開發應用程式的交易式應用程式。   |
| 適用對象：想要為許多企業客戶提供軟體的組織。    | 適用對象：想要能夠驗證來自合作夥伴組織 (無論是否為識別提供者) 之使用者的組織。    | 適用對象：邀請您的行動和 Web 應用程式客戶 (無論是個人、機構或組織客戶) 加入 Azure AD 目錄，該目錄與您自有的組織目錄分開。 |
| 支援的身分識別︰具有 Azure AD 帳戶的員工。 | 支援的身分識別︰本機有工作或學校帳戶的員工、本機有工作或學校帳戶的合作夥伴、或任何電子郵件地址。 即將支援直接聯盟。      | 支援的身分識別︰具有本機應用程式帳戶的取用者使用者 (任何電子郵件地址或使用者名稱) 或任何以直接聯盟支援的社交身分識別。       |
| 外部使用者會在自己的目錄中進行管理，與應用程式註冊所在的目錄隔離。    | 外部使用者與員工在相同的目錄中管理的，但特別註解。 管理他們的方式可以和員工相同、他們可以加入到相同的群組等等。    | 外部使用者在應用程式目錄中管理的。 他們和組織的員工及合作夥伴目錄 (若有) 分開管理。  |
| 單一登入：支援 SSO 至所有與 Azure AD 連線的應用程式。          | 單一登入：支援 SSO 至所有與 Azure AD 連線的應用程式。 例如，您可以提供 Microsoft 365 或內部部署應用程式的存取權，以及其他 SaaS 應用程式 (例如 Salesforce 或 Workday) 的存取權。    | 單一登入：支援 SSO 至客戶在 Azure AD B2C 租用戶內擁有的應用程式。 不支援 SSO 至 Microsoft 365 或其他 Microsoft SaaS 應用程式。    |
| 客戶生命週期︰由使用者的主要組織管理。      | 合作夥伴生命週期：由主控/邀請組織管理。    | 客戶生命週期︰自助式管理或由應用程式管理。      |
| 安全性原則和合規性：由主控/邀請組織管理 (例如，使用[條件式存取原則](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access))。           | 安全性原則和合規性：由主控/邀請組織管理 (例如，使用[條件式存取原則](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access))。 | 安全性原則和合規性：受應用程式管理。        |
| 商標：使用主控/邀請組織的品牌。   | 商標：使用主控/邀請組織的品牌。    | 商標：受應用程式管理。 通常多半會是產品品牌加上組織在背景淡出的效果。   |
| 其他資訊：[管理多租用戶應用程式中的身分識別](https://docs.microsoft.com/azure/architecture/multitenant-identity/)、[操作說明指南](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | 其他資訊：[部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文件](what-is-b2b.md)                   | 其他資訊：[產品頁面](https://azure.microsoft.com/services/active-directory-b2c/)、[文件](https://docs.microsoft.com/azure/active-directory-b2c/)       |

使用 Azure AD 外部身分識別，保護及管理組織界限以外的客戶和合作夥伴。

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [關於 Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
