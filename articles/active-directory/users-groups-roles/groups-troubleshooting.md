---
title: 修正動態群組成員資格的問題-Azure AD |Microsoft Docs
description: Azure Active Directory 中動態群組成員資格的疑難排解秘訣
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8730ac8aa6a6056db67613f2ac8decf11740c467
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84727682"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>針對群組問題進行疑難排解並加以解決

## <a name="troubleshooting-group-creation-issues"></a>針對群組建立問題進行疑難排解

**我已在 Azure 入口網站中停用安全性群組建立，但仍可透過 Powershell 建立群組** [ **使用者可以在 Azure 入口網站中建立安全性群組** ] 設定中的 [Azure 入口網站控制非系統管理員的使用者是否可以在存取面板或 Azure 入口網站中建立安全性群組。 它不會透過 Powershell 控制安全性群組建立。

若要在 Powershell 中停用非系統管理員使用者的群組建立：
1. 請確認允許非管理使用者建立群組：
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. 如果它傳回 `UsersPermissionToCreateGroupsEnabled : True`，非管理使用者就可以建立群組。 若要停用這項功能：
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**我在嘗試于 Powershell 中建立動態群組時收到允許的最大群組錯誤**<br/>
如果您在 Powershell 中收到訊息，指出已 _達到動態群組原則允許的群組計數上限_，這表示您已達到組織中動態群組的最大限制。 每個組織的動態群組數目上限為5000。

若要建立任何新的動態群組，您必須先刪除一些現有的動態群組。 沒有任何方法可以提高限制。

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>疑難排解群組的動態成員資格

**我針對某個群組設定了一個規則，但是群組中的成員資格沒有任何更新**<br/>
1. 確認規則中的使用者或裝置屬性值。 確定有符合規則的使用者。 針對裝置，請檢查裝置屬性，以確定任何同步的屬性都包含預期的值。<br/>
2. 檢查成員資格處理狀態以確認是否已完成。 您可以在群組的 [**總覽**] 頁面上檢查[成員資格處理狀態](groups-create-rule.md#check-processing-status-for-a-rule)和上次更新日期。

如果一切良好，請等候一段時間，以填入群組。 視 Azure AD 組織的大小而定，群組最多可能需要24小時的時間，才能首次或在規則變更之後填入。

**我已設定一個規則，但是目前現有的規則成員已被移除**<br/>這是正常的現象。 因為在啟用或變更規則時，現有群組的成員會遭到移除。 從評估規則所傳回的使用者會當作成員，新增至群組。

**當我新增或變更規則時，沒有立即看到成員資格變更，為什麼？**<br/>專用的成員資格評估會定期在非同步的背景處理序中完成。 該程序所需的時間，取決於您目錄中的使用者數目以及因規則所建立的群組大小。 具有少量使用者的目錄通常會在很短的時間內看到群組成員資格變更。 具有大量使用者的目錄則可能需要 30 分鐘或更長的時間填入。

**如何強制立即處理群組？**<br/>
目前沒有任何方法可以自動觸發依需求處理的群組。 不過，您可以藉由更新成員資格規則來手動觸發重新處理，以在結尾加上空白字元。  

**我遇到規則處理錯誤**<br/>下表列出常見動態成員資格規則錯誤及其更正方式。

| 規則剖析器錯誤 | 錯誤的使用方式 | 更正的使用方式 |
| --- | --- | --- |
| 錯誤：不支援屬性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>請確定此屬性位於[支援的屬性清單](groups-dynamic-membership.md#supported-properties)中。 |
| 錯誤：屬性不支援運算子。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>屬性類型不支援使用的運算子 (在此範例中，-contains 不能在布林類型上使用)。 屬為屬性類型使用正確的運算子。 |
| 錯誤：查詢編譯錯誤。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. 遺漏運算子。 使用這兩個 -and 或 -or 聯結述詞<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. 使用 match 的正則運算式發生錯誤<br>(user.userPrincipalName -match ".*@domain.ext")<br>或者：(user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>接下來的步驟

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [什麼是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)