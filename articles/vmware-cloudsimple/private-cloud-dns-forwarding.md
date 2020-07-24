---
title: Azure VMware 解決方案-從私人雲端轉送至內部部署的 DNS
description: 說明如何讓您的 CloudSimple 私用雲端 DNS 伺服器向前查閱內部部署資源
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3571455db6ecc600bf0948087b40c281d72512ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091244"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>啟用 CloudSimple 私人雲端 DNS 伺服器，以將內部部署資源的 DNS 查閱轉送至您的 DNS 伺服器

私人雲端 DNS 伺服器可以將任何內部部署資源的 DNS 查閱轉送到您的 DNS 伺服器。  啟用查閱可讓私用雲端 vSphere 元件查閱內部部署環境中執行的任何服務，並使用完整功能變數名稱（FQDN）與其通訊。

## <a name="scenarios"></a>案例 

針對您的內部部署 DNS 伺服器轉送 DNS 查閱，可讓您在下列案例中使用私用雲端：

* 使用私人雲端作為內部部署 VMware 解決方案的嚴重損壞修復設定
* 使用內部部署 Active Directory 做為私人雲端 vSphere 的身分識別來源
* 使用 HCX 將虛擬機器從內部部署遷移至私用雲端

## <a name="before-you-begin"></a>開始之前

從您的私人雲端網路到內部部署網路必須有網路連線，DNS 轉送才能正常執行。  您可以使用下列內容設定網路連接：

* [使用 ExpressRoute 從內部部署連接到 CloudSimple](on-premises-connection.md)
* [設定站對站 VPN 閘道](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

必須在此連線上開啟防火牆埠，DNS 轉送才能正常執行。  使用的埠是 TCP 通訊埠53或 UDP 埠53。

> [!NOTE]
> 如果您使用站對站 VPN，您的內部部署 DNS 伺服器子網必須新增為內部部署首碼的一部分。

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>要求從私人雲端轉送至內部部署的 DNS

若要啟用從私人雲端轉送至內部部署的 DNS，請提交[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，並提供下列資訊。

* 問題類型：**技術**
* 訂用帳戶： **CloudSimple 服務部署所在的訂**用帳戶
* 服務：**依 CloudSimple 的 VMware 解決方案**
* 問題類型：**諮詢或如何? ...**
* 問題子類型：**需要與 NW 的協助**
* 在 [詳細資料] 窗格中，提供您內部部署網域的功能變數名稱。
* 在 [詳細資料] 窗格中，提供您的內部部署 DNS 伺服器清單，以從私人雲端轉送查閱。

## <a name="next-steps"></a>後續步驟

* [深入瞭解內部部署防火牆設定](on-premises-firewall-configuration.md)
* [內部部署 DNS 伺服器設定](on-premises-dns-setup.md)
