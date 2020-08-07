---
title: B2B 共同作業的限制-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 共同作業目前的限制
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 127f05298483dcf155000c2eda8f590fc069a1e9
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908686"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 共同作業的限制
Azure Active Directory (Azure AD) B2B 共同作業目前受限於本文所述的限制。

## <a name="possible-double-multi-factor-authentication"></a>可能的雙重多重要素驗證
使用 Azure AD B2B，您便可以在資源組織 (邀請組織) 強制執行多重要素驗證。 適用于 B2B 共同作業[使用者的條件式存取](conditional-access.md)中會詳細說明這種方法的原因。 如果合作夥伴已設定並強制使用 Multi-Factor Authentication，其使用者可能必須在其所屬組織執行一次驗證，然後在您的組織中再次執行驗證。

## <a name="instant-on"></a>即時
在 B2B 共同作業流程中，我們會新增使用者到目錄中，並在邀請兌換與應用程式指派等期間動態更新它們。 更新與寫入通常會在一個目錄執行個體中進行，而且必須複寫到所有執行個體。 在所有執行個體都更新後，複寫作業便已完成。 有時，在某個執行個體中寫入或更新物件，但另一個執行個體呼叫擷取此物件時，就會發生複寫延遲問題。 如果發生該情況，請重新整理或重試來提供幫助。 如果您正在使用我們的 API 來撰寫應用程式，則採用某種讓步來重試是可減輕此問題的良好防禦性做法。

## <a name="azure-ad-directories"></a>Azure AD 目錄
Azure AD B2B 受限於 Azure AD 服務目錄限制。 如需使用者可建立的目錄數目以及使用者或來賓使用者可屬於的目錄數目詳細資訊，請參閱 [Azure AD 服務限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。

## <a name="national-clouds"></a>國家雲端
[國家](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)雲端是實體獨立的 Azure 實例。 不支援跨國家雲端界限進行 B2B 共同作業。 例如，如果您的 Azure 租使用者位於公用的全域雲端，您就無法邀請其帳戶位於國家雲端的使用者。 若要與使用者共同作業，請要求他們提供另一個電子郵件地址，或在您的目錄中建立成員使用者帳戶。

## <a name="azure-us-government-clouds"></a>Azure 美國政府雲端
在 Azure 美國政府雲端中，同時在 Azure 美國政府雲端內的租使用者和兩者都支援 B2B 共同作業的租使用者之間，支援 B2B 共同作業。 支援 B2B 共同作業的 Azure 美國政府租使用者也可以使用 Microsoft 或 Google 帳戶與社交使用者共同作業。 如果您邀請這些群組以外的使用者 (例如，如果使用者位於不屬於 Azure 美國政府雲端的租使用者，或尚未支援 B2B 共同作業) ，邀請將會失敗，或使用者將無法兌換邀請。 如需其他限制的詳細資訊，請參閱[Azure Active Directory Premium P1 和 P2 變化](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)。

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>如何判斷我的 Azure 美國政府租使用者是否可以使用 B2B 共同作業？
若要瞭解您的 Azure 美國政府雲端租使用者是否支援 B2B 共同作業，請執行下列動作：

1. 在瀏覽器中，移至下列 URL，並以您的租使用者名稱取代* &lt; tenantname &gt; *：

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. `"tenant_region_scope"`在 JSON 回應中尋找：

   - 如果 `"tenant_region_scope":"USGOV”` 出現，則支援 B2B。
   - 如果 `"tenant_region_scope":"USG"` 出現，則不支援 B2B。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [委派 B2B 共同作業邀請](delegate-invitations.md)
