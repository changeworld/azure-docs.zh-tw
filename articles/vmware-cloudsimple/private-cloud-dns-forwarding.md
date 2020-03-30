---
title: Azure VMware 解決方案 - 從私有雲轉發到本地的 DNS
description: 描述如何使雲簡單私有雲 DNS 伺服器轉發本地資源
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961123"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>啟用雲簡單私有雲 DNS 伺服器，將本地資源的 DNS 查找轉發到 DNS 伺服器

私有雲 DNS 伺服器可以將任何本地資源的 DNS 查找轉發到您的 DNS 伺服器。  啟用查找允許私有雲 vSphere 元件查找在本地環境中運行的任何服務，並使用完全限定的功能變數名稱 （FQDN） 與其通信。

## <a name="scenarios"></a>案例 

通過轉發本地 DNS 伺服器的 DNS 查詢，您可以針對以下方案使用私有雲：

* 將私有雲用作本地 VMware 解決方案的災害復原設置
* 將本地活動目錄用作私有雲 vSphere 的身份源
* 使用 HCX 將虛擬機器從本地遷移到私有雲

## <a name="before-you-begin"></a>開始之前

從私有雲網路到本地網路，必須存在網路連接，以便 DNS 轉發工作。  您可以使用：

* [使用 ExpressRoute 從本地連接到雲簡單](on-premises-connection.md)
* [佈建網站到網站 VPN 閘道](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

必須在此連接上打開防火牆埠，DNS 轉發才能正常工作。  使用的埠是 TCP 埠 53 或 UDP 埠 53。

> [!NOTE]
> 如果使用網站到網站 VPN，則必須將本地 DNS 伺服器子網添加為本地首碼的一部分。

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>請求 DNS 從私有雲轉發到本地

要啟用 DNS 從私有雲轉發到本地，請提交[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，並提供以下資訊。

* 問題類型：**技術**
* 訂閱：**部署雲簡單服務的訂閱**
* 服務 **：VMware 解決方案（按雲簡單）**
* 問題類型：**諮詢或如何...**
* 問題子類型：**需要使用 NW 的説明**
* 在詳細資訊窗格中提供本地域的功能變數名稱。
* 在詳細資訊窗格中提供本地 DNS 伺服器的清單，其中查詢將從私有雲轉發到該伺服器。

## <a name="next-steps"></a>後續步驟

* [瞭解有關本地防火牆配置的更多資訊](on-premises-firewall-configuration.md)
* [本地 DNS 伺服器配置](on-premises-dns-setup.md)
