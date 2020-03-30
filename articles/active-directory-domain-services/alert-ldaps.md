---
title: 解決 Azure AD 域服務中的安全 LDAP 警報 |微軟文檔
description: 瞭解如何使用 Azure 活動目錄域服務的安全 LDAP 排除和解決常見警報。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258040"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>已知問題：Azure 活動目錄域服務中的安全 LDAP 警報

使用羽量級目錄訪問協定 （LDAP） 與 Azure 活動目錄域服務 （Azure AD DS） 通信的應用程式和服務可以[配置為使用安全的 LDAP](tutorial-configure-ldaps.md)。 必須打開適當的證書和所需的網路埠，才能安全 LDAP 正常工作。

本文可説明您瞭解和解決在 Azure AD DS 中具有安全 LDAP 存取權限的常見警報。

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101：安全 LDAP 網路設定

### <a name="alert-message"></a>警報消息

*為託管域啟用了 Internet 上的安全 LDAP。但是，不會使用網路安全性群組鎖定對埠 636 的訪問。這可能將託管域上的使用者帳戶暴露為密碼暴力攻擊。*

### <a name="resolution"></a>解決方案

啟用安全 LDAP 時，建議創建其他規則，限制對特定 IP 位址的入站 LDAPS 訪問。 這些規則保護 Azure AD DS 託管域免受暴力攻擊。 要更新網路安全性群組以限制 TCP 埠 636 訪問的安全 LDAP，請完成以下步驟：

1. 在 Azure 門戶中，搜索並選擇**網路安全性群組**。
1. 選擇與託管域關聯的網路安全性群組，例如*AADDS-contoso.com-NSG，* 然後選擇**入站安全規則**
1. **• 為**TCP 埠 636 添加規則。 如果需要，請在視窗中選擇 **"高級"** 以創建規則。
1. 對於**源**，從下拉式功能表*中選擇 IP 位址*。 輸入要授予安全 LDAP 流量存取權限的源 IP 位址。
1. 選擇 *"任意"* 作為**目標**，然後輸入*636*表示**目標埠範圍**。
1. 將**協定**設置為*TCP* *和允許***的操作**。
1. 指定規則的優先順序，然後輸入名稱，如*限制LDAPS*。
1. 準備就緒後，選擇 **"添加"** 以創建規則。

Azure AD DS 託管域的運行狀況會在兩小時內自動更新自身並刪除警報。

> [!TIP]
> TCP 埠 636 不是 Azure AD DS 平穩運行所需的唯一規則。 要瞭解更多資訊，請參閱[Azure AD DS 網路安全性群組和所需埠](network-considerations.md#network-security-groups-and-required-ports)。

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 憑證過期

### <a name="alert-message"></a>警報消息

*受控網域的安全 LDAP 憑證將於 [date] 到期。*

### <a name="resolution"></a>解決方案

通過按照為[安全 LDAP 創建證書](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)的步驟創建替換安全 LDAP 證書。 將替換證書應用於 Azure AD DS，並將證書分發到使用安全 LDAP 連接的任何用戶端。

## <a name="next-steps"></a>後續步驟

如果仍有問題，[請打開 Azure 支援請求][azure-support]以獲取其他故障排除説明。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
