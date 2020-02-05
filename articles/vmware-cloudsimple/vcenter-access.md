---
title: Azure VMware 解決方案（AVS）-存取權 vSphere 用戶端
description: 說明如何從您的 AVS 私人雲端存取 vCenter。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022659"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>存取您的 AVS 私用雲端 vCenter 入口網站

您可以從 Azure 入口網站或 AVS 入口網站啟動您的 AVS 私用雲端 vCenter 入口網站。 vCenter 入口網站可讓您管理您的 AVS 私人雲端上的 VMware 基礎結構。

## <a name="before-you-begin"></a>開始之前

必須建立網路連線，而且必須啟用 DNS 名稱解析，才能存取 vCenter 入口網站。 您可以使用下列任何選項，建立與您的 AVS 私人雲端的網路連線。

* [使用 ExpressRoute 從內部部署連接到 AVS](on-premises-connection.md)
* [設定與您的 AVS 私人雲端的 VPN 連線](set-up-vpn.md)

若要設定您的 AVS 私用雲端 VMware 基礎結構元件的 DNS 名稱解析，請參閱針對[來自內部部署工作站的 Avs 私用雲端 vCenter 存取設定 dns 以進行名稱解析](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-vcenter-from-azure-portal"></a>從 Azure 入口網站存取 vCenter

您可以從 Azure 入口網站啟動您的 AVS 私人雲端的 vCenter 入口網站。

1. 選取 [所有服務]。

2. 搜尋 [ **AVS 服務**]。

3. 選取您要連接的 AVS 私人雲端的 AVS 服務。

4. 在 [**總覽**] 頁面上，按一下 [**查看 VMware AVS 私人**雲端]

    ![AVS 服務總覽](media/cloudsimple-service-overview.png)

5. 從 [AVS 私人雲端] 清單中選取 [AVS 私用雲端]，然後按一下 [**啟動 VSphere 用戶端**]。

    ![啟動 vSphere 用戶端](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>從 AVS 入口網站存取 vCenter

您可以從 AVS 入口網站啟動您的 AVS 私用雲端的 vCenter 入口網站。

1. 存取您的[AVS 入口網站](access-cloudsimple-portal.md)。

2. 從 [**資源**] 中，選取您想要存取的 AVS 私人雲端，然後按一下 [**啟動 vSphere 用戶端**]。

    ![啟動 vSphere 用戶端-資源](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 您也可以從您的 AVS 私人雲端的 [摘要] 畫面啟動 vCenter 入口網站。

    ![啟動 vSphere 用戶端-摘要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>後續步驟

* [建立及管理您的 AVS 私人雲端的 Vlan/子網](create-vlan-subnet.md)
* [VMware vCenter 的 AVS 私用雲端許可權模型](learn-private-cloud-permissions.md)