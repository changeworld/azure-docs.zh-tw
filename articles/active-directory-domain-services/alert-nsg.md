---
title: 解決 Azure AD DS 中的網路安全性群組警示 |Microsoft Docs
description: 瞭解如何疑難排解和解決 Azure Active Directory Domain Services 的網路安全性群組設定警示
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: 5b48d326efad889adbcf25d487ee27b8200f558f
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693923"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>已知問題： Azure Active Directory Domain Services 中的網路設定警示

為了讓應用程式和服務能夠正確地與 Azure Active Directory Domain Services (Azure AD DS) 受控網域進行通訊，必須開啟特定的網路埠，以允許流量流動。 在 Azure 中，您可以使用網路安全性群組來控制流量的流程。 如果所需的網路安全性群組規則未就緒，則 Azure AD DS 受控網域的健全狀況狀態會顯示警示。

本文可協助您瞭解並解決網路安全性群組設定問題的常見警示。

## <a name="alert-aadds104-network-error"></a>警示 AADDS104：網路錯誤

### <a name="alert-message"></a>警示訊息

*Microsoft 無法觸達此受控網域的網域控制站。如果虛擬網路上設定 (NSG) 的網路安全性群組封鎖對受控網域的存取，就會發生這種情況。另一個可能的原因是使用者定義的路由會封鎖來自網際網路的連入流量。*

網路安全性群組規則無效是 Azure AD DS 發生網路錯誤的最常見原因。 虛擬網路的網路安全性群組必須允許存取特定埠和通訊協定。 如果這些埠遭到封鎖，Azure 平臺就無法監視或更新受控網域。 Azure AD 目錄和 Azure AD DS 之間的同步處理也會受到影響。 請確定您保持開啟預設埠，以避免服務中斷。

## <a name="default-security-rules"></a>預設安全性規則

下列預設輸入和輸出安全性規則會套用至受控網域的網路安全性群組。 這些規則可讓 Azure AD DS 保持安全，並可讓 Azure 平臺監視、管理及更新受控網域。

### <a name="inbound-security-rules"></a>輸入安全性規則

| 優先順序 | 名稱 | 連接埠 | 通訊協定 | 來源 | Destination | 動作 |
|----------|------|------|----------|--------|-------------|--------|
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | 任意 | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | 任意 | 拒絕<sup>1</sup> |
| 65000    | AllVnetInBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | 允許 |
| 65001    | AllowAzureLoadBalancerInBound | 任意 | 任意 | AzureLoadBalancer | 任意 | Allow |
| 65500    | DenyAllInBound | 任意 | 任意 | 任意 | 任意 | 拒絕 |


<sup>1</sup>用於偵錯工具的選擇性。 在需要進行先進的疑難排解時允許。

> [!NOTE]
> 如果您 [設定安全的 LDAP][configure-ldaps]，您可能也會有額外的規則，可允許輸入流量。 這是正確 LDAPS 通訊所需的額外規則。

### <a name="outbound-security-rules"></a>輸出安全性規則

| 優先順序 | 名稱 | 連接埠 | 通訊協定 | 來源 | Destination | 動作 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | 允許 |
| 65001    | AllowAzureLoadBalancerOutBound | 任意 | 任意 |  任意 | 網際網路 | 允許 |
| 65500    | DenyAllOutBound | 任意 | 任意 | 任意 | 任意 | 拒絕 |

>[!NOTE]
> Azure AD DS 需要來自虛擬網路的無限制輸出存取。 我們不建議您建立任何額外的規則，以限制虛擬網路的輸出存取。

## <a name="verify-and-edit-existing-security-rules"></a>驗證和編輯現有的安全性規則

若要確認現有的安全性規則，並確定已開啟預設埠，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **網路安全性群組**]。
1. 選擇與您的受控網域相關聯的網路安全性群組，例如 *AADDS-contoso.com-NSG*。
1. 在 [ **總覽** ] 頁面上會顯示現有的輸入和輸出安全性規則。

    查看輸入和輸出規則，並與上一節中的必要規則清單進行比較。 如有需要，請選取並刪除任何會封鎖所需流量的自訂規則。 如果遺漏任何必要的規則，請在下一節中新增規則。

    在您新增或刪除規則以允許所需的流量之後，受控網域的健康情況會在兩小時內自動更新，並移除警示。

### <a name="add-a-security-rule"></a>新增安全性規則

若要新增遺失的安全性規則，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **網路安全性群組**]。
1. 選擇與您的受控網域相關聯的網路安全性群組，例如 *AADDS-contoso.com-NSG*。
1. 在左側面板的 [ **設定** ] 底下，按一下 [ *輸入安全性規則* ] 或 [ *輸出安全性規則* ]，視您需要新增的規則而定。
1. 選取 [ **新增**]，然後根據埠、通訊協定、方向等來建立必要的規則。準備好時，請選取 **[確定]**。

將安全性規則加入並顯示在清單中需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟

如果仍有問題，請[開啟 Azure 支援要求][azure-support]以取得其他疑難排解協助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
