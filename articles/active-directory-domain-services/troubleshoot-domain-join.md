---
title: 使用 Azure AD Domain Services 進行網域聯結的疑難排解 |Microsoft Docs
description: 瞭解如何在嘗試將 VM 加入網域或將應用程式連線到 Azure Active Directory Domain Services 時，針對常見問題進行疑難排解，而且您無法連線到受控網域或進行驗證。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299103"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>針對 Azure AD Domain Services 受控網域的網域聯結問題進行疑難排解

當您嘗試加入虛擬機器（VM）或將應用程式連線到 Azure Active Directory Domain Services （AD DS）受控網域時，可能會收到無法執行此動作的錯誤。 若要針對網域聯結問題進行疑難排解，請參閱下列哪一個問題：

* 如果您未收到驗證提示，則 VM 或應用程式無法連線到 Azure AD DS 受控網域。
    * 開始[針對加入網域](#connectivity-issues-for-domain-join)的連線問題進行疑難排解。
* 如果您在驗證期間收到錯誤，Azure AD DS 受控網域的連線就會成功。
    * 開始針對[加入網域時的認證相關問題](#credentials-related-issues-during-domain-join)進行疑難排解。

## <a name="connectivity-issues-for-domain-join"></a>加入網域的連線問題

如果 VM 找不到 Azure AD DS 受控網域，通常會有網路連線或設定問題。 請參閱下列疑難排解步驟，以找出並解決問題：

1. 請確定 VM 已連線至已啟用 Azure AD DS 的相同或對等互連虛擬網路。 如果沒有，VM 就找不到並聯機到網域，以便加入。
    * 如果 VM 未連線到相同的虛擬網路，請確認虛擬網路對等互連或 VPN 連線為*作用中或**已連接*，以允許流量正確流動。
1. 嘗試使用 Azure AD DS 受控網域的功能變數名稱（例如） ping 網域`ping aaddscontoso.com`。
    * 如果 ping 回應失敗，請嘗試在入口網站中，針對您的 Azure AD DS 受控網域（例如），偵測所顯示網域的 IP 位址`ping 10.0.0.4`。
    * 如果您可以順利 ping IP 位址，而不是網域，則 DNS 可能設定不正確。 請確定您已設定虛擬網路的 Azure AD DS 受控網域 DNS 伺服器。
1. 請嘗試排清虛擬機器上的 DNS 解析程式快取`ipconfig /flushdns`，例如。

### <a name="network-security-group-nsg-configuration"></a>網路安全性群組（NSG）設定

當您建立 Azure AD DS 受控網域時，也會建立一個網路安全性群組，其中包含成功網域操作的適當規則。 如果您編輯或建立額外的網路安全性群組規則，您可能會不小心封鎖 Azure AD DS 提供連線和驗證服務所需的埠。 這些網路安全性群組規則可能會導致密碼同步無法完成、使用者無法登入或加入網域的問題等問題。

如果您持續遇到連線問題，請參閱下列疑難排解步驟：

1. 檢查 Azure 入口網站中 Azure AD DS 受控網域的健全狀況狀態。 如果您有*AADDS001*的警示，網路安全性群組規則會封鎖存取。
1. 檢查[必要的埠和網路安全性群組規則][network-ports]。 請確定沒有任何網路安全性群組規則套用至您要連線的 VM 或虛擬網路，請封鎖這些網路埠。
1. 一旦解決任何網路安全性群組設定問題， *AADDS001*警示就會在大約2小時內從健全狀況頁面中消失。 現在已可使用網路連線，請嘗試再次將 VM 加入網域。

## <a name="credentials-related-issues-during-domain-join"></a>加入網域時的認證相關問題

如果您收到要求認證以加入 Azure AD DS 受控網域的對話方塊，VM 就能夠使用 Azure 虛擬網路連線到網域。 網域聯結程式在向網域進行驗證時失敗，或使用認證提供的授權完成網域加入程式。

若要針對認證相關的問題進行疑難排解，請參閱下列疑難排解步驟：

1. 嘗試使用 UPN 格式來指定認證，例如 `dee@aaddscontoso.onmicrosoft.com`。 請確定已在 Azure AD 中正確設定此 UPN。
    * 如果您的租使用者中有多個使用者具有相同的 UPN 前置詞，或您的 UPN 前置詞太長，您帳戶的*SAMAccountName*可能會自動產生。 因此，您帳戶的*SAMAccountName*格式可能會與您在內部部署網域中預期或使用的格式不同。
1. 嘗試將認證用於屬於 Azure AD DS 受控網域的使用者帳戶，以將 Vm 加入受控網域。
1. 請確定您已[啟用密碼同步][enable-password-sync]處理，並等候足夠的時間來完成初始密碼同步。

## <a name="next-steps"></a>後續步驟

若要更深入瞭解做為加入網域作業的 Active Directory 進程，請參閱[聯結和驗證問題][join-authentication-issues]。

如果您仍有將 VM 加入 Azure AD DS 受控網域的問題，請尋找 [說明]，[並開啟 Azure Active Directory 的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
