---
title: 使用 Azure AD Domain Services 對加入網域進行疑難排解 |Microsoft Docs
description: 瞭解如何在嘗試將 VM 加入網域，或將應用程式連線到 Azure Active Directory Domain Services，且無法連線或驗證受控網域時，針對常見問題進行疑難排解。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: ee60b684d64ef49fbb669de8c98203e2df5268bf
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967506"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>針對 Azure Active Directory Domain Services 受控網域的網域聯結問題進行疑難排解

當您嘗試將虛擬機器加入 (VM) 或將應用程式連線至 Azure Active Directory Domain Services (Azure AD DS) 受控網域時，您可能會收到錯誤，指出您無法這麼做。 若要針對加入網域問題進行疑難排解，請參閱下列哪一個問題：

* 如果您未收到驗證提示，VM 或應用程式將無法連線到 Azure AD DS 受控網域。
    * 開始 [針對加入網域的連線問題](#connectivity-issues-for-domain-join)進行疑難排解。
* 如果您在驗證期間收到錯誤，則會成功連線到受控網域。
    * 開始對 [加入網域期間的認證相關問題](#credentials-related-issues-during-domain-join)進行疑難排解。

## <a name="connectivity-issues-for-domain-join"></a>網域加入的連線能力問題

如果 VM 找不到受控網域，通常會有網路連線或設定問題。 請參閱下列疑難排解步驟，找出並解決問題：

1. 確定 VM 已連線至與受控網域相同的或對等互連的虛擬網路。 如果沒有，VM 就找不到網域，並連接到網域以加入。
    * 如果 VM 未連線至相同的虛擬網路，請確認虛擬網路對等互連或 VPN 連線為*使用中或**已連線*，以允許流量正常地流動。
1. 請嘗試使用受控網域的功能變數名稱偵測網域，例如 `ping aaddscontoso.com` 。
    * 如果 ping 回應失敗，請嘗試針對您的受控網域（例如），在入口網站中針對 [總覽] 頁面上顯示的網域偵測 IP 位址 `ping 10.0.0.4` 。
    * 如果您可以成功 ping IP 位址，但不能偵測到網域，DNS 可能設定不正確。 請確定您已 [為虛擬網路設定受控網域 DNS 伺服器][configure-dns]。
1. 請嘗試排清虛擬機器上的 DNS 解析程式快取，例如 `ipconfig /flushdns` 。

### <a name="network-security-group-nsg-configuration"></a> (NSG) 設定的網路安全性群組

當您建立受控網域時，也會使用適當的網域作業規則來建立網路安全性群組。 如果您編輯或建立額外的網路安全性群組規則，您可能會不慎封鎖 Azure AD DS 提供連線和驗證服務所需的埠。 這些網路安全性群組規則可能會造成一些問題，例如密碼同步未完成、使用者無法登入，或網域加入問題。

如果您持續發生連線問題，請參閱下列疑難排解步驟：

1. 在 Azure 入口網站中檢查受控網域的健康情況狀態。 如果您有 *AADDS001*的警示，網路安全性群組規則會封鎖存取。
1. 檢查 [必要的埠和網路安全性群組規則][network-ports]。 請確定沒有任何網路安全性群組規則套用至您要連線的 VM 或虛擬網路，而無法封鎖這些網路埠。
1. 一旦解決任何網路安全性群組設定問題，就會在大約2小時內從健康情況頁面中消失 *AADDS001* 警示。 現在可以使用網路連線，請再次嘗試將 VM 加入網域。

## <a name="credentials-related-issues-during-domain-join"></a>網域加入期間的認證相關問題

如果您收到要求認證以加入受控網域的對話方塊，則 VM 可以使用 Azure 虛擬網路連線到網域。 網域加入程式無法驗證網域或授權，而無法使用認證提供的完成網域加入程式。

若要針對認證相關問題進行疑難排解，請參閱下列疑難排解步驟：

1. 嘗試使用 UPN 格式來指定認證，例如 `dee@contoso.onmicrosoft.com`。 請確定已在 Azure AD 中正確設定此 UPN。
    * 如果您的租使用者中有多個使用者具有相同的 UPN 前置詞，或您的 UPN 前置詞太長，您帳戶的 *SAMAccountName* 可能會自動產生。 因此，您帳戶的 *SAMAccountName* 格式可能會與您在內部部署網域中預期或使用的格式不同。
1. 請嘗試使用屬於受控網域的使用者帳號憑證，將 Vm 加入受控網域。
1. 請確定您已 [啟用密碼同步化][enable-password-sync] ，並等待足夠的時間來完成初始密碼同步處理。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Active Directory 的程式做為網域加入作業的一部分，請參閱 [加入和驗證問題][join-authentication-issues]。

如果您在將 VM 加入受控網域時仍有問題，請尋找 [說明] [並開啟 Azure Active Directory 的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
