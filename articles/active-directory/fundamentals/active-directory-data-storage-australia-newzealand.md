---
title: 適用于澳大利亞客戶的身分識別資料儲存-Azure AD
description: 瞭解 Azure Active Directory 在何處儲存其澳大利亞客戶的身分識別相關資料。
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460529"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory 中澳大利亞和紐西蘭客戶的身分識別資料存放區

識別資料是根據貴組織在訂閱 Microsoft 線上服務（例如 Office 365 和 Azure）時所提供的位址，在地理位置中 Azure AD 儲存。 如需您的身分識別客戶資料儲存位置的資訊，您可以使用 Microsoft 信任中心的[資料所在位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)一節。

> [!NOTE]
> 與 Azure AD 整合的服務和應用程式可以存取身分識別客戶資料。 評估您所使用的每個服務和應用程式，以判斷該特定服務和應用程式如何處理身分識別客戶資料，以及它們是否符合您公司的資料儲存需求。 若想進一步了解 Microsoft 服務的資料存放處，請參閱 Microsoft 信任中心的 您的資料所在位置 區段。

針對在澳大利亞和紐西蘭提供位址，並使用 Azure AD 免費版的客戶，Azure AD 會在澳大利亞資料中心內保留 PII 資料。 

所有其他 Azure AD premium 服務會將客戶資料儲存在全球資料中心。 若要找出服務的資料中心，請參閱[Azure Active Directory –您的資料位於何處？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure 多重要素驗證（MFA）

Azure AD 中的 MFA 服務會將身分識別客戶資料儲存在待用的全球資料中心。 若要深入瞭解以雲端為基礎的 Azure MFA 和 Azure MFA Server 所收集和儲存的使用者資訊，請參閱[Azure 多重要素驗證使用者資料收集](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)。 如果客戶使用 MFA，其資料將會儲存在非使用中的澳大利亞資料中心以外。 

## <a name="next-steps"></a>後續步驟
如需前述任何特性和功能的詳細資訊，請參閱下列文章：
- [何謂 Multi-Factor Authentication？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
