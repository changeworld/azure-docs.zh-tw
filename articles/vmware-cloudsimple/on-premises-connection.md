---
title: Azure VMware 解決方案（AVS）-使用 ExpressRoute 的內部部署連線
description: 說明如何使用來自 AVS 地區網路的 ExpressRoute 要求內部部署連線
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019616"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>使用 ExpressRoute 從內部部署連接到 AVS

如果您已經有從外部位置（例如內部部署）到 Azure 的 Azure ExpressRoute 連線，您可以將它連線到您的 AVS 環境。 若要這麼做，您可以透過 Azure 功能，讓兩個 ExpressRoute 線路彼此連接。 這個方法會在兩個環境之間建立安全、私用、高頻寬、低延遲的連線。

[![內部部署 ExpressRoute 連線-全球範圍](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>開始之前

若要從內部部署環境建立全球觸達連線，則需要 **/29**網路位址區塊。 /29 位址空間用於 ExpressRoute 線路之間的傳輸網路。 傳輸網路不應該與您的任何 Azure 虛擬網路、內部部署網路或 AVS 私人雲端網路重迭。

## <a name="prerequisites"></a>必要條件

* 您必須先有 Azure ExpressRoute 電路，才能建立線路與 AVS 私人雲端網路之間的連線。
* 使用者必須具備在 ExpressRoute 線路上建立授權金鑰的許可權。

## <a name="scenarios"></a>案例

將內部部署網路連接到您的 AVS 私人雲端網路，可讓您以各種方式使用 AVS 私用雲端，包括下列案例：

* 存取您的 AVS 私人雲端網路，而不需建立站對站 VPN 連線。
* 使用您的內部部署 Active Directory 做為您的 AVS 私人雲端上的身分識別來源。
* 將內部部署執行的虛擬機器遷移至您的 AVS 私用雲端。
* 使用您的 AVS 私用雲端作為嚴重損壞修復解決方案的一部分。
* 使用您的 AVS 私用雲端工作負載 Vm 上的內部部署資源。

## <a name="connecting-expressroute-circuits"></a>連接 ExpressRoute 線路

若要建立 ExpressRoute 連線，您必須在您的 ExpressRoute 線路上建立授權，並將授權資訊提供給 AVS。


### <a name="create-expressroute-authorization"></a>建立 ExpressRoute 授權

1. 登入 Azure 入口網站。

2. 從頂端的搜尋列中，搜尋**ExpressRoute 線路**，然後按一下 [**服務**] 底下的 [ **expressroute 線路**]。
    [![ExpressRoute 線路](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. 選取您想要連接到您的 AVS 網路的 ExpressRoute 線路。

4. 在 [ExpressRoute] 頁面上，按一下 [**授權**]，輸入授權的名稱，然後按一下 [**儲存**]。
    [![ExpressRoute 線路授權](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 按一下 [複製] 圖示來複製資源識別碼和授權金鑰。 將識別碼和金鑰貼到文字檔中。
    [![ExpressRoute 線路授權複本](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **資源識別碼**必須從 UI 複製，而且當您提供支援時，其格式應該是 ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>```。

6. 以<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支援</a>建立的連線來提出票證。
    * 問題類型：**技術**
    * 訂用帳戶：**已部署 AVS 服務的訂**用帳戶
    * 服務： **VMware 解決方案（AVS）**
    * 問題類型：**服務要求**
    * 問題子類型：**建立與內部部署的 ExpressRoute**連線
    * 提供您複製並儲存在詳細資料窗格中的資源識別碼和授權金鑰。
    * 提供傳輸網路的/29 網路位址空間。
    * 您要透過 ExpressRoute 傳送預設路由嗎？
    * AVS 私用雲端流量應該使用透過 ExpressRoute 傳送的預設路由嗎？

    > [!IMPORTANT]
    > 傳送預設路由可讓您使用內部部署網際網路連線，從 AVS 私人雲端傳送所有網際網路流量。 若要停用在 AVS 私人雲端上設定的預設路由，並使用內部部署連線預設路由，請在支援票證中提供詳細資料。

## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure 網路連線](cloudsimple-azure-network-connection.md)  
