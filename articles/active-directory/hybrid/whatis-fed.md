---
title: 什麼是與 Azure AD 同盟？ | Microsoft Docs
description: 描述與 Azure AD 的同盟。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb1c3bc9f89db3f4b694803a63293a5537d4b98b
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278747"
---
# <a name="what-is-federation-with-azure-ad"></a>什麼是與 Azure AD 同盟？

同盟是已建立信任的網域集合。 信任層級可能因情況而異，但通常會包括驗證且幾乎一律會包括授權。 典型的同盟可能包括一些已針對一組資源的共用存取權建立信任的組織。

您可以讓內部部署環境與 Azure AD 同盟，然後使用此同盟來進行驗證和授權。  這個登入方法可確保所有使用者驗證都在內部部署環境中進行。  此方法可讓系統管理員實作更嚴格層級的存取控制。 您可以使用與 AD FS 和 PingFederate 的同盟。

![同盟身分識別](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> 如果您選擇使用「與 Active Directory 同盟服務 (AD FS) 同盟」，則可以視需要設定密碼雜湊同步處理來作為 AD FS 基礎結構失敗時的備用方式。


## <a name="next-steps"></a>後續步驟

- [什麼是混合式身分識別？](./whatis-hybrid-identity.md)
- [什麼是 Azure AD Connect 和 Connect Health？](whatis-azure-ad-connect.md)
- [什麼是密碼雜湊同步處理？](whatis-phs.md)
- [什麼是同盟？](whatis-fed.md)
- [什麼是單一登入？](how-to-connect-sso.md)
- [同盟如何運作](how-to-connect-fed-whatis.md)
- [與 PingFederate 同盟](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)