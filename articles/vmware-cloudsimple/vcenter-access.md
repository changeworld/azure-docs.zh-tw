---
title: Azure VMware Solution by CloudSimple-存取 vSphere 用戶端
description: 說明如何存取私人雲端的 vCenter。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77022659"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>存取您的私用雲端 vCenter 入口網站

您可以從 Azure 入口網站或 CloudSimple 入口網站啟動您的私用雲端 vCenter 入口網站。  vCenter 入口網站可讓您管理私人雲端上的 VMware 基礎結構。

## <a name="before-you-begin"></a>開始之前

必須建立網路連線，而且必須啟用 DNS 名稱解析，才能存取 vCenter 入口網站。  您可以使用下列任何選項，建立與私人雲端的網路連線。

* [使用 ExpressRoute 從內部部署連接到 CloudSimple](on-premises-connection.md)
* [設定 CloudSimple 私人雲端的 VPN 連線](set-up-vpn.md)

若要設定私人雲端 VMware 基礎結構元件的 DNS 名稱解析，請參閱針對[從內部部署工作站進行私人雲端 vCenter 存取的名稱解析設定 dns](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-vcenter-from-azure-portal"></a>從 Azure 入口網站存取 vCenter

您可以從 Azure 入口網站啟動私人雲端的 vCenter 入口網站。

1. 選取 [所有服務]  。

2. 搜尋**CloudSimple Services**。

3. 選取您要連接之私人雲端的 CloudSimple 服務。

4. 在 [**總覽**] 頁面上，按一下 [**查看 VMware 私人**雲端]

    ![CloudSimple 服務總覽](media/cloudsimple-service-overview.png)

5. 從私人雲端清單中選取私人雲端，然後按一下 [**啟動 VSphere 用戶端**]。

    ![啟動 vSphere 用戶端](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>從 CloudSimple 入口網站存取 vCenter

您可以從 CloudSimple 入口網站啟動私人雲端的 vCenter 入口網站。

1. 存取您的[CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 從 [**資源**] 中，選取您想要存取的私人雲端，然後按一下 [**啟動 vSphere 用戶端**]。

    ![啟動 vSphere 用戶端-資源](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 您也可以從私人雲端的 [摘要] 畫面啟動 vCenter 入口網站。

    ![啟動 vSphere 用戶端-摘要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>後續步驟

* [建立及管理私人雲端的 Vlan/子網](create-vlan-subnet.md)
* [VMware vCenter 的 CloudSimple 私用雲端許可權模型](learn-private-cloud-permissions.md)