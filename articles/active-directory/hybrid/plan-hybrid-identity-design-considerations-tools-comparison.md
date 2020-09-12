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
ms.openlocfilehash: d9c8f5a75fea6ddd85026407fe4784c8a4589e33
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278304"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>混合式身分識別目錄整合工具比較
目錄整合工具已經經過多年的成長和發展。  


- 但仍支援[FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100))和[MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) ，主要是在內部部署系統之間啟用同步處理。   Fim 和 MIM 都支援 [Fim Windows Azure AD 連接器](/previous-versions/mim/dn511001(v=ws.10)) ，但不建議用於新的部署-內部部署來源（例如附注或 SAP HCM）的客戶應該使用 MIM 來填入 Active Directory Domain Services (AD DS) ，然後也使用 Azure AD Connect 同步或 Azure AD Connect 雲端布建，從 AD DS 同步處理至 Azure AD。
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md) 處理會併入先前在 DirSync 和 Azure AD 同步中發行的元件和功能，以便在 AD DS 樹系和 Azure AD 之間進行同步處理。  
- [Azure AD Connect 雲端](../cloud-provisioning/what-is-cloud-provisioning.md) 布建是一種新的 Microsoft 代理程式，可從 AD DS 同步到 Azure AD，適用于合併和收購等案例，其中取得的公司的 ad 樹系與父公司的 ad 樹系隔離。

若要深入瞭解 Azure AD Connect 同步與 Azure AD Connect 雲端布建之間的差異，請參閱[Azure AD Connect 雲端](../cloud-provisioning/what-is-cloud-provisioning.md)布建？

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。