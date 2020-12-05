---
title: 解決 Azure AD Domain Services 中的安全 LDAP 警示 |Microsoft Docs
description: 瞭解如何使用安全 LDAP 進行 Azure Active Directory Domain Services 的一般警示進行疑難排解和解決。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620302"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>已知問題：安全 LDAP Azure Active Directory Domain Services 中的警示

使用輕量型目錄存取協定 (LDAP) 的應用程式和服務，可以 [設定為使用安全](tutorial-configure-ldaps.md)AZURE ACTIVE DIRECTORY DOMAIN SERVICES 的 ldap， (Azure AD DS) 。 必須開啟適當的憑證和必要的網路埠，安全的 LDAP 才能正確運作。

本文可協助您瞭解和解決 Azure AD DS 中安全 LDAP 存取的常見警示。

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101：安全 LDAP 網路設定

### <a name="alert-message"></a>警示訊息

*受控網域已啟用透過網際網路的安全 LDAP。不過，使用網路安全性群組並不會鎖定埠636的存取。這可能會將受控網域上的使用者帳戶公開給密碼暴力密碼破解攻擊。*

### <a name="resolution"></a>解決方法

當您啟用安全 LDAP 時，建議您建立其他規則，以限制對特定 IP 位址的輸入 LDAPS 存取。 這些規則會保護受控網域免于暴力密碼破解攻擊。 若要更新網路安全性群組以限制安全 LDAP 的 TCP 埠636存取，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **網路安全性群組**]。
1. 選擇與您的受控網域相關聯的網路安全性群組，例如 *AADDS-contoso.com-NSG*，然後選取 [**輸入安全性規則**]
1. 選取 [ **+ 新增** ] 以建立 TCP 通訊埠636的規則。 如有需要，請在視窗中選取 [ **Advanced** ] 以建立規則。
1. 針對 [ **來源**]，從下拉式功能表中選擇 [ *IP 位址* ]。 輸入您想要授與安全 LDAP 流量存取權的來源 IP 位址。
1. 選擇 [ *任何* ] 作為 **目的地**，然後輸入 *636* 作為 **目的地埠範圍**。
1. 將 **通訊協定** 設定 *為 TCP* ，並將 **動作** 設定為 [ *允許*]。
1. 指定規則的優先順序，然後輸入 *RestrictLDAPS* 之類的名稱。
1. 準備好時，請選取 [ **新增** ] 以建立規則。

受控網域的健康情況會在兩小時內自動更新，並移除警示。

> [!TIP]
> TCP 埠636不是 Azure AD DS 順利執行所需的唯一規則。 若要深入瞭解，請參閱 [AZURE AD DS 網路安全性群組和必要的埠](network-considerations.md#network-security-groups-and-required-ports)。

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 憑證過期

### <a name="alert-message"></a>警示訊息

*受控網域的安全 LDAP 憑證將於 [date] 到期。*

### <a name="resolution"></a>解決方法

遵循 [建立安全 ldap 憑證](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)的步驟，建立取代的安全 ldap 憑證。 將替代憑證套用至 Azure AD DS，然後將憑證發佈至使用安全 LDAP 連接的任何用戶端。

## <a name="next-steps"></a>後續步驟

如果仍有問題，請[開啟 Azure 支援要求][azure-support]以取得其他疑難排解協助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
