---
title: 使用 ExpressRoute 將 Azure 虛擬網路連接到雲簡單 - 按雲簡單方式連接 Azure VMware 解決方案
description: 描述如何獲取 Azure 虛擬網路和 CloudSimple 環境之間連接的對等資訊
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563986"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>使用快速路由將 Azure 虛擬網路連接到雲簡單

您可以將私有雲網路擴展到 Azure 虛擬網路和 Azure 資源。 通過 ExpressRoute 連接，您可以從私有雲訪問 Azure 訂閱中運行的資源。

## <a name="request-authorization-key"></a>請求授權金鑰

私有雲和 Azure 虛擬網路之間的 ExpressRoute 連接需要授權金鑰。 要獲取金鑰，使用<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援</a>檔票證。  在請求中使用以下資訊：

* 問題類型：**技術**
* 訂閱：**選擇部署雲簡單服務的訂閱**
* 服務 **：VMware 解決方案（按雲簡單）**
* 問題類型：**服務請求**
* 問題子類型 **：Azure VNET 連接的授權金鑰**
* 主題 **：Azure VNET 連接的授權金鑰請求**

## <a name="get-peering-information-from-cloudsimple-portal"></a>從雲簡單門戶獲取對等資訊

要設置連接，必須在 Azure 虛擬網路和 CloudSimple 環境之間建立連接。  作為該過程的一部分，您必須提供對等電路 URI 和授權金鑰。 從[CloudSimple 門戶](access-cloudsimple-portal.md)獲取 URI 和授權金鑰。  選擇**側**功能表上的網路，然後選擇 Azure**網路連接**。 或者選擇側功能表上的**帳戶**，然後選擇**Azure 網路連接**。

使用*複製*圖示複製對等電路 URI 以及每個區域的授權金鑰。 對於要連接的每個雲簡單區域：

1. 按一下 **"複製**"以複製 URI。 將其粘貼到可用於添加到 Azure 門戶的檔中。  
2. 按一下 **"複製**"以複製授權金鑰並將其粘貼到檔中。

複製處於**可用**狀態的授權金鑰和對等電路 URI。  **已使用**狀態表示金鑰已用於創建虛擬網路連接。

![虛擬網路連接頁面](media/virtual-network-connection.png)

有關將 Azure 虛擬網路設置為雲簡單連結的詳細資訊，請參閱[使用 ExpressRoute 將雲簡單私有雲環境連接到 Azure 虛擬網路](azure-expressroute-connection.md)。

## <a name="next-steps"></a>後續步驟

* [Azure 虛擬網路連接到私有雲](azure-expressroute-connection.md)
* [使用 Azure 快速路由連接到本地網路](on-premises-connection.md)
