---
title: 混合式身分識別：目錄整合工具比較 | Microsoft Docs
description: 本頁面提供完整資料表，供您比較各種可用於目錄整合的目錄整合工具。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811086"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>混合式身分識別目錄整合工具比較
目錄整合工具已經經過多年的成長和發展。  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29)和[MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016)仍然受支援,主要支援本地系統之間的同步。   FIM 和 MIM 都支援[FIM Windows Azure AD 連接器](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN),但不建議用於新部署 - 具有本地源(如 Notes 或 SAP HCM)的客戶應使用 MIM 填充活動目錄域服務 (AD DS),然後使用 Azure AD 連接同步或 Azure AD 連接雲端配置從 AD DS 同步到 Azure AD。
- [Azure AD 連接同步](how-to-connect-sync-whatis.md)合併了之前在 DirSync 和 Azure AD 同步中發布的元件和功能,用於在 AD DS 林和 Azure AD 之間進行同步。  
- [Azure AD Connect 雲端配置](../cloud-provisioning/what-is-cloud-provisioning.md)是一種新的 Microsoft 代理,用於從 AD DS 到 Azure AD 進行同步,適用於合併和獲取等方案,其中被收購公司的 AD 林與母公司的 AD 林隔離。

要瞭解有關 Azure AD 連接同步和 Azure AD 連接雲端預配之間的區別,請參閱文章[「什麼是 Azure AD 連接雲端資源?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。

