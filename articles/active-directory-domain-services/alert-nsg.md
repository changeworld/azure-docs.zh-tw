---
title: 在 Azure AD DS 中解決網路安全性群組警報 |微軟文檔
description: 瞭解如何對 Azure 活動目錄域服務的網路安全性群組配置警報進行故障排除和解析
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257995"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>已知問題：Azure 活動目錄域服務中的網路設定警報

要讓應用程式和服務與 Azure 活動目錄域服務 （Azure AD DS） 正確通信，必須打開特定的網路埠以允許流量流動。 在 Azure 中，您可以使用網路安全性群組控制流量。 如果所需的網路安全性群組規則沒有到位，Azure AD DS 託管域的運行狀況將顯示警報。

本文可説明您瞭解和解決網路安全性群組配置問題的常見警報。

## <a name="alert-aadds104-network-error"></a>警示 AADDS104：網路錯誤

### <a name="alert-message"></a>警報消息

*Microsoft 無法訪問此託管域的網域控制站。如果虛擬網路上配置的網路安全性群組 （NSG） 阻止對託管域的訪問，則可能發生此情況。另一個可能的原因是，是否有使用者定義的路由阻止來自 Internet 的傳入流量。*

不正確網路安全性群組規則是 Azure AD DS 網路錯誤的最常見原因。 虛擬網路的網路安全性群組必須允許訪問特定埠和協定。 如果這些埠被阻止，Azure 平臺無法監視或更新託管域。 Azure AD 目錄和 Azure AD DS 託管域之間的同步也會受到影響。 請確保保持預設埠打開狀態，以避免服務中斷。

## <a name="default-security-rules"></a>預設安全性規則

以下預設入站和出站安全規則將應用於 Azure AD DS 託管域的網路安全性群組。 這些規則確保 Azure AD DS 的安全，並允許 Azure 平臺監視、管理和更新託管域。 如果[配置安全 LDAP，][configure-ldaps]您可能還有一條允許入站流量的附加規則。

### <a name="inbound-security-rules"></a>輸入安全性規則

| 優先順序 | 名稱 | 連接埠 | 通訊協定 | 來源 | Destination | 動作 |
|----------|------|------|----------|--------|-------------|--------|
| 101      | 允許同步與 AzureAD | 443 | TCP | Azure ActiveDirectory 域服務 | 任意 | Allow |
| 201      | 允許RD | 3389 | TCP | 公司網路鋸 | 任意 | Allow |
| 301      | 允許PS重新移動 | 5986| TCP | Azure ActiveDirectory 域服務 | 任意 | Allow |
| 65000    | 所有VnetinBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | 任意 | 任意 | AzureLoadBalancer | 任意 | Allow |
| 65500    | DenyAllInBound | 任意 | 任意 | 任意 | 任意 | 拒絕 |

### <a name="outbound-security-rules"></a>輸出安全性規則

| 優先順序 | 名稱 | 連接埠 | 通訊協定 | 來源 | Destination | 動作 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | 所有VnetOut綁定 | 任意 | 任意 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | 允許 Azure 載入平衡器退出綁定 | 任意 | 任意 |  任意 | Internet | Allow |
| 65500    | DenyAllOutBound | 任意 | 任意 | 任意 | 任意 | 拒絕 |

>[!NOTE]
> Azure AD DS 需要從虛擬網路無限制的出站訪問。 我們不建議您創建任何其他規則來限制虛擬網路的出站訪問。

## <a name="verify-and-edit-existing-security-rules"></a>驗證和編輯現有安全規則

要驗證現有安全規則並確保預設埠處於打開狀態，請完成以下步驟：

1. 在 Azure 門戶中，搜索並選擇**網路安全性群組**。
1. 選擇與託管域關聯的網路安全性群組，例如*AADDS-contoso.com-NSG*。
1. 在 **"概述"** 頁上，將顯示現有的入站和出站安全規則。

    查看入站和出站規則，並比較上一節中所需的規則清單。 如果需要，選擇並刪除阻止所需流量的任何自訂規則。 如果缺少任何必需的規則，則在下一節中添加規則。

    添加或刪除規則以允許所需的流量後，Azure AD DS 託管域的運行狀況會在兩小時內自動更新並刪除警報。

### <a name="add-a-security-rule"></a>新增安全性規則

要添加缺少的安全規則，完成以下步驟：

1. 在 Azure 門戶中，搜索並選擇**網路安全性群組**。
1. 選擇與託管域關聯的網路安全性群組，例如*AADDS-contoso.com-NSG*。
1. 在左側面板中的 **"設置"** 下，按一下 *"入站安全規則*"或 *"出站安全規則*"，具體取決於需要添加的規則。
1. 選擇 **"添加**"，然後根據埠、協定、方向等創建所需的規則。準備就緒後，選擇 **"確定**"。

添加安全規則並顯示在清單中需要一些時間。

## <a name="next-steps"></a>後續步驟

如果仍有問題，[請打開 Azure 支援請求][azure-support]以獲取其他故障排除説明。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
