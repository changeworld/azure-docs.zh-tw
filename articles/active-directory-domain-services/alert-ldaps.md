---
title: 解決 Azure AD Domain Services 中的安全 LDAP 警示 |Microsoft Docs
description: 瞭解如何使用 Azure Active Directory Domain Services 的安全 LDAP 來疑難排解和解決常見的警示。
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
ms.openlocfilehash: 68798cf98bf01697e5d854f5b539c1c381642c3c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735025"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>已知問題： Azure Active Directory Domain Services 中的安全 LDAP 警示

使用輕量型目錄存取協定（LDAP）與 Azure Active Directory Domain Services （Azure AD DS）通訊的應用程式和服務，可以[設定為使用安全 LDAP](tutorial-configure-ldaps.md)。 必須開啟適當的憑證和必要的網路埠，安全的 LDAP 才能正常運作。

本文可協助您瞭解並使用 Azure AD DS 中的安全 LDAP 存取來解決常見的警示。

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101：安全 LDAP 網路設定

### <a name="alert-message"></a>警示訊息

*受控網域已啟用透過網際網路的安全 LDAP。不過，無法使用網路安全性群組來鎖定埠636的存取。這可能會讓受控網域上的使用者帳戶暴露于暴力密碼破解攻擊。*

### <a name="resolution"></a>解決方案

當您啟用安全 LDAP 時，建議您建立其他規則，以限制對特定 IP 位址的輸入 LDAPS 存取。 這些規則會保護受控網域免于暴力密碼破解攻擊。 若要更新網路安全性群組，以限制安全 LDAP 的 TCP 埠636存取，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [**網路安全性群組**]。
1. 選擇與受控網域相關聯的網路安全性群組，例如*AADDS-contoso.com-NSG*，然後選取 [**輸入安全性規則**]
1. **+ 新增**TCP 通訊埠636的規則。 如有需要，請在視窗中選取 [ **Advanced** ] 來建立規則。
1. 針對 [**來源**]，從下拉式功能表中選擇 [ *IP 位址*]。 輸入您想要為安全 LDAP 流量授與存取權的來源 IP 位址。
1. 選擇 [*任何*] 作為**目的地**，然後針對 [**目的地埠範圍**] 輸入*636* 。
1. 將**通訊協定**設定*為 TCP* ，並將**動作**設為 [*允許*]。
1. 指定規則的優先順序，然後輸入名稱，例如*RestrictLDAPS*。
1. 準備好時，請選取 [**新增**] 來建立規則。

受控網域的健康狀態會在兩小時內自動更新，並移除警示。

> [!TIP]
> TCP 通訊埠636不是 Azure AD DS 順利執行所需的唯一規則。 若要深入瞭解，請參閱[AZURE AD DS 網路安全性群組和所需的埠](network-considerations.md#network-security-groups-and-required-ports)。

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 憑證過期

### <a name="alert-message"></a>警示訊息

*受控網域的安全 LDAP 憑證將於 [date] 到期。*

### <a name="resolution"></a>解決方案

遵循[建立安全 ldap 憑證的](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)步驟，以建立取代的安全 ldap 憑證。 將取代憑證套用至 Azure AD DS，然後將憑證發佈至使用安全 LDAP 連接的任何用戶端。

## <a name="next-steps"></a>後續步驟

如果仍有問題，請[開啟 Azure 支援要求][azure-support]以取得其他疑難排解協助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
