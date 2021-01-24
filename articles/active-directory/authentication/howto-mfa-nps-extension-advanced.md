---
title: 設定 Azure AD MFA NPS 擴充功能-Azure Active Directory
description: 安裝 NPS 擴充功能之後，請使用下列步驟進行 advanced 設定，例如允許的 IP 清單和 UPN 取代。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695261ceae9d64be9395e6de082f97be04292078
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745980"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Multi-Factor Authentication 之 NPS 延伸模組的進階設定選項

網路原則伺服器 (NPS) 擴充功能會將您的雲端式 Azure AD Multi-Factor Authentication 功能延伸至您的內部部署基礎結構。 本文假設您已經安裝延伸模組，而現在想要知道如何為您的需求自訂延伸模組。

## <a name="alternate-login-id"></a>替代登入識別碼

因為 NPS 延伸模組會連接到您的內部部署和雲端目錄，所以您可能會發生內部部署使用者主體名稱 (UPN) 不符合雲端中名稱的問題。 若要解決此問題，請使用替代登入識別碼。 

在 NPS 擴充功能中，您可以指定要用來取代 Azure AD Multi-Factor Authentication 之 UPN 的 Active Directory 屬性。 這可讓您保護具有雙步驟驗證的內部部署資源，而不需要修改內部部署 UPN。 

若要設定替代登入識別碼，請移至 `HKLM\SOFTWARE\Microsoft\AzureMfa`，然後編輯下列登錄值：

| 名稱 | 類型 | 預設值 | 描述 |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | 字串 | 空白 | 指定您想要使用的 Active Directory 屬性名稱，而非 UPN。 此屬性用作 AlternateLoginId 屬性。 如果此登錄值設定為[有效的 Active Directory 屬性](/windows/win32/adschema/attributes-all) (例如，mail 或 displayName)，則會使用屬性的值來取代使用者的 UPN 以進行驗證。 如果此登錄值是空的或未設定，則會停用 AlternateLoginId，並以使用者的 UPN 進行驗證。 |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | False | 使用此旗標，可在查閱 AlternateLoginId 時強制使用通用類別目錄進行 LDAP 搜尋。 將網域控制站設定為通用類別目錄，並將 AlternateLoginId 屬性新增至通用類別目錄，然後啟用此旗標。 <br><br> 如果設定 LDAP_LOOKUP_FORESTS (不是空的)，則 **此旗標會強制執行為 true**，不論登錄設定的值為何。 在此情況下，NPS 延伸模組需要使用每個樹系的 AlternateLoginId 屬性來設定通用類別目錄。 |
| LDAP_LOOKUP_FORESTS | 字串 | 空白 | 提供要搜尋的樹系清單 (以分號分隔)。 例如，*contoso.com;foobar.com*。 如果設定此登錄值，NPS 延伸模組會依列出順序反覆搜尋所有樹系，並傳回第一個成功的 AlternateLoginId 值。 如果未設定此登錄值，AlternateLoginId 查閱會侷限於目前網域。|

若要針對替代登入識別碼問題進行疑難排解，請使用[替代登入識別碼錯誤](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)的建議步驟。

## <a name="ip-exceptions"></a>IP 例外狀況

如果您需要監視伺服器可用性 (例如，如果負載平衡器確認哪些伺服器在傳送工作負載之前執行)，則不會想要驗證要求封鎖這些檢查。 相反地，建立一份您知道服務帳戶所使用的 IP 位址清單，並停用該清單的 Multi-Factor Authentication 需求。

若要設定 IP 允許清單，請移至 `HKLM\SOFTWARE\Microsoft\AzureMfa` 並設定下列登錄值：

| 名稱 | 類型 | 預設值 | 描述 |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | 字串 | 空白 | 提供 IP 位址清單 (以分號分隔)。 包含產生服務要求之機器的 IP 位址，例如 NAS/VPN 伺服器。 不支援 IP 範圍和子網。 <br><br> 例如，*10.0.0.1;10.0.0.2;10.0.0.3*。

> [!NOTE]
> 安裝程式預設不會建立此登錄機碼，而且在重新開機服務時，AuthZOptCh 記錄中會出現錯誤。 您可以忽略記錄檔中的這個錯誤，但如果建立此登錄機碼，並在不需要時保留空白，則不會傳回錯誤訊息。

從存在的 IP 位址傳入要求時 `IP_WHITELIST` ，會略過雙步驟驗證。 IP 清單會與 RADIUS 要求的 *傳入沒有 ratnasipaddress* 屬性中所提供的 ip 位址進行比較。 如果在沒有傳入沒有 ratnasipaddress 屬性的情況下傳入 RADIUS 要求，則會記錄警告：「因為 RADIUS 要求 NasIpAddress 屬性中遺失來源 IP，所以會忽略「IP_WHITE_LIST_WARNING：： IP 白名單」。

## <a name="next-steps"></a>後續步驟

[針對 Azure AD Multi-Factor Authentication 解決 NPS 擴充功能的錯誤訊息](howto-mfa-nps-extension-errors.md)