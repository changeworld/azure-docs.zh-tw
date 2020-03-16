---
title: 教學課程：建立內部負載平衡器 - Azure 入口網站
titleSuffix: Azure Load Balancer
description: 本教學課程會示範如何使用 Azure 入口網站建立內部的基本負載平衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 6f62771d707d1aebccbfaf809dee7d0dedf5fefa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096124"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>教學課程：在 Azure 入口網站中使用基本負載平衡器來平衡內部流量負載

負載平衡會將傳入要求分散於多部虛擬機器 (VM)，藉此提供高可用性和範圍。 您可使用 Azure 入口網站建立基本負載平衡器，以及平衡多部虛擬機器的內部流量。 本教學課程說明如何在基本定價層建立及設定內部負載平衡器、後端伺服器及網路資源。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

您可以依偏好使用 [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) 或 [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) (而非入口網站) 執行這些步驟。

若要使用本教學課程執行步驟，請登入 Azure 入口網站，網址為：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>建立 VNet、後端伺服器和測試 VM

首先，建立虛擬網路 (VNet)。 在 VNet 中，建立要用於基本負載平衡器後端集區的兩部 VM，以及要用於測試負載平衡器的第三部 VM。 

### <a name="create-a-virtual-network"></a>建立虛擬網路

1. 在入口網站的左上方，選取 [建立資源]   > [網路]   > [虛擬網路]  。
   
1. 在 [建立虛擬網路]  窗格中，輸入或選取下列值：
   
   - **Name**：輸入 MyVNet  。
   - **資源群組**：選取 [新建]  ，然後輸入 MyResourceGroupLB  ，然後選取 [確定]  。 
   - [子網路]   > [名稱]  ：輸入 MyBackendSubnet  。
   
