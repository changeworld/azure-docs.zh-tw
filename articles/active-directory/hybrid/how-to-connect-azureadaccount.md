---
title: 變更 Azure AD 連接器帳戶密碼 |Microsoft Docs
description: 本主題說明如何還原 Azure AD 連接器帳戶。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 7f6b623cc5f864106dc2f119308370e80014a4c2
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611019"
---
# <a name="change-the-azure-ad-connector-account-password"></a>變更 Azure AD Connector 帳戶密碼
Azure AD 連接器帳戶應為免費服務。 如果您需要重設其認證，則這個主題適合您。 例如，如果全域系統管理員不小心使用 PowerShell 重設帳戶的密碼。

## <a name="reset-the-credentials"></a>重設認證
如果 Azure AD 連接器帳戶因驗證問題而無法連絡 Azure AD，則可以重設密碼。

1. 登入 Azure AD Connect 同步處理伺服器，並啟動 PowerShell。
2. 執行 `Add-ADSyncAADServiceAccount`。
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. 提供 Azure AD 全域管理員認證。

這個 Cmdlet 會重設服務帳戶的密碼，並在 Azure AD 和同步處理引擎中加以更新。

## <a name="known-issues-these-steps-can-solve"></a>這些步驟可以解決的已知問題
本節是客戶回報的錯誤清單，由 Azure AD 連接器帳戶上的認證重設來修正。

---
事件6900伺服器在處理密碼變更通知時發生未預期的錯誤： AADSTS70002：驗證認證時發生錯誤。 AADSTS50054︰使用舊密碼進行驗證。

---
抓取密碼原則同步設定時發生事件659錯誤。 Microsoft.identitymodel。 AdalServiceException： AADSTS70002：驗證認證時發生錯誤。 AADSTS50054︰使用舊密碼進行驗證。

## <a name="next-steps"></a>後續步驟
**概觀主題**

* [Azure AD Connect 同步處理：了解及自訂同步處理](how-to-connect-sync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)
