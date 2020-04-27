---
title: 解決 Azure AD DS 中的網路安全性群組警示 |Microsoft Docs
description: 瞭解如何疑難排解和解決 Azure Active Directory Domain Services 的網路安全性群組設定警示
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "71257995"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>已知問題： Azure Active Directory Domain Services 中的網路設定警示

為了讓應用程式和服務正確地與 Azure Active Directory Domain Services （Azure AD DS）通訊，必須開啟特定的網路埠，以允許流量流動。 在 Azure 中，您可以使用網路安全性群組來控制流量的流程。 如果所需的網路安全性群組規則不存在，則 Azure AD DS 受控網域的健全狀況狀態會顯示警示。

本文可協助您瞭解並解決網路安全性群組設定問題的常見警示。

## <a name="alert-aadds104-network-error"></a>警示 AADDS104：網路錯誤

### <a name="alert-message"></a>警示訊息

*Microsoft 無法觸達此受控網域的網域控制站。如果在虛擬網路上設定的網路安全性群組（NSG）封鎖對受控網域的存取，就可能發生這種情況。另一個可能的原因是使用者定義的路由封鎖了來自網際網路的連入流量。*

網路安全性群組規則無效是 Azure AD DS 的網路錯誤最常見的原因。 虛擬網路的網路安全性群組必須允許存取特定的埠和通訊協定。 如果這些埠遭到封鎖，Azure 平臺就無法監視或更新受控網域。 Azure AD 目錄與 Azure AD DS 受控網域之間的同步處理也會受到影響。 請確定您保持開啟預設埠，以避免服務中斷。

## <a name="default-security-rules"></a>預設安全性規則

下列預設的輸入和輸出安全性規則適用于 Azure AD DS 受控網域的網路安全性群組。 這些規則會讓 Azure AD DS 保持安全，並允許 Azure 平臺監視、管理及更新受控網域。 如果您[設定安全 LDAP][configure-ldaps]，您可能也會有允許輸入流量的額外規則。

### <a name="inbound-security-rules"></a>輸入安全性規則

| 優先順序 | 名稱 | 連接埠 | 通訊協定 | 來源 | Destination | 動作 |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | 任意 | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | 任意 | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | 任意 | Allow |
| 65000    | AllVnetInBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | 任意 | 任意 | AzureLoadBalancer | 任意 | Allow |
| 65500    | DenyAllInBound | 任意 | 任意 | 任意 | 任意 | 拒絕 |

### <a name="outbound-security-rules"></a>輸出安全性規則

| 優先順序 | 名稱 | 連接埠 | 通訊協定 | 來源 | Destination | 動作 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | 任意 | 任意 |  任意 | Internet | Allow |
| 65500    | DenyAllOutBound | 任意 | 任意 | 任意 | 任意 | 拒絕 |

>[!NOTE]
> Azure AD DS 需要從虛擬網路進行不受限制的輸出存取。 我們不建議您建立任何額外的規則，以限制虛擬網路的輸出存取。

## <a name="verify-and-edit-existing-security-rules"></a>驗證和編輯現有的安全性規則

若要確認現有的安全性規則，並確定預設通訊埠已開啟，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [**網路安全性群組**]。
1. 選擇與受控網域相關聯的網路安全性群組，例如*AADDS-contoso.com-NSG*。
1. 在 [**總覽**] 頁面上，會顯示現有的輸入和輸出安全性規則。

    檢查輸入和輸出規則，並與上一節中的必要規則清單進行比較。 如有需要，請選取並刪除任何會封鎖所需流量的自訂規則。 如果遺漏任何必要的規則，請在下一節中新增規則。

    在您新增或刪除規則以允許所需的流量之後，Azure AD DS 受控網域的健康情況會在兩小時內自動更新，並移除警示。

### <a name="add-a-security-rule"></a>新增安全性規則

若要新增遺失的安全性規則，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [**網路安全性群組**]。
1. 選擇與受控網域相關聯的網路安全性群組，例如*AADDS-contoso.com-NSG*。
1. 在左側面板的 [**設定**] 下，按一下 [*輸入安全性規則*] 或 [*輸出安全性規則*]，視您需要新增的規則而定。
1. 選取 [**新增**]，然後根據埠、通訊協定、方向等來建立所需的規則。準備好時，請選取 **[確定]**。

新增安全性規則需要一些時間，並顯示在清單中。

## <a name="next-steps"></a>後續步驟

如果您仍有問題，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
