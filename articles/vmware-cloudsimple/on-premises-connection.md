---
title: Azure VMware Solution by CloudSimple-使用 ExpressRoute 的內部部署連線
description: 說明如何使用 ExpressRoute 從 CloudSimple 區域網路要求內部部署連線
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 27d1d9e54838d9f45a28d634fa1c24fb8785aee1
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899230"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>使用 ExpressRoute 從內部部署連接到 CloudSimple

如果您已經有來自外部位置的 Azure ExpressRoute 連線 (例如內部部署) 到 Azure，您可以將它連線到您的 CloudSimple 環境。 您可以透過可讓兩個 ExpressRoute 線路彼此連接的 Azure 功能，來完成此動作。 這個方法會建立兩個環境之間的安全、私用、高頻寬、低延遲連接。

[![內部部署 ExpressRoute 連線-全球接觸](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>開始之前

若要建立從內部部署連線的全球連線，則需要 **/29** 網路位址區塊。  /29 位址空間是用於 ExpressRoute 線路之間的傳輸網路。  傳輸網路不應與任何 Azure 虛擬網路、內部部署網路或 CloudSimple 私人雲端網路重迭。

## <a name="prerequisites"></a>必要條件

* 您必須要有 Azure ExpressRoute 線路，才能建立線路與 CloudSimple 私人雲端網路之間的連線。
* 需要有許可權才能在 ExpressRoute 線路上建立授權金鑰的使用者。

## <a name="scenarios"></a>案例

將您的內部部署網路連線到您的私人雲端網路，可讓您以各種方式使用私用雲端，包括下列案例：

* 無須建立站對站 VPN 連線，即可存取您的私用雲端網路。
* 使用您的內部部署 Active Directory 作為私用雲端上的身分識別來源。
* 將在內部部署執行的虛擬機器遷移至私人雲端。
* 使用您的私人雲端作為嚴重損壞修復解決方案的一部分。
* 在您的私人雲端工作負載 Vm 上使用內部部署資源。

## <a name="connecting-expressroute-circuits"></a>連接 ExpressRoute 線路

若要建立 ExpressRoute 連線，您必須建立 ExpressRoute 線路的授權，並將授權資訊提供給 CloudSimple。


### <a name="create-expressroute-authorization"></a>建立 ExpressRoute 授權

1. 登入 Azure 入口網站。

2. 在頂端搜尋列中，搜尋 **expressroute 線路**，然後按一下 [**服務**] 下的 [ **expressroute 線路**]。
    [![ExpressRoute 線路](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. 選取您要連接到 CloudSimple 網路的 ExpressRoute 線路。

4. 在 [ExpressRoute] 頁面上，按一下 [ **授權**]，輸入授權的名稱，然後按一下 [ **儲存**]。
    [![ExpressRoute 線路授權](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 按一下複製圖示來複製資源識別碼和授權金鑰。 將識別碼和金鑰貼到文字檔中。
    [![ExpressRoute 線路授權複製](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **資源識別碼** 必須從 UI 複製，而且 ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` 當您提供支援時，應採用格式。

6. 提出 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援</a> 建立連線的票證。
    * 問題類型： **技術**
    * 訂用帳戶： **CloudSimple 服務部署所在的訂** 用帳戶
    * 服務： **VMware Solution By CloudSimple**
    * 問題類型： **服務要求**
    * 問題子類型：**建立與內部部署的 ExpressRoute** 連線
    * 提供您在 [詳細資料] 窗格中複製並儲存的資源識別碼和授權金鑰。
    * 為傳輸網路提供/29 網路位址空間。
    * 您是否透過 ExpressRoute 傳送預設路由？
    * 私人雲端流量是否應使用透過 ExpressRoute 傳送的預設路由？

    > [!IMPORTANT]
    > 傳送預設路由可讓您使用內部部署網際網路連線，從私人雲端傳送所有的網際網路流量。  若要停用在私人雲端上設定的預設路由，並使用內部部署連線預設路由，請在支援票證中提供詳細資料。

## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure 網路連接](cloudsimple-azure-network-connection.md)  
