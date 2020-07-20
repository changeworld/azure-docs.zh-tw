---
title: 什麼是 Azure AD 的密碼雜湊同步處理？ | Microsoft Docs
description: 描述密碼雜湊同步處理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e40eecce3ff0ea5b252fd135ae553fa20e0a77b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386346"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>什麼是 Azure AD 的密碼雜湊同步處理？
密碼雜湊同步處理是其中一種用來完成混合式身分識別的登入方法。 Azure AD Connect 可將使用者密碼的雜湊從內部部署 Active Directory 執行個體，同步到雲端式 Azure AD 執行個體。

密碼雜湊同步處理是 Azure AD Connect 同步所實作的目錄同步作業功能的延伸。您可以使用這項功能來登入 Office 365 之類的 Azure AD 服務。 用來登入服務的密碼和您用來登入內部部署 Active Directory 執行個體的密碼相同。

![何謂 Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

密碼雜湊同步處理可透過將使用者需要維護的密碼數目減少到只剩一個來協助您。 密碼雜湊同步處理可以：

* 提升使用者的生產力。
* 降低技術支援成本。  

密碼雜湊同步處理也可為您的混合式帳戶啟用[洩漏認證偵測](../identity-protection/concept-identity-protection-risks.md#user-risk)。 Microsoft 與暗網研究人員和執法機關合作，尋找公開可用的使用者名稱/密碼配對。 如果其中任何一組配對符合我們的使用者，則會將相關聯的帳戶移至高風險。 

>[!NOTE]
> 只會針對您的租用戶處理在您啟用 PHS 之後所找到新的洩漏認證。 不會對先前找到的認證配對進行驗證。


如果您選擇使用[與 Active Directory 同盟服務 (AD FS) 同盟](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)作為單入方法，則可以視需要設定密碼雜湊同步處理作為備用方式。

若要在環境中使用密碼雜湊同步處理，您需要︰

* 安裝 Azure AD Connect。  
* 設定內部部署 Active Directory 執行個體與 Azure Active Directory 執行個體之間的目錄同步作業。
* 啟用密碼雜湊同步處理。



如需詳細資訊，請參閱[什麼是混合式身分識別？](whatis-hybrid-identity.md)。




## <a name="next-steps"></a>後續步驟

- [什麼是混合式身分識別？](whatis-hybrid-identity.md)
- [什麼是 Azure AD Connect 和 Connect Health？](whatis-azure-ad-connect.md)
- [什麼是傳遞驗證 (PTA)？](how-to-connect-pta.md)
- [什麼是同盟？](whatis-fed.md)
- [什麼是單一登入？](how-to-connect-sso.md)
- [密碼雜湊同步處理如何運作](how-to-connect-password-hash-synchronization.md)
