---
title: Azure 虛擬 WAN：建立站對站連線
description: 在本教學課程，您可了解如何使用 Azure 虛擬 WAN 來與 Azure 建立站對站的 VPN 連線。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775333"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>教學課程：使用 Azure 虛擬 WAN 來建立站對站連線

本教學課程會示範如何使用虛擬 WAN，透過 IPsec/IKE (IKEv1 和 IKEv2) VPN 連線來與 Azure 中的資源連線。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立虛擬 WAN
> * 建立中樞
> * 建立網站
> * 將網站連線到中樞
> * 將 VPN 網站連線到中樞
> * 將 VNet 連線至中樞
> * 下載設定檔
> * 檢視您的虛擬 WAN

> [!NOTE]
> 如果您有許多網站，通常會使用[虛擬 WAN 夥伴](https://aka.ms/virtualwan)來建立此組態。 不過，如果您慣用網路並且知道如何設定自己的 VPN 裝置，可以自行建立此組態。
>

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 您有一個要連線的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

* 您的虛擬網路沒有任何虛擬網路閘道。 如果您的虛擬網路有閘道 (VPN 或 ExpressRoute)，則必須移除所有閘道。 此設定需要將虛擬網路改為連線到虛擬 WAN 中樞閘道。

* 取得中樞區域的 IP 位址範圍。 中樞是虛擬 WAN 建立和使用的虛擬網路。 您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路設定的 IP 位址範圍，請與能夠提供那些詳細資料的人員協調。

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="openvwan"></a>建立虛擬 WAN

透過瀏覽器瀏覽至 Azure 入口網站，並使用您的 Azure 帳戶登入。

1. 瀏覽至 [虛擬 WAN] 頁面。 在入口網站中，按一下 [+建立資源]  。 在搜尋方塊中鍵入**虛擬 WAN** 並選取 [輸入]。
2. 從結果中選取 [虛擬 WAN]  。 在 [虛擬 WAN] 頁面中，按一下 [建立]  ，以開啟 [建立 WAN] 頁面。
3. 在 [建立 WAN]  頁面的 [基本]  索引標籤中，填寫下列欄位：

   ![虛擬 WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **訂用帳戶** - 選取您要使用的訂用帳戶。
   * **資源群組** - 建立新的或使用現有的資源群組。
   * **資源群組位置** - 從下拉式清單中選擇資源位置。 WAN 是全域資源，並不會在特定區域存留。 不過，您必須選取一個區域以方便管理，以及放置所建立的 WAN 資源。
   * **名稱** - 鍵入要用來稱呼 WAN 的名稱。
   * **類型：** 基本或標準。 如果您建立基本 WAN，則只能建立基本中樞。 基本中樞只能進行 VPN 站對站連線。
4. 填寫完欄位之後，選取 [檢閱 + 建立]  。
5. 驗證通過後，選取 [建立]  ，以建立虛擬 WAN。

## <a name="hub"></a>建立中樞。

中樞是一種虛擬網路，其中包含可用於站對站、ExpressRoute 或點對站功能的閘道。 中樞建立好時，您就需要支付中樞費用，即使您未連結任何網站也是如此。 在虛擬中樞內建立站對站 VPN 閘道需要 30 分鐘的時間。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>建立網站

您現在已準備好建立對應到實體位置的網站。 建立需要的網站數目，這會與您的實體位置相對應。 例如，如果您在紐約 (NY)、倫敦和洛杉磯 (LA) 各有分公司，就會建立三個不同網站。 這些網站包含您內部部署的 VPN 裝置端點。 在虛擬 WAN 中，您可以為每個虛擬中樞建立最多 1000 個網站。 如果您有多個中樞，您可以為其中每一個中樞建立 1000 個網站。 如果您有虛擬 WAN 合作夥伴 (連結插入) CPE 裝置，請向他們確認並了解其對 Azure 有何自動化項目。 一般來說，自動化體驗意指簡單按一下即可將大規模分支資訊匯出至 Azure，以及設定從 CPE 到 Azure 虛擬 WAN VPN 閘道的連線。 如需詳細資訊，請參閱[從 Azure 到 CPE 合作夥伴的自動化指引](virtual-wan-configure-automation-providers.md)。

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>將 VPN 網站連線到中樞

在此步驟中，您會將 VPN 網站連線到中樞。

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>將 VNet 連線到中樞

在此步驟中，您會在中樞和 VNet 之間建立連線。 為您想要連線的每個 VNet 重複這些步驟。

1. 在虛擬 WAN 頁面上，按一下 [虛擬網路連線]  。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]  。
3. 在 [新增連線]  頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定]  以建立虛擬網路連線。

