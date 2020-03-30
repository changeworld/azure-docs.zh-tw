---
title: 修復動態組成員身份的問題 - Azure AD |微軟文檔
description: Azure 活動目錄中動態組成員身份的故障排除提示
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026550"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>排除和解決組問題

## <a name="troubleshooting-group-creation-issues"></a>解決組創建問題

**我禁用了 Azure 門戶中的安全性群組創建，但仍可以通過 Powershell 創建組****使用者可以在 Azure 門戶中的 Azure 門戶設置中創建安全性群組**，以控制非管理員使用者是否可以在 Access 面板或 Azure 門戶中創建安全性群組。 它不通過 Powershell 控制安全性群組創建。

要禁用 Powershell 中非管理員使用者的組創建：
1. 請確認允許非管理使用者建立群組：
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. 如果它傳回 `UsersPermissionToCreateGroupsEnabled : True`，非管理使用者就可以建立群組。 若要停用這項功能：
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**嘗試在 Powershell 中創建動態組時，我收到允許的最大組錯誤**<br/>
如果在 Powershell 中收到一條消息，指示_動態群組原則已達到最大允許的組計數_，這意味著已達到租戶中動態組的最大限制。 每個租戶的動態組的最大數量為 5，000。

要創建任何新的動態組，首先需要刪除某些現有動態組。 沒有辦法增加限制。

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>疑難排解群組的動態成員資格

**我針對某個群組設定了一個規則，但是群組中的成員資格沒有任何更新**<br/>
1. 驗證規則中使用者或裝置屬性的值。 確保有使用者滿足規則。 對於設備，請檢查裝置屬性以確保任何同步的屬性包含預期值。<br/>
2. 檢查成員資格處理狀態以確認是否已完成。 您可以在"**概述"** 頁上檢查[成員資格處理狀態](groups-create-rule.md#check-processing-status-for-a-rule)和上次更新日期。

如果一切良好，請等候一段時間，以填入群組。 根據您的租用戶大小，群組可能最多需要 24 小時來完成初次填入或規則變更。

**我已設定一個規則，但是目前現有的規則成員已被移除**<br/>這是預期行為。 因為在啟用或變更規則時，現有群組的成員會遭到移除。 從評估規則所傳回的使用者會當作成員，新增至群組。

**當我新增或變更規則時，沒有立即看到成員資格變更，為什麼？**<br/>專用的成員資格評估會定期在非同步的背景處理序中完成。 該程序所需的時間，取決於您目錄中的使用者數目以及因規則所建立的群組大小。 具有少量使用者的目錄通常會在很短的時間內看到群組成員資格變更。 具有大量使用者的目錄則可能需要 30 分鐘或更長的時間填入。

**如何強制立即處理組？**<br/>
目前，無法自動觸發要按需處理的組。 但是，您可以通過更新成員資格規則來在末尾添加空格來手動觸發後處理。  

**我遇到規則處理錯誤**<br/>下表列出常見動態成員資格規則錯誤及其更正方式。

| 規則剖析器錯誤 | 錯誤的使用方式 | 更正的使用方式 |
| --- | --- | --- |
| 錯誤：不支援屬性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>請確定此屬性位於[支援的屬性清單](groups-dynamic-membership.md#supported-properties)中。 |
| 錯誤：屬性不支援運算子。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>屬性類型不支援使用的運算子 (在此範例中，-contains 不能在布林類型上使用)。 屬為屬性類型使用正確的運算子。 |
| 錯誤：查詢編譯錯誤。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. 缺少操作員。 使用這兩個 -and 或 -or 聯結述詞<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. 正則運算式中的錯誤與 -匹配一起使用<br>(user.userPrincipalName -match ".*@domain.ext")<br>或者：(user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>後續步驟

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [什麼是 Azure 活動目錄？](../fundamentals/active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)