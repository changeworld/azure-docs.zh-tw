---
title: Azure AD Connect 雲端布建代理程式：管理登錄選項 |Microsoft Docs
description: 本文說明如何在 Azure AD Connect 雲端布建代理程式中管理登錄選項。
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371086"
---
# <a name="manage-agent-registry-options"></a>管理代理程式登錄選項

本節說明您可以設定的登錄選項，以控制 Azure AD Connect 布建代理程式的執行時間處理行為。 

## <a name="configure-ldap-connection-timeout"></a>設定 LDAP 連接逾時
在設定的 Active Directory 網域控制站上執行 LDAP 作業時，布建代理程式預設會使用預設連接逾時值30秒。 如果您的網域控制站需要更多時間來回應，則您可能會在代理程式記錄檔中看到下列錯誤訊息： 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

如果搜尋屬性未建立索引，LDAP 搜尋作業可能需要較長的時間。 第一個步驟是，如果您收到上述錯誤，請先檢查搜尋/查閱屬性是否已 [編制索引](https://docs.microsoft.com/windows/win32/ad/indexed-attributes)。 如果搜尋屬性已編制索引，但錯誤持續發生，您可以使用下列步驟來增加 LDAP 連接逾時： 

1. 在執行 Azure AD Connect 布建代理程式的 Windows server 上，以系統管理員身分登入。
1. 使用 [ *執行* ] 功能表項目開啟 [登錄編輯程式] ( # A0)  
1. 找出金鑰資料夾 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. 以滑鼠右鍵按一下並選取 [新增-> 字串值]
1. 提供名稱： `LdapConnectionTimeoutInMilliseconds`
1. 按兩下 **值名稱** ，並以毫秒為單位輸入值資料 `60000` 。
    > [!div class="mx-imgBorder"]
    > ![LDAP 連接逾時](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. 從 [ *服務* ] 主控台重新開機 Azure AD Connect 布建服務。
1. 如果您已部署多個布建代理程式，請將此登錄變更套用至所有代理程式的一致性。 

## <a name="configure-referral-chasing"></a>設定參考追蹤
根據預設，Azure AD Connect 布建代理程式不會執行 [參考](https://docs.microsoft.com/windows/win32/ad/referrals)。 您可能會想要啟用參考追蹤，以支援特定的 HR 輸入布建案例，例如： 
* 檢查跨多個網域的 UPN 唯一性
* 解析跨網域管理員參考

使用下列步驟來開啟參考追蹤：

1. 在執行 Azure AD Connect 布建代理程式的 Windows server 上，以系統管理員身分登入。
1. 使用 [ *執行* ] 功能表項目開啟 [登錄編輯程式] ( # A0)  
1. 找出金鑰資料夾 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. 以滑鼠右鍵按一下並選取 [新增-> 字串值]
1. 提供名稱： `ReferralChasingOptions`
1. 按兩下 **值名稱** ，然後輸入值資料做為 `96` 。 這個值會對應至的常數值 `ReferralChasingOptions.All` ，並指定子樹和基底層級的參考會接著代理程式。 
    > [!div class="mx-imgBorder"]
    > ![參考追蹤](media/how-to-manage-registry-options/referral-chasing.png)
1. 從 [ *服務* ] 主控台重新開機 Azure AD Connect 布建服務。
1. 如果您已部署多個布建代理程式，請將此登錄變更套用至所有代理程式的一致性。

> [!NOTE]
> 您可以藉由啟用 [詳細資訊記錄](how-to-troubleshoot.md#log-files)來確認已設定登錄選項。 代理程式啟動期間發出的記錄會顯示從登錄中挑選的設定值。 

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)