## <a name="device"></a>下載 VPN 組態

若要設定您的內部部署 VPN 裝置，請使用 VPN 裝置組態。

1. 在您的虛擬 WAN 頁面上，按一下 [概觀]  。
2. 在 [中樞] -> [VPNSite]  頁面的頂端按一下 [下載 VPN 組態]  。Azure 會在資源群組 'microsoft-network-[location]' 中建立儲存體帳戶，其中 location 是 WAN 的位置。 將組態套用至您的 VPN 裝置之後，就可以刪除此儲存體帳戶。
3. 檔案建立完成之後，您可以按一下連結來下載。
4. 將組態套用至您的內部部署 VPN 裝置。

### <a name="understanding-the-vpn-device-configuration-file"></a>了解 VPN 裝置組態檔

裝置設定檔包含了設定內部部署 VPN 裝置時所要使用的設定。 當您檢視此檔案時，請注意下列資訊：

* **vpnSiteConfiguration -** 此區段表示網站連線至虛擬 WAN 時設定的裝置詳細資料。 其中包含分支裝置的名稱和公用 IP 位址。
* **vpnSiteConnections -** 此區段提供與下列設定有關的資訊：

    * 虛擬中樞 VNet 的**位址空間**<br>範例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 連線至中樞的 Vnet **位址空間**<br>範例：

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 虛擬中樞 Vpngateway 的 **IP 位址**。 由於 Vpngateway 的每個連線都由「主動對主動」設定中的 2 個通道組成，因此您會看到此檔案中列出這兩個 IP 位址。 在此範例中，您會看到每個網站的 "Instance0" 和 "Instance1"。<br>範例：

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway 連線設定詳細資料**，例如 BGP、預先共用金鑰等等。PSK 是自動為您產生的預先共用金鑰。 您隨時都可以在自訂 PSK 的 [概觀] 頁面中編輯連線。
  
### <a name="example-device-configuration-file"></a>範例裝置設定檔

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>設定 VPN 裝置

>[!NOTE]
> 如果您正在處理虛擬 WAN 合作夥伴解決方案，系統會自動進行 VPN 裝置設定。 裝置控制器會從 Azure 取得設定檔，並套用到裝置以設定 Azure 的連線。 這表示您無須知道如何手動設定 VPN 裝置。
>

如果需要設定裝置的指示，您可以使用 [VPN 裝置組態指令碼頁面](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)中的指示，並留意下列注意事項：

* VPN 裝置頁面上的指示不是針對虛擬 WAN 所撰寫，但您也可以從組態檔使用虛擬 WAN 的值，手動設定 VPN 裝置。 
* 適用於 VPN 閘道的可下載裝置組態指令碼不適用於虛擬 WAN，因為組態不同。
* 新的虛擬 WAN 可以支援 IKEv1 和 IKEv2。
* 虛擬 WAN 可以使用原則式與路由式 VPN 裝置和裝置指示。

## <a name="viewwan"></a>檢視您的虛擬 WAN

1. 瀏覽至虛擬 WAN。
2. 在 [概觀]  頁面中，地圖上的每個點都代表著中樞。 將滑鼠停留在任何一個點，即可檢視中樞健康情況摘要、連線狀態，以及傳入和傳出的位元組。
3. 在 [中樞和連線] 區段中，您可以檢視中樞狀態和 VPN 網站等項目。您可以按一下特定中樞名稱，然後瀏覽至 VPN 網站來取得其他詳細資料。

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
