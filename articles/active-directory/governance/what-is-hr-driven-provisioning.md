---
title: 何謂使用 Azure Active Directory 進行 HR 驅動佈建？ | Microsoft Docs
description: 說明 HR 驅動佈建的概觀。
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
ms.openlocfilehash: 45b8ed75a87d1ac638e5ca55058a3d1ad7fcdffa
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134904"
---
# <a name="what-is-hr-driven-provisioning"></a>什麼是 HR 驅動佈建？

![HR 佈建](./media/what-is-hr-driven-provisioning/cloud2a.png)

HR 驅動佈建是根據人力資源系統建立數位身分識別的程序。  HR 系統會成為這些新建數位身分識別的授權啟動，而且通常是許多佈建程序的起點。  例如，如果新員工加入貴公司，則會在人力資源系統中予以建立。  建立時會觸發將使用者帳戶佈建到 Active Directory，然後 Azure AD Connect 會將此帳戶佈建到 Azure AD 等等。

HR 驅動佈建可以內部部署或雲端為基礎。

## <a name="on-premises-based-hr-provisioning"></a>以內部部署為基礎的 HR 佈建
以內部部署為基礎的 HR 佈建是藉由使用本機 HR 系統和佈建新數位身分識別的方法來完成。

HR 系統附有各種套件、軟體組合，並可能使用 SQL 伺服器、LDAP 目錄等等。

目前，在這些 HR 系統中建立新的身分識別時，Microsoft 內部部署 HR 佈建解決方案會使用 Microsoft Identity Manager 來觸發佈建。

使用 MIM，您可以將來自內部部署 HR 系統的使用者佈建到 Active Directory 或 Azure AD。

如需 Microsoft Identity Manager 及其支援系統的詳細資訊，請參閱 [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) 文件。

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>後續步驟 
- [什麼是身分識別生命週期管理？](what-is-identity-lifecycle-management.md)
- [什麼是佈建？](what-is-provisioning.md)
- [什麼是應用程式佈建？](what-is-app-provisioning.md)
- [什麼是目錄間的佈建？](what-is-inter-directory-provisioning.md)
- [什麼是目錄佈建？](what-is-inter-directory-provisioning.md)