---
title: 比較外部身分識別 - Azure Active Directory | Microsoft Docs
description: Azure AD 外部身分識別可讓貴組織外部的人員使用自己的身分識別來存取您的應用程式和資源。 比較外部身分識別的解決方案，包括 Azure Active Directory B2B 共同作業和 Azure AD B2C。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/23/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6be0720b7eafb251b262d5cd182bdf06e201402d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173068"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Azure Active Directory 中的外部身分識別為何？

透過 Azure AD 中的外部身分識別，您可允許組織外部的人員存取您的應用程式和資源，同時讓他們使用自己偏好的任何身分識別進行登入。 您的合作夥伴、經銷商、供應商、廠商和其他來賓使用者都可以「自備身分識別」。 無論是擁有公司或政府簽發的數位身分識別，還是具有非受控的社交身分識別 (例如 Google 或 Facebook)，他們都可以使用自己的認證登入。 外部使用者的識別提供者會管理其身分識別，而您可使用 Azure AD 來管理對應用程式的存取，讓您的資源受到保護。

## <a name="external-identities-scenarios"></a>外部身分識別案例

Azure AD 外部身分識別比較不注重使用者與貴組織的關聯性，而比較著重於個人想要登入您應用程式和資源的方式。 在此架構中，Azure AD 支援企業對企業 (B2B) 共同作業的各種案例，以存取客戶/客戶或公民對應應用程式 (企業對客戶，或 B2C)。

- **與外部使用者 (B2B 共同作業) 共用應用程式和資源**。 邀請外部使用者加入您自己的租用戶，成為可對其指派權限 (進行授權)，同時允許他們使用其現有認證 (進行驗證) 的「來賓」使用者。 使用者透過他們的公司、學校或其他電子郵件帳戶，使用簡單的邀請與兌換程序登入共用資源。 您也可以使用 [Azure AD 權利管理](../governance/entitlement-management-overview.md)來設定原則，以[管理外部使用者的存取權](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)。 現在您可以使用[自助式註冊使用者流程 (預覽)](self-service-sign-up-overview.md)，允許外部使用者自行註冊應用程式。 您可以自訂此體驗，以允許使用工作、學校或社交身分識別 (例如 Google 或 Facebook) 註冊。 您也可以在註冊過程中收集使用者的相關資訊。 如需詳細資訊，請參閱 [Azure AD B2B 文件](index.yml)。

- **使用適用於取用者和客戶面向應用程式 (Azure AD B2C)** 的白標身分識別管理解決方案，建立使用者旅程。 如果您是建立客戶對應應用程式的企業或開發人員，則可使用 Azure AD B2C 來擴展至數百萬名消費者、客戶或公民。 開發人員可以使用 Azure AD 作為其應用程式的全功能客戶身分識別和存取管理 (CIAM) 系統。 客戶可以使用已建立的身分識別 (例如 Facebook 或 Gmail) 登入。 使用 Azure AD B2C，您可以全完自訂及控制客戶註冊、登入的方式，以及在他們使用您的應用程式時管理其設定檔。 如需詳細資訊，請參閱 [Azure AD B2C 文件](../../active-directory-b2c/index.yml)。

## <a name="compare-external-identities-solutions"></a>比較外部身分識別解決方案

下表提供您可使用 Azure AD 外部身分識別啟用的案例詳細比較。

|   | 外部使用者共同作業 (B2B) | 存取取用者/客戶對應的應用程式 (B2C)  |
| ---- | --- | --- |
| **主要案例** | 使用 Microsoft 應用程式 (Microsoft 365、Teams 等) 或您自己的應用程式 (SaaS 應用程式、自訂開發的應用程式等) 共同作業。  | 新式 SaaS 或自訂開發應用程式 (不是第一方 Microsoft 應用程式) 的身分識別與存取管理。   |
| **適用對象**    | 與來自外部組織 (例如供應商、合作夥伴、廠商) 的商業夥伴共同作業。 使用者會在您的目錄中顯示為來賓使用者。 這些使用者不一定會管理 IT。  | 使用產品的客戶。 這些使用者會在個別的 Azure AD 目錄中管理。  |
| **支援的識別提供者** | 外部使用者可以使用工作帳戶、學校帳戶、任何電子郵件地址、SAML 和以 WS 為基礎的身分識別提供者、Gmail 和 Facebook 共同作業。  | 具有本機應用程式帳戶的取用者使用者 (任何電子郵件地址或使用者名稱)、各種支援的社交身分識別，以及具有透過直接聯盟簽發之公司和政府的身分識別。       |
| **外部使用者管理**   | 外部使用者與員工都在相同的目錄中管理，但系統會特別註記為來賓使用者。 管理來賓使用者的方式與員工相同，也可以新增到相同的群組。    | 外部使用者會在 Azure AD B2C 目錄中管理。 他們和組織的員工及合作夥伴目錄 (若有) 分開管理。  |
| **單一登入 (SSO)**      | 支援 SSO 至所有與 Azure AD 連線的應用程式。 例如，您可以提供 Microsoft 365 或內部部署應用程式的存取權，以及其他 SaaS 應用程式 (例如 Salesforce 或 Workday) 的存取權。    | 支援 SSO 至客戶在 Azure AD B2C 租用戶內擁有的應用程式。 不支援 SSO 至 Microsoft 365 或其他 Microsoft SaaS 應用程式。    |
| **安全性原則和合規性**        | 由主控/邀請組織管理 (例如，使用[條件式存取原則](conditional-access.md))。 | 由組織透過條件式存取和身分識別保護來管理。        |
| **商標**  | 使用主控/邀請組織的品牌。    | 可完全依應用程式或組織自訂的商標。   |
| **計費模型** | [外部身分識別定價](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/)以每月作用中使用者 (MAU) 為基礎。 <br>(另請參閱：[B2B 設定詳細資料](external-identities-pricing.md)) | [外部身分識別定價](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/)以每月作用中使用者 (MAU) 為基礎。 <br>(另請參閱：[B2C 設定詳細資料](../../active-directory-b2c/billing.md)) |
| **詳細資訊** | [部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[文件](what-is-b2b.md)                   | [產品頁面](https://azure.microsoft.com/services/active-directory-b2c/)、[文件](../../active-directory-b2c/index.yml)       |

使用 Azure AD 外部身分識別，保護及管理組織界限以外的客戶和合作夥伴。

## <a name="about-multitenant-applications"></a>關於多租用戶應用程式

如果您要提供應用程式即服務，而不想管理客戶的使用者帳戶，則多租用戶應用程式應該是正確的選擇。 開發適用於其他 Azure AD 租用戶的應用程式時，您的開發對象可以是來自單一組織 (單一租用戶) 的使用者，或是來自任何已有 Azure AD 租用戶之組織的使用者 (多租用戶應用程式)。 根據預設，Azure AD 中的應用程式註冊是單一租用戶，但您可以讓將註冊設定為多租用戶。 這個多租用戶應用程式會在您自己的 Azure AD 中自行註冊一次。 但是接下來，任何組織的任何 Azure AD 使用者都可以使用應用程式，而不需要額外設定。 如需詳細資訊，請參閱[管理多租用戶應用程式中的身分識別](/azure/architecture/multitenant-identity/)、[操作指南](../develop/howto-convert-app-to-be-multi-tenant.md)。

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [關於 Azure AD B2C](../../active-directory-b2c/overview.md)