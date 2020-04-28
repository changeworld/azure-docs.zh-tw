---
title: B2B 共同作業授權指引-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 共同作業不需要 Azure AD 付費授權，但您也可以取得付費功能給 B2B 來賓使用者
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74868842"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 共同作業授權指引

使用 Azure Active Directory (Azure AD) 企業對企業 (B2B) 共同作業時，您可以邀請「外部使用者」(或「來賓使用者」) 來使用您的付費 Azure AD 服務。 有些功能是免費的，但對於任何付費的 Azure AD 功能，您可以針對您租使用者中員工或非來賓使用者所擁有的每個 Azure AD edition 授權，邀請最多五個來賓使用者。

> [!NOTE]
> 如需 Azure AD 定價和 B2B 共同作業功能的詳細資訊，請參閱[Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。

系統會根據 1:5 的比例來自動計算和回報 B2B 來賓使用者授權。 

此外，來賓使用者無須符合任何額外的授權需求，即可使用免費的 Azure AD 功能。 即使您沒有任何付費的 Azure AD 授權，來賓使用者也可以存取免費的 Azure AD 功能。 

## <a name="examples-calculating-guest-user-licenses"></a>範例：計算來賓使用者授權
在您判斷出有多少來賓使用者需要存取您的付費 Azure AD 服務之後，請確定您有足夠的 Azure AD 付費授權可涵蓋所需 1:5 比例的來賓使用者。 以下是一些範例：

- 您想要邀請100來賓使用者加入 Azure AD 的應用程式或服務，並提供存取管理和布建。 針對這些來賓使用者的50，您也想要要求 MFA 和條件式存取，因此若要使用這些功能，您將需要10個 Azure AD Premium P1 授權。 如果您打算對來賓使用者使用「身分識別保護」功能，則需要同樣 1:5 比例的 Azure AD Premium P2 授權，才能這些使用者。
- 您想要邀請 60 位全都必須使用 MFA 的來賓使用者，因此您必須至少有 12 個 Azure AD Premium P1 授權。 您有 10 個具備 Azure AD Premium P1 授權的員工，在 1:5 授權比例下，這將最多可允許 50 位來賓使用者。 您將需要購買 2 個額外的 Premium P1 授權，才能涵蓋 10 位額外的來賓使用者。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的資源：

* [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)
* [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](faq.md)
