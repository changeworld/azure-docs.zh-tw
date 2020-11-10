---
title: 何謂使用 Azure Active Directory 進行目錄間佈建？ | Microsoft Docs
description: 說明身分識別目錄間佈建的概觀。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cbca2d00d10b6595b8a1d13251dd88c65667541
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134903"
---
# <a name="what-is-inter-directory-provisioning"></a>什麼是目錄間的佈建？

目錄是一種共用資訊基礎結構，用於尋找、管理及組織項目和網路資源。  Microsoft Active Directory 和 Azure AD 都是使用目錄服務的應用程式範例。  身分識別可協助目錄系統做出決定，例如誰可以存取哪些內容，以及誰可以使用特定資源。

目錄間佈建會在兩個不同的目錄服務系統之間佈建身分識別。   目錄間佈建的最常見案例是已在 Active Directory 中的使用者佈建到 Azure AD 時。 此佈建可由代理程式完成，例如 Azure AD Connect 同步或 Azure AD Connect 雲端佈建。

目錄間佈建可讓我們建立[混合式身分識別](../hybrid/whatis-hybrid-identity.md)環境。


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Azure AD 支援那些類型的目錄間佈建

Azure AD 目前支援三種方法來完成目錄間佈建。 方法如下：

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) - Microsoft 工具，其設計目的是為了符合並完成您的混合式身分識別，包括從 Active Directory 到 Azure AD 的目錄間佈建。

- [Azure AD Connect 雲端佈建](../cloud-provisioning/what-is-cloud-provisioning.md)- 新的 Microsoft 代理程式，其設計目的是要符合並完成您的混合式身分識別目標。  其可提供 Active Directory 與 Azure AD 之間的輕量型目錄間佈建體驗。

- [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) - Microsoft 的內部部署身分識別和存取管理解決方案，可協助您管理貴組織內的使用者、認證、原則和存取權。 此外，MIM 提供了進階的目錄間佈建，以達成 Active Directory、Azure AD 和其他目錄的混合式身分識別環境。

### <a name="key-benefits"></a>主要權益

此種目錄間佈建功能提供下列顯著的商業優勢：

- [密碼雜湊同步處理](../hybrid/whatis-phs.md) - 一種將使用者內部部署 AD 密碼的雜湊與 Azure AD 同步的登入方法。
- [傳遞驗證](../hybrid/how-to-connect-pta.md) - 一種登入方法，可讓使用者在內部部署環境與雲端中使用相同的密碼，但不需要額外的同盟環境基礎結構。
- [同盟整合](../hybrid/how-to-connect-fed-whatis.md) - 可用來以內部部署 AD FS 基礎結構設定混合式環境。 它也提供 AD FS 管理功能，例如憑證更新及額外的 AD FS 伺服器部署。
- [同步處理](../hybrid/how-to-connect-sync-whatis.md) - 負責建立使用者、群組及其他物件。  此外，也確保您內部部署使用者和群組的身分識別資訊與雲端相符。  此同步處理也包括密碼雜湊。
- [狀況監控](../hybrid/whatis-hybrid-identity-health.md) - 可以提供健全監控，並在 Azure 入口網站中提供檢視此活動的中央位置。 


## <a name="next-steps"></a>後續步驟 
- [什麼是身分識別生命週期管理？](what-is-identity-lifecycle-management.md)
- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 HR 驅動佈建？](what-is-hr-driven-provisioning.md)
- [什麼是應用程式佈建？](what-is-app-provisioning.md)
