---
title: 卸載 Azure AD Connect
description: 本檔說明如何卸載 Azure AD Connect。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934161"
---
# <a name="uninstall-azure-ad-connect"></a>卸載 Azure AD Connect

本檔說明如何正確地卸載 Azure AD Connect。

## <a name="uninstall-azure-ad-connect-from-the-server"></a>從伺服器卸載 Azure AD Connect
您需要做的第一件事，就是從執行它的伺服器中移除 Azure AD Connect。  請使用下列步驟：

 1. 在執行 Azure AD Connect 的伺服器上，流覽至 **主控台**。
 2. 按一下 [**卸載** 程式] 
  ![ 卸載程式](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. 選取 [Azure AD Connect]。
 ![選取 Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. 出現提示時，請按一下 **[是]** 以確認。
 5. 此確認會顯示 Azure AD Connect 畫面。  按一下 [移除]。
 ![移除](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. 完成此動作之後，請 **按一下 [** 結束]。
 7. ![結束](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. 回到 **主控台****按一下 [** 重新整理]，所有的元件應該都已移除。


## <a name="next-steps"></a>後續步驟

- 深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
- [使用現有的 ADSync 資料庫安裝 Azure AD Connect](how-to-connect-install-existing-database.md)
- [使用 SQL 委派的管理員權限安裝 Azure AD Connect](how-to-connect-install-sql-delegation.md)