1. 選取 [建立]  。

   ![建立虛擬網路](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>建立虛擬機器

1. 在入口網站的左上方，選取 [建立資源]   >  **[計算]**  > [Windows Server 2016 Datacenter]  。 
   
1. 在 [建立虛擬機器]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：
   - [訂用帳戶]   > [資源群組]  ：下拉並選取 [MyResourceGroupLB]  。
   - **執行個體詳細資料** > **虛擬機器名稱**：輸入 MyVM1  。
   - **執行個體詳細資料** > **可用性選項**： 
     1. 下拉並選取 [可用性設定組]  。 
     2. 選取 [新建]  ，輸入 MyAvailabilitySet  ，然後選取 [確定]  。
   
1. 選取 [網路]  索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。 
   
   請確定已選取下列項目：
   - **虛擬網路**：**MyVNet**
   - **子網路**：**MyBackendSubnet**
   
   在 [網路安全性群組]  之下：
   1. 選取 [進階]  。 
   1. 下拉 [設定網路安全性群組]  並選取 [無]  。 
   
1. 選取 [管理]  索引標籤，或選取 [下一步]   > [管理]  。 在 [監視]  下，將 [開機診斷]  設定為 [關閉]  。
   
1. 選取 [檢閱 + 建立]  。
   
1. 檢閱設定，然後選取 [建立]  。 

1. 請依照下列步驟來建立名為 *MyVM2* 的第二個 VM，其所有其他設定與 MyVM1 相同。 

1. 再次遵循步驟來建立第三個名為 *MyTestVM* 的 VM。 

## <a name="create-a-basic-load-balancer"></a>建立基本負載平衡器

使用入口網站建立基本內部負載平衡器。 您所建立的名稱和 IP 位址會自動設定為負載平衡器的前端。

1. 在入口網站的左上方，選取 [建立資源]   > [網路]   > [負載平衡器]  。
   
2. 在 [建立負載平衡器]  頁面的 [基本資料]  中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [檢閱 + 建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]  ，並在文字方塊中輸入 *MyResourceGroupLB*。|
    | 名稱                   | *myLoadBalancer*                                   |
    | 區域         | 選取 [美國東部 2]  。                                        |
    | 類型          | 選取 [內部]  。                                        |
    | SKU           | 選取 [基本]  。                          |
    | 虛擬網路           | 選取 [MyVNet]  。                          |    
    | IP 位址指派              | 選取 [靜態]  。   |
    | 私人 IP 位址|輸入位於您虛擬網路和子網路的位址空間內的位址，例如 *10.3.0.7*。  |

3. 在 [檢閱 + 建立]  索引標籤中，按一下 [建立]  。 
   

## <a name="create-basic-load-balancer-resources"></a>建立基本負載平衡器資源

在本節中，您會設定後端位址集區和健康狀態探查的負載平衡器設定，並指定負載平衡器規則。

### <a name="create-a-back-end-address-pool"></a>建立後端位址集區

若要將流量分散至 VM，則負載平衡器會使用後端位址集區。 後端位址集區包含已連線至負載平衡器之虛擬網路介面 (NIC) 的 IP 位址。 

**若要建立包含 VM1 和 VM2 的後端位址集區：**

1. 選取左側功能表上的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  底下選取 [後端集區]  ，然後選取 [新增]  。
   
1. 在 [新增後端集區]  頁面上，輸入或選取下列值：
   
   - **Name**：輸入 MyBackendPool  。
   - **與下列產生關聯**：下拉並選取 [虛擬機器]  。
   
   
1. 選取 [虛擬機器]  。 
   1. 將 **MyVM1** 和 **MyVM2** 新增到後端集區。
   2. 新增每部機器之後，下拉並選取其 [網路 IP 組態]  。 
   
   >[!NOTE]
   >請誤將 **MyTestVM** 新增到集區。 
   
1. 選取 [確定]  。
   
   ![新增後端位址集區](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. 在 [後端集區]  頁面上，展開 **MyBackendPool**，並確定 **VM1** 和 **VM2** 都已列出。

### <a name="create-a-health-probe"></a>建立健康狀態探查

若要讓負載平衡器能監視 VM 狀態，請使用健康情況探查。 健康狀態探查會根據 VM 對健康狀態檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 

**若要建立健康情況探查以監視 VM 的健康情況：**

1. 選取左側功能表上的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  底下選取 [健康狀態探查]  ，然後選取 [新增]  。
   
1. 在 [新增健康情況探查]  頁面上，輸入或選取下列值：
   
   - **Name**：輸入 MyHealthProbe  。
   - **通訊協定**：下拉並選取 [HTTP]  。 
   - **連接埠**：輸入 80  。 
   - **路徑**：接受 /  作為預設 URI。 您可以使用任何其他 URI 來取代此值。 
   - **間隔**：輸入 15  。 間隔是探查嘗試之間的秒數。
   - **狀況不良閾值**：輸入 2  。 這個值是將 VM 視為狀況不良之前，所發生的連續探查失敗次數。
   
1. 選取 [確定]  。
   
   ![新增探查](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則可定義如何將流量分散至 VM。 此規則會定義連入流量的前端 IP 組態、用來接收流量的後端 IP 集區，以及所需的來源和目的地連接埠。 

名為 **MyLoadBalancerRule** 的負載平衡器規則會接聽前端 **LoadBalancerFrontEnd** 中的連接埠 80。 此規則會將網路流量傳送到後端位址集區 **MyBackendPool**，也在連接埠 80 上。 

**若要建立負載平衡器規則：**

1. 選取左側功能表上的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  下選取 [負載平衡規則]  ，然後選取 [新增]  。
   
1. 在 [新增負載平衡規則]  頁面上，輸入或選取下列值 (如果尚未存在)：
   
   - **Name**：輸入 MyLoadBalancerRule  。
   - **前端 IP 位址：** 輸入 LoadBalancerFrontEnd  (如果部存在)。
   - **通訊協定**：選取 [TCP]  。
   - **連接埠**：輸入 80  。
   - **後端連接埠**：輸入 80  。
   - **後端集區**：選取 [MyBackendPool]  。
   - **健康情況探查**：選取 [MyHealthProbe]  。 
   
1. 選取 [確定]  。
   
   ![新增負載平衡器規則](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>測試負載平衡器

在後端伺服器上安裝 Internet Information Services (IIS)，然後使用 MyTestVM 來測試使用其私人 IP 位址的負載平衡器。 每部後端 VM 都會提供不同版本的預設 IIS 網頁，以便您查看兩部 VM 之間的負載平衡器分散式要求。

在入口網站中 **MyLoadBalancer** 的 [概觀]  頁面上，於 [私人 IP 位址]  之下尋找其 IP 位址。 將滑鼠停在該位址上，然後選取 [複製]  圖示來複製它。 在此範例中為 **10.3.0.7**。 

### <a name="connect-to-the-vms-with-rdp"></a>使用 RDP 連線到 VM

首先，使用遠端桌面 (RDP) 連線到三部 VM。 

>[!NOTE]
>根據預設，VM 已經開啟 **RDP** (遠端桌面) 連接埠，以允許遠端桌面存取。 

**若要將遠端桌面 (RDP) 連到 VM：**

1. 在入口網站的左側功能表上，選取 [所有資源]  。 從資源清單，選取 **MyResourceGroupLB** 資源群組中的每部 VM。
   
1. 在 [概觀]  頁面上，選取 [連線]  ，然後選取 [下載 RDP 檔案]  。 
   
1. 開啟所下載的 RDP 檔案，然後選取 [連線]  。
   
1. 在 [Windows 安全性] 畫面上，選取 [更多選擇]  ，然後選取 [使用不同的帳戶]  。 
   
   輸入使用者名稱和密碼，然後選取 [確定]  。
   
1. 對任何憑證提示回應 [是]  。 
   
   VM 桌面會在新視窗中開啟。 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>安裝 IIS 並取代後端 VM 上的預設 IIS 頁面

在每部後端伺服器上，使用 PowerShell 來安裝 IIS，並使用自訂頁面取代預設 IIS 網頁。

>[!NOTE]
>您也可以在 [伺服器管理員]  中使用 [新增角色及功能精靈]  來安裝 IIS。 

**若要安裝 IIS 並使用 PowerShell 更新預設網頁：**

1. 在 MyVM1 和 MyVM2 上，從 [開始]  功能表啟動 **Windows PowerShell**。 

2. 執行下列命令來安裝 IIS 並取代預設 IIS 網頁：
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. 選取 [中斷連線]  ，以關閉與 MyVM1 和 MyVM2 的 RDP 連線。 請勿關閉 VM。

### <a name="test-the-load-balancer"></a>測試負載平衡器

1. 在 MyTestVM 上，開啟 [Internet Explorer]  ，並且對任何組態提示回應 [確定]  。 
   
1. 在瀏覽器的網址列中貼上或輸入負載平衡器的私人 IP 位址 (*10.3.0.7*)。 
   
   自訂的 IIS Web 伺服器預設頁面會出現在瀏覽器中。 顯示的訊息如下：「來自 MyVM1 的 Hello World」  或「來自 MyVM2 的 Hello World」  。
   
1. 重新整理瀏覽器，以查看負載平衡器如何將流量分散於 VM。 您也可能需要在嘗試之間清除瀏覽器快取。

   有時候 **MyVM1** 頁面會出現，而有時候 **MyVM2** 頁面會出現，因為負載平衡器會將要求分散至每部後端 VM。 

   ![新的 IIS 預設網頁](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>清除資源

當您不再需要時，若要刪除負載平衡器和所有相關的資源，請開啟 **MyResourceGroupLB** 資源群組並選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立了基本層內部負載平衡器。 您還建立及設定了資源群組、後端伺服器、健康情況探查，以及負載平衡器的規則。 您已在後端 VM 上安裝 IIS，並使用測試 VM 在瀏覽器中測試負載平衡器。 

接下來，您可以了解如何跨越多個可用性區域為 VM 進行負載平衡。

> [!div class="nextstepaction"]
> [跨越多個可用性區域為 VM 進行負載平衡](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
