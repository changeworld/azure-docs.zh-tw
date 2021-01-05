---
title: 部署流量管理員以平衡 Azure VMware 解決方案工作負載
description: 瞭解如何整合流量管理員與 Azure VMware 解決方案，以平衡不同區域中多個端點之間的應用程式工作負載。
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: 6dbd58f17e29b045bd654bee90b6390f608803ab
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809729"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>部署流量管理員以平衡 Azure VMware 解決方案工作負載

本文將逐步解說如何整合 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) 與 Azure VMware 解決方案的步驟。 整合會平衡多個端點之間的應用程式工作負載。 本文也會逐步解說如何設定流量管理員，以將三個 [Azure 應用程式閘道](../application-gateway/overview.md) 之間的流量導向數個 Azure VMware 解決方案區域。 

閘道會將 Azure VMware 解決方案虛擬機器 (Vm) 設定為後端集區成員，以針對傳入的第7層要求進行負載平衡。 如需詳細資訊，請參閱 [使用 Azure 應用程式閘道保護您在 Azure VMware 解決方案上的 web 應用程式](protect-azure-vmware-solution-with-application-gateway.md)

此圖顯示流量管理員如何在區域端點之間的 DNS 層級提供應用程式的負載平衡。 閘道的後端集區成員已設定為 IIS 伺服器，並被參考為 Azure VMware 解決方案外部端點。 這兩個私用雲端區域之間的虛擬網路連線使用 ExpressRoute 閘道。   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="流量管理員與 Azure VMware 解決方案整合的架構圖" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

開始之前，請先複習 [必要條件](#prerequisites) ，然後我們將逐步解說下列程式：

> [!div class="checklist"]
> * 確認您的應用程式閘道和 NSX-T 區段的設定
> * 建立您的流量管理員設定檔
> * 將外部端點新增至流量管理員設定檔

## <a name="prerequisites"></a>必要條件

- 有三個 Vm 設定為在不同的 Azure VMware 解決方案區域中執行的 Microsoft IIS 伺服器： 
   - 美國西部
   - 西歐
   - 美國東部 (內部部署)  

- 在先前所述的 Azure VMware 解決方案區域中，具有外部端點的應用程式閘道。

- 具有網際網路連線能力的主機以進行驗證。 

- [在 Azure VMware 解決方案中建立的 NSX-T 網路區段](tutorial-nsx-t-network-segment.md)。

## <a name="verify-your-application-gateways-configuration"></a>驗證您的應用程式閘道設定

下列步驟會確認應用程式閘道的設定。

1. 在 [Azure 入口網站中，選取 [ **應用程式閘道** ] 以查看您目前的應用程式閘道清單：

   - AVS-GW-WUS
   - AVS-GW-EUS (內部部署) 
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="應用程式閘道頁面的螢幕擷取畫面，其中顯示已設定的應用程式閘道清單。" lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. 選取其中一個您先前部署的應用程式閘道。 

   隨即開啟一個視窗，顯示應用程式閘道上的各種資訊。 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="應用程式閘道頁面的螢幕擷取畫面，其中顯示所選應用程式閘道的詳細資料。" lightbox="media/traffic-manager/backend-pool-config.png":::

1. 選取 [ **後端** 集區] 以確認其中一個後端集區的設定。 您會看到一個 VM 後端集區成員設定為具有 172.29.1.10 IP 位址的 web 伺服器。
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="[編輯後端集區] 頁面的螢幕擷取畫面，其中反白顯示目標 IP 位址。":::

1. 確認其他應用程式閘道和後端集區成員的設定。 

## <a name="verify-the-nsx-t-segment-configuration"></a>確認 NSX-T 區段設定

下列步驟會在 Azure VMware 解決方案環境中確認 NSX-T 區段的設定。

1. 選取 **區段** 以查看已設定的區段。  您會看到 Contoso segment1 連線到 Contoso-T01 閘道，第1層彈性路由器。

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="顯示 NSX-T 管理員中區段設定檔的螢幕擷取畫面。" lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. 選取 **第1層閘道** ，以查看具有連結區段數目的第1層閘道清單。 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="顯示所選區段之閘道位址的螢幕擷取畫面。":::    

1. 選取連結至 Contoso-T01 的區段。 隨即開啟一個視窗，顯示在第1層路由器上設定的邏輯介面。 它可做為連接到區段的後端集區成員 VM 的閘道。

1. 在 vSphere 用戶端中，選取 VM 以查看其詳細資料。 

   >[!NOTE]
   >其 IP 位址符合上一節中設定為 web 伺服器的 VM 後端集區成員：172.29.1.10。

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="顯示 vSphere 用戶端中 VM 詳細資料的螢幕擷取畫面。" lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. 選取 VM，然後選取 [ **動作] > [編輯設定** ]，以確認連接到 NSX-T 區段。

## <a name="create-your-traffic-manager-profile"></a>建立您的流量管理員設定檔

1. 登入 [Azure 入口網站](https://rc.portal.azure.com/#home)。 在 [ **Azure 服務 > 網路**] 下，選取 [ **流量管理員設定檔**]。

2. 選取 [ **+ 新增** ] 以建立新的流量管理員設定檔。
 
3. 提供下列資訊，然後選取 [ **建立**]：

   - 設定檔名稱
   - 路由方法 (使用 [加權](../traffic-manager/traffic-manager-routing-methods.md)
   - 訂用帳戶
   - 資源群組

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>將外部端點新增至流量管理員設定檔

1. 從搜尋結果窗格中選取流量管理員設定檔，選取 [ **端點**]，然後按一下 [ **+ 新增**]。

1. 針對不同區域中的每個外部端點，輸入必要的詳細資料，然後選取 [ **新增**]： 
   - 類型
   - 名稱
   -  (FQDN) 或 IP 的完整功能變數名稱
   - 權數 (將權數1指派給每個端點) 。 

   建立之後，所有三個都會顯示在流量管理員設定檔中。 所有三項的監視器狀態都必須在 **線上**。

3. 選取 **[總覽** ]，然後在 [ **DNS 名稱**] 底下複製 URL。

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="顯示流量管理員端點總覽的螢幕擷取畫面，其中已反白顯示 DNS 名稱。" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. 將 DNS 名稱 URL 貼到瀏覽器中。 螢幕擷取畫面顯示導向西歐區域的流量。

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="瀏覽器視窗的螢幕擷取畫面，顯示路由傳送至西歐的流量。"::: 

5. 重新整理您的瀏覽器。 螢幕擷取畫面顯示的流量會導向至美國西部區域中的另一組後端集區成員。

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="瀏覽器視窗的螢幕擷取畫面，顯示路由傳送到美國西部的流量。"::: 

6. 重新整理瀏覽器。 螢幕擷取畫面顯示的流量會導向至內部部署的最後一組後端集區成員。

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="瀏覽器視窗的螢幕擷取畫面，顯示路由傳送至內部部署的流量。":::

## <a name="next-steps"></a>後續步驟

深入了解：

- [在 Azure VMware 解決方案上使用 Azure 應用程式閘道](protect-azure-vmware-solution-with-application-gateway.md)
- [流量管理員路由傳送方法](../traffic-manager/traffic-manager-routing-methods.md)
- [在 Azure 中合併負載平衡服務](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [測量流量管理員效能](../traffic-manager/traffic-manager-performance-considerations.md)
