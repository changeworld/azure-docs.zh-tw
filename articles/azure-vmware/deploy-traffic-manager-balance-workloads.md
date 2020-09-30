---
title: 部署流量管理員以平衡 Azure VMware 解決方案 (AVS) 工作負載
description: 瞭解如何將流量管理員與 Azure VMware Solution (AVS) 整合，以平衡不同區域中多個端點之間的應用程式工作負載。
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: d461cc444c60e1907a34a08c68139446301c133c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579662"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>部署流量管理員以平衡 Azure VMware 解決方案 (AVS) 工作負載

本文將逐步引導您整合流量管理員與 Azure VMware Solution (AVS) ，以平衡多個端點之間的應用程式工作負載。 我們將探討下列案例：流量管理員會將三個應用程式閘道之間的流量導向至多個 AVS 區域：美國西部、西歐和美國東部的內部部署。 

Azure 流量管理員是以 DNS 為基礎的流量負載平衡器，可讓您以最理想的方式，將流量分散到全球 Azure 區域之間的服務。 它會對 Azure 執行中工作負載和外部公用端點之間的應用程式流量進行負載平衡。 如需流量管理員的詳細資訊，請參閱 [什麼是流量管理員？](../traffic-manager/traffic-manager-overview.md)

請先檢查 [必要條件](#prerequisites) ;接著，我們將逐步解說下列程式：

> [!div class="checklist"]
> * 確認應用程式閘道的設定
> * 確認 NSX-T 區段的設定
> * 建立您的流量管理員設定檔
> * 將外部端點新增至流量管理員設定檔

## <a name="topology"></a>拓撲

如下圖所示，Azure 流量管理員在區域端點之間的 DNS 層級提供應用程式的負載平衡。 應用程式閘道的後端集區成員已設定為 IIS 伺服器，並被視為 AVS 外部端點。

在兩個 AVS 私用雲端區域、美國西部和西歐以及美國東部的內部部署伺服器之間的虛擬網路連線，會使用 ExpressRoute 閘道。   

![流量管理員與 AVS 整合](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>必要條件

- 三部虛擬機器設定為在不同 AVS 區域中執行的 Microsoft IIS 伺服器：美國西部、西歐和內部部署。 

- 在美國西部、西歐和內部部署中具有外部端點的應用程式閘道。

- 具有網際網路連線能力的主機以進行驗證。 

## <a name="verify-configuration-of-your-application-gateways"></a>確認應用程式閘道的設定

[Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/) 是第7層 web 流量負載平衡器，可讓您管理 web 應用程式的流量。 如需應用程式閘道的詳細資訊，請參閱 [什麼是 Azure 應用程式閘道？](../application-gateway/overview.md) 

在此案例中，會將三個應用程式閘道實例設定為外部 AVS 端點。 應用程式閘道會將 AVS 虛擬機器設定為後端集區成員，以針對傳入的第7層要求進行負載平衡。  (若要瞭解如何將具有 AVS 虛擬機器的應用程式閘道設定為後端集區，請參閱 [使用 Azure 應用程式閘道保護 Azure VMware 解決方案上的 web 應用](protect-azure-vmware-solution-with-application-gateway.md)程式。 )   

下列步驟會確認應用程式閘道的正確設定。

1. 開啟 Azure 入口網站，然後選取 [ **應用程式閘道** ] 以查看您目前的應用程式閘道清單。 

    在此案例中，我們已設定三個應用程式閘道：
    - AVS-GW-WUS
    - AVS-GW-EUS (內部部署) 
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="應用程式閘道清單。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. 選取其中一個您先前部署的應用程式閘道。 隨即開啟一個視窗，顯示應用程式閘道上的各種資訊。 選取 [ **後端** 集區] 以確認其中一個後端集區的設定。

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="應用程式閘道清單。" lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. 在此情況下，我們會看到一個虛擬機器後端集區成員設定為具有 172.29.1.10 IP 位址的 web 伺服器。
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="應用程式閘道清單。":::

    同樣地，您也可以確認其他應用程式閘道和後端集區成員的設定。 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>確認 NSX-T 區段的設定

在 NSX 中建立的網路區段會當做 vCenter 中虛擬機器的網路使用。 如需詳細資訊，請參閱教學課程： [在 Azure VMware Solution 中建立 NSX-T 網路區段 (AVS) ](tutorial-nsx-t-network-segment.md)。

在我們的案例中，會在應用程式後端集區成員虛擬機器所連接的 AVS 環境中設定 NSX-T 區段。

1. 選取 **區段** 以查看已設定的區段。 在此情況下，我們看到 Contoso segment1 連接到 Contoso-T01 閘道，這是第1層彈性的路由器。

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="應用程式閘道清單。":::    

2. 選取 **第1層閘道** ，以查看具有連結區段數目的第1層閘道清單。 選取連結至 Contoso-T01 的區段。 隨即開啟一個視窗，顯示在第1層路由器上設定的邏輯介面。 這可做為連接到區段的後端集區成員虛擬機器的閘道。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="應用程式閘道清單。":::    

3. 在 VM vSphere 用戶端中，選取虛擬機器以查看其詳細資料。 請注意，其 IP 位址符合我們在上一節的步驟3中看到的內容：172.29.1.10。

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="應用程式閘道清單。":::    

4. 選取虛擬機器，然後按一下 [ **動作] > [編輯設定** ]，以確認連接到 NSX-T 區段。

## <a name="create-your-traffic-manager-profile"></a>建立您的流量管理員設定檔

1. 登入 [Azure 入口網站](https://rc.portal.azure.com/#home)。 在 [ **Azure 服務 > 網路**] 下，選取 [ **流量管理員設定檔**]。

2. 選取 [ **+ 新增** ] 以建立新的流量管理員設定檔。
 
3. 提供設定檔名稱，路由方法 (我們將在此案例中使用加權;請參閱 [流量管理員路由方法](../traffic-manager/traffic-manager-routing-methods.md)) 、訂用帳戶和資源群組，然後選取 [ **建立**]。

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>將外部端點新增至流量管理員設定檔

1. 從搜尋結果窗格中選取流量管理員設定檔，選取 [ **端點** ]，然後按一下 [ **+ 新增**]。

2. 輸入必要的詳細資料：類型、名稱、完整功能變數名稱 (FQDN) 或 IP，以及在此案例中 (權數，我們會將權數1指派給每個端點) 。 選取 [新增]。

   :::image type="content" source="media/traffic-manager/traffic-manager-profile.png" alt-text="應用程式閘道清單。":::  
 
   這會建立外部端點。 監視器狀態必須在 **線上**。 

   重複相同的步驟，以建立兩個外部端點，一個位於不同的區域，另一個在內部部署。 建立之後，所有三個都會顯示在流量管理員設定檔中，而所有三個的狀態都應該是 **線上**。

3. 選取 [概觀]。 在 [ **DNS 名稱**] 底下複製 URL。

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="應用程式閘道清單。"::: 

4. 將 DNS 名稱 URL 貼到瀏覽器中。 下列螢幕擷取畫面顯示導向西歐區域的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="應用程式閘道清單。"::: 

5. 重新整理您的瀏覽器。 下列螢幕擷取畫面顯示流量現在會導向美國西部區域中的另一組後端集區成員。

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="應用程式閘道清單。"::: 

6. 重新整理瀏覽器。 下列螢幕擷取畫面顯示流量現在會導向至內部部署的最後一組後端集區成員。

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="應用程式閘道清單。":::

## <a name="next-steps"></a>後續步驟

深入了解：

- [在 Azure VMware Solution 上使用 Azure 應用程式 Gateway (AVS) ](protect-azure-vmware-solution-with-application-gateway.md)
- [流量管理員路由傳送方法](../traffic-manager/traffic-manager-routing-methods.md)
- [在 Azure 中合併負載平衡服務](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [測量流量管理員效能](../traffic-manager/traffic-manager-performance-considerations.md)
