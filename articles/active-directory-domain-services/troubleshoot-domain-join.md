---
title: 使用 Azure AD 域服務排除域聯接故障 |微軟文檔
description: 瞭解如何在嘗試域加入 VM 或將應用程式連接到 Azure 活動目錄域服務且無法連接到託管域或進行身份驗證時解決常見問題。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299103"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>解決 Azure AD 域服務託管域的域聯接問題

當您嘗試加入虛擬機器 （VM） 或將應用程式連接到 Azure 活動目錄域服務 （AD DS） 託管域時，可能會收到無法執行此操作的錯誤。 要解決域加入問題，請查看以下哪些點存在問題：

* 如果未收到身份驗證提示，則 VM 或應用程式無法連接到 Azure AD DS 託管域。
    * 開始解決[域聯接的連接問題](#connectivity-issues-for-domain-join)。
* 如果在身份驗證期間收到錯誤，則與 Azure AD DS 託管域的連接將成功。
    * 開始在[域加入期間解決與憑據相關的問題](#credentials-related-issues-during-domain-join)。

## <a name="connectivity-issues-for-domain-join"></a>域加入的連接問題

如果 VM 找不到 Azure AD DS 託管域，則通常存在網路連接或配置問題。 查看以下故障排除步驟以查找並解決問題：

1. 確保 VM 連接到為 Azure AD DS 啟用的相同虛擬網路或對等虛擬網路。 如果沒有，VM 無法查找並連接到域才能加入。
    * 如果 VM 未連接到同一虛擬網路，請確認虛擬網路對等*互連或*VPN 連接處於活動狀態或*已連接*，以允許流量正確流動。
1. 嘗試使用 Azure AD DS 託管域的功能變數名稱（如`ping aaddscontoso.com`） 對域進行 ping。
    * 如果 ping 回應失敗，請嘗試 ping Azure AD DS 託管域門戶概覽頁上顯示的域的 IP 位址，例如`ping 10.0.0.4`。
    * 如果可以成功 ping IP 位址，但不能 ping 域，則 DNS 可能配置不正確。 確保已為虛擬網路配置 Azure AD DS 託管域 DNS 伺服器。
1. 嘗試刷新虛擬機器上的 DNS 解析器緩存，例如`ipconfig /flushdns`。

### <a name="network-security-group-nsg-configuration"></a>網路安全性群組 （NSG） 配置

創建 Azure AD DS 託管域時，還會使用成功的域操作的適當規則創建網路安全性群組。 如果編輯或創建其他網路安全性群組規則，可能會無意中阻止 Azure AD DS 提供連接和身份驗證服務所需的埠。 這些網路安全性群組規則可能會導致問題，如密碼同步未完成、使用者無法登錄或域加入問題。

如果繼續出現連接問題，請查看以下故障排除步驟：

1. 檢查 Azure 門戶中的 Azure AD DS 託管域的運行狀況狀態。 如果您有*AADDS001*的警報，則網路安全性群組規則將阻止訪問。
1. 查看[所需的埠和網路安全性群組規則][network-ports]。 確保沒有將網路安全性群組規則應用於從阻止這些網路埠連接的 VM 或虛擬網路。
1. 解決任何網路安全性群組配置問題後 *，AADDS001*警報大約在 2 小時內從運行狀況頁中消失。 現在可用的網路連接後，請嘗試再次加入 VM。

## <a name="credentials-related-issues-during-domain-join"></a>域加入期間與憑據相關的問題

如果得到一個對話方塊，該對話方塊要求憑據加入 Azure AD DS 託管域，則 VM 能夠使用 Azure 虛擬網路連接到域。 域聯接進程在驗證域或授權以使用憑據完成域加入過程時失敗。

要排除與憑據相關的問題，請查看以下故障排除步驟：

1. 嘗試使用 UPN 格式來指定認證，例如 `dee@aaddscontoso.onmicrosoft.com`。 確保此 UPN 在 Azure AD 中配置正確。
    * 如果租戶中有多個具有相同 UPN 首碼的使用者，或者您的 UPN 首碼過長，則帳戶的*SAMAccountName*可能會自動生成。 因此，您帳戶的*SAMAccountName*格式可能與您期望或在本地域中使用的內容不同。
1. 嘗試對屬於 Azure AD DS 託管域的使用者帳戶使用憑據，將 VM 加入託管域。
1. 請確保您[已啟用密碼同步][enable-password-sync]，並等待初始密碼同步完成足夠長的時間。

## <a name="next-steps"></a>後續步驟

有關作為域聯接操作一部分的活動目錄進程的深刻理解，請參閱[加入和身份驗證問題][join-authentication-issues]。

如果仍難以將 VM 加入 Azure AD DS 託管域，[請查找説明並打開 Azure 活動目錄的支援票證][azure-ad-support]。

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
