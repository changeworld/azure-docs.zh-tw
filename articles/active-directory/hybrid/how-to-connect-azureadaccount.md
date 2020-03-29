---
title: 更改 Azure AD 連接器帳戶密碼 |微軟文檔
description: 本主題將記錄如何還原 Azure AD 連接器帳戶。
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
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204523"
---
# <a name="change-the-azure-ad-connector-account-password"></a>變更 Azure AD Connector 帳戶密碼
Azure AD 連接器帳戶應免費服務。 如果您需要重設其認證，則這個主題適合您。 例如，如果全域管理員錯誤地使用 PowerShell 重置帳戶上的密碼。

## <a name="reset-the-credentials"></a>重設認證
如果 Azure AD 連接器帳戶由於身份驗證問題無法與 Azure AD 聯繫，則可以重置密碼。

1. 登入 Azure AD Connect 同步處理伺服器，並啟動 PowerShell。
2. 執行 `Add-ADSyncAADServiceAccount`。  
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. 提供 Azure AD 全域管理員認證。

這個 Cmdlet 會重設服務帳戶的密碼，並在 Azure AD 和同步處理引擎中加以更新。

## <a name="known-issues-these-steps-can-solve"></a>這些步驟可以解決的已知問題
本節是客戶報告的錯誤清單，這些錯誤是由 Azure AD 連接器帳戶上的憑據重置修復的。

---
事件 6900  
伺服器在處理密碼變更通知時發生未預期的錯誤︰  
AADSTS70002︰驗證認證時發生錯誤。 AADSTS50054︰使用舊密碼進行驗證。

---
事件 659  
擷取密碼原則同步處理設定時發生錯誤。 Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException：  
AADSTS70002︰驗證認證時發生錯誤。 AADSTS50054︰使用舊密碼進行驗證。

## <a name="next-steps"></a>後續步驟
**概觀主題**

* [Azure AD Connect 同步處理：了解及自訂同步處理](how-to-connect-sync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)

