---
title: Azure VMware 解決方案（按雲簡單 - 使用 ExpressRoute 的本地連接）
description: 描述如何使用來自雲簡單區域網路的 ExpressRoute 請求本地連接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019616"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>使用 ExpressRoute 從本地連接到雲簡單

如果已經從外部位置（如本地）連接到 Azure，則可以將其連接到 CloudSimple 環境。 可以通過 Azure 功能執行此操作，該功能允許兩個 ExpressRoute 電路相互連接。 此方法在兩個環境之間建立安全、私有、高頻寬、低延遲的連接。

[![本地快速路由連接 - 全球覆蓋](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>開始之前

從本地建立全域覆蓋連接需要 **/29**網路位址塊。  /29 位址空間用於 ExpressRoute 電路之間的傳輸網路。  傳輸網路不應與任何 Azure 虛擬網路、本地網路或雲簡單私有雲網路重疊。

## <a name="prerequisites"></a>Prerequisites

* 在建立電路和雲簡單私有雲網路之間的連接之前，需要 Azure ExpressRoute 電路。
* 使用者必須具有在 ExpressRoute 電路上創建授權金鑰的許可權。

## <a name="scenarios"></a>案例

通過將本地網路連接到私有雲網路，您可以以各種方式使用私有雲，包括以下方案：

* 無需創建網站到網站 VPN 連接即可訪問您的私有雲網路。
* 將本地活動目錄用作私有雲上的標識源。
* 將本地運行的虛擬機器遷移到私有雲。
* 將私有雲用作災害復原解決方案的一部分。
* 在私有雲工作負載 VM 上使用本地資源。

## <a name="connecting-expressroute-circuits"></a>連接快速路由電路

要建立 ExpressRoute 連接，您必須在 ExpressRoute 電路上創建授權，並將授權資訊提供給 CloudSimple。


### <a name="create-expressroute-authorization"></a>創建快速路由授權

1. 登入 Azure 入口網站。

2. 從頂部搜索欄中，搜索**ExpressRoute 電路**，然後按一下 **"服務**"下的**ExpressRoute 電路**。
    [![快速路由電路](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. 選擇要連接到雲簡單網路的 ExpressRoute 電路。

4. 在"快速路由"頁上，按一下 **"授權**"，輸入授權的名稱，然後按一下"**保存**"。
    [![快速路由電路授權](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 通過按一下複製圖示複製資源識別碼 和授權金鑰。 將 ID 和金鑰粘貼到文字檔中。
    [![快速路由電路授權副本](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **資源識別碼**必須從 UI 複製，並且在提供```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>```資源識別碼 以支援時應採用該格式。

6. 使用<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援</a>要創建的連接提交票證。
    * 問題類型：**技術**
    * 訂閱：**部署雲簡單服務的訂閱**
    * 服務 **：VMware 解決方案（按雲簡單）**
    * 問題類型：**服務請求**
    * 問題子類型：**創建到本地的快速路由連接**
    * 提供在詳細資訊窗格中複製並保存的資源識別碼 和授權金鑰。
    * 為傳輸網路提供 /29 網路位址空間。
    * 您是否通過 ExpressRoute 發送預設路由？
    * 私有雲流量是否應使用通過 ExpressRoute 發送的預設路由？

    > [!IMPORTANT]
    > 發送預設路由允許您使用本地互聯網連接從私有雲發送所有互聯網流量。  要禁用在私有雲上配置的預設路由並使用本地連接預設路由，請提供支援票證中的詳細資訊。

## <a name="next-steps"></a>後續步驟

* [瞭解有關 Azure 網路連接的更多資訊](cloudsimple-azure-network-connection.md)  
