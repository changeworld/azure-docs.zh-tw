---
title: Azure VMware Solution by CloudSimple-Access vSphere 用戶端
description: 說明如何存取私人雲端的 vCenter。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899111"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>存取私用雲端 vCenter 入口網站

您可以從 Azure 入口網站或 CloudSimple 入口網站啟動私人雲端 vCenter 入口網站。  vCenter 入口網站可讓您管理私用雲端上的 VMware 基礎結構。

## <a name="before-you-begin"></a>開始之前

必須建立網路連線，而且必須啟用 DNS 名稱解析，才能存取 vCenter 入口網站。  您可以使用下列任何選項來建立與私人雲端的網路連線。

* [使用 ExpressRoute 從內部部署連接到 CloudSimple](on-premises-connection.md)
* [設定 CloudSimple 私人雲端的 VPN 連線](set-up-vpn.md)

若要設定私人雲端 VMware 基礎結構元件的 DNS 名稱解析，請參閱設定 [dns 進行名稱解析，以從內部部署工作站進行私用雲端 vCenter 存取](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-vcenter-from-azure-portal"></a>從 Azure 入口網站存取 vCenter

您可以從 Azure 入口網站啟動私人雲端的 vCenter 入口網站。

1. 選取 [所有服務]  。

2. 搜尋 **CloudSimple 服務**。

3. 選取您要連接之私人雲端的 CloudSimple 服務。

4. 在 [**總覽**] 頁面上，按一下 [ **View VMware Private cloud** ]

    ![CloudSimple 服務總覽](media/cloudsimple-service-overview.png)

5. 從私人雲端清單中選取私人雲端，然後按一下 [ **啟動 VSphere 用戶端**]。

    ![啟動 vSphere 用戶端](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>從 CloudSimple 入口網站存取 vCenter

您可以從 CloudSimple 入口網站啟動私人雲端的 vCenter 入口網站。

1. 存取 [CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 從 **資源** 中選取您想要存取的私人雲端，然後按一下 [ **啟動 vSphere 用戶端**]。

    ![啟動 vSphere 用戶端-資源](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 您也可以從私人雲端的摘要畫面啟動 vCenter 入口網站。

    ![啟動 vSphere 用戶端-摘要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>後續步驟

* [建立及管理私人雲端的 Vlan/子網](create-vlan-subnet.md)
* [VMware vCenter 的 CloudSimple 私用雲端許可權模型](learn-private-cloud-permissions.md)