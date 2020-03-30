---
title: Azure VMware 解決方案（按雲簡單 - 訪問 vSphere 用戶端）
description: 描述如何訪問私有雲的 vCenter。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022659"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>訪問您的私有雲 vCenter 門戶

可以從 Azure 門戶或雲簡單門戶啟動私有雲 vCenter 門戶。  vCenter 門戶允許您管理私有雲上的 VMware 基礎架構。

## <a name="before-you-begin"></a>開始之前

必須建立網路連接，並且必須啟用 DNS 名稱解析才能訪問 vCenter 門戶。  您可以使用以下任何選項建立與私有雲的網路連接。

* [使用 ExpressRoute 從本地連接到雲簡單](on-premises-connection.md)
* [配置與雲簡單私有雲的 VPN 連接](set-up-vpn.md)

要設置私有雲 VMware 基礎結構元件的 DNS 名稱解析，請參閱[配置 DNS 以獲取來自本地工作站的私有雲 vCenter 訪問的名稱解析](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="access-vcenter-from-azure-portal"></a>從 Azure 門戶訪問 vCenter

可以從 Azure 門戶啟動私有雲的 vCenter 門戶。

1. 選擇**所有服務**。

2. 搜索**雲簡單服務**。

3. 選擇要連接到的私有雲的雲簡單服務。

4. 在 **"概述"** 頁上，按一下"**查看 VMware 私有雲**"

    ![雲簡單服務概述](media/cloudsimple-service-overview.png)

5. 從私有雲清單中選擇私有雲，然後按一下 **"啟動 vSphere 用戶端**"。

    ![啟動 vSphere 用戶端](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>從雲簡單門戶訪問 vCenter

您可以通過雲簡單門戶啟動私有雲的 vCenter 門戶。

1. 訪問[雲簡單門戶](access-cloudsimple-portal.md)。

2. 從 **"資源"** 中選擇要訪問的私有雲，然後按一下 **"啟動 vSphere 用戶端**"。

    ![啟動 vSphere 用戶端 - 資源](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 您還可以從私有雲的摘要螢幕啟動 vCenter 門戶。

    ![啟動 vSphere 用戶端 - 摘要](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>後續步驟

* [為您的私有雲創建和管理 VLAN/子網](create-vlan-subnet.md)
* [VMware vCenter 的雲簡單私有雲許可權模型](learn-private-cloud-permissions.md)