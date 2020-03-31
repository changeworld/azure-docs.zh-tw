---
title: Azure AD 連接：在安裝過程中解決源錨點問題 |微軟文檔
description: 本主題提供了如何在安裝過程中解決源錨點問題的步驟。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114149"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>在安裝過程中解決源錨點問題
本文介紹了安裝過程中可能出現的不同源錨點相關問題，並提供瞭解決這些問題的方法。

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure 活動目錄中的無效源錨點

### <a name="custom-installation"></a>自訂安裝

在自訂安裝期間，Azure AD Connect 從 Azure 活動目錄讀取源錨點策略。 如果策略存在於 Azure 活動目錄中，則 Azure AD Connect 將應用相同的策略，除非客戶覆蓋該策略。 嚮導通知您已讀取哪個屬性。 此外，如果嘗試重寫源錨點策略，嚮導將發出警告。

在此讀取操作期間，Azure 活動目錄中的源錨點策略可能是意外的。 在這種情況下，Azure AD Connect 不知道要使用的源錨點，需要手動重寫。</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

要解決此問題，可以通過選擇特定屬性手動覆蓋源錨點。 僅當並且僅當您確定要選擇哪個屬性時，才繼續使用此選項。 如果您不確定，請聯繫 Microsoft[支援人員](https://support.microsoft.com/contactus/)尋求指導。 如果更改源錨點策略，則可能會中斷本地使用者與其關聯的 Azure 資源之間的關聯。</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>快速安裝
在快速安裝期間，Azure AD 連接從 Azure 活動目錄讀取源錨點策略。 如果該策略存在於 Azure 活動目錄中，則 Azure AD 連接將應用相同的策略。 沒有執行手動覆蓋的選項。

在此讀取操作期間，Azure 活動目錄中的源錨點策略可能是意外的。 在這種情況下，Azure AD 連接不知道源錨點應該是什麼。</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

要解決此問題，您需要使用自訂模式重新安裝，並通過選擇特定屬性手動覆蓋源錨點。 僅當並且僅當您確定要選擇哪個屬性時，才繼續使用此選項。 如果您不確定，請聯繫 Microsoft[支援人員](https://support.microsoft.com/contactus/)尋求指導。 如果更改源錨點策略，則可能會中斷本地使用者與其關聯的 Azure 資源之間的關聯。

### <a name="invalid-source-anchor-in-sync-engine"></a>同步引擎中的無效源錨點
在安裝過程中，Azure AD Connect 可能會嘗試使用不正確源錨點配置同步引擎。 此操作很可能是產品問題，Azure AD 連接的安裝將失敗。 如果您遇到此問題，請與[Microsoft 支援](https://support.microsoft.com/contactus/)部門聯繫。</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。