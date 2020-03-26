---
title: 教學課程：設定連接埠轉送 - Azure 入口網站
titleSuffix: Azure Load Balancer
description: 本教學課程說明如何使用 Azure Load Balancer 設定連接埠轉送，以對 Azure 虛擬網路中的 VM 建立連線。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: e740a65d453a69a987e938a5170ae8e04c7bfe40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78249871"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>教學課程：使用入口網站在 Azure Load Balancer 中設定連接埠轉送

連接埠轉送可讓您使用 Azure Load Balancer 公用 IP 位址和連接埠號碼連線到 Azure 虛擬網路中的虛擬機器 (VM)。 

在本教學課程中，您會設定 Azure Load Balancer 上的連接埠轉送。 您會了解如何：

> [!div class="checklist"]
> * 建立公用標準負載平衡器來平衡 VM 間的網路流量。 
> * 使用網路安全性群組 (NSG) 規則建立虛擬網路和 VM。 
> * 將 VM 新增至負載平衡器的後端位址集區。
> * 建立負載平衡器健康情況探查和流量規則。
> * 建立負載平衡器輸入 NAT 連接埠轉送規則。
> * 在 VM 上安裝及設定 IIS 以檢視作用中的負載平衡與連接埠轉送。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

若要取得本教學課程中的所有步驟，請登入 Azure 入口網站，網址為：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-standard-load-balancer"></a>建立標準負載平衡器

首先，建立公用標準負載平衡器，以平衡 VM 間的流量負載。 標準負載平衡器只支援標準公用 IP 位址。 當您建立標準負載平衡器時，您也會建立新的標準公用 IP 位址，而該 IP 位址會設定為負載平衡器的前端 (預設的名稱為 **LoadBalancerFrontEnd**)。 

1. 在畫面的左上方，按一下 [建立資源]   > [網路]   > [負載平衡器]  。
2. 在 [建立負載平衡器]  頁面的 [基本資料]  中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [檢閱 + 建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]  ，並在文字方塊中輸入 *MyResourceGroupLB*。|
    | 名稱                   | *myLoadBalancer*                                   |
    | 區域         | 選取 [西歐]  。                                        |
    | 類型          | 選取 [公用]  。                                        |
    | SKU           | 選取 [標準]  。                          |
    | 公用 IP 位址 | 選取 [建立新的]  。 |
    | 公用 IP 位址名稱              | 在文字方塊中輸入 *myPublicIP*。   |
    |可用性區域| 選取 [區域備援]  。    |
     
    >[!NOTE]
     >務必在支援可用性區域的位置中建立您的 Load Balancer 和其所有資源。 如需詳細資訊，請參閱[支援可用性區域的區域](../availability-zones/az-overview.md#services-support-by-region)。 

3. 在 [檢閱 + 建立]  索引標籤中，按一下 [建立]  。  
  
## <a name="create-and-configure-back-end-servers"></a>建立及設定後端伺服器

建立一個虛擬網路以及兩部虛擬機器，並將 VM 新增至負載平衡器的後端集區。 

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (選取現有的資源群組) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西歐      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>建立 VM 並將其新增至負載平衡器後端集區

1. 在入口網站的左上方，選取 [建立資源]   >  **[計算]**  > [Windows Server 2016 Datacenter]  。 
   
1. 在 [建立虛擬機器]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：
   - [訂用帳戶]   > [資源群組]  ：下拉並選取 [MyResourceGroupLB]  。
   - **虛擬機器名稱**：輸入 MyVM1  。
   - **區域**：選取 [西歐]  。 
   - **使用者名稱**：輸入 azureuser  。
   - **密碼**：輸入 Azure1234567  。 
     在 [確認密碼]  欄位中重新輸入密碼。
   
1. 選取 [網路]  索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。 
   
   請確定已選取下列項目：
   - **虛擬網路**：**MyVNet**
   - **子網路**：**MyBackendSubnet**
   
1. 在 [公用 IP]  下選取 [新建]  ，然後在 [建立公用 IP 位址]  頁面上選取 [標準]  ，最後選取 [確定]  。 
   
1. 在 [網路安全性群組]  之下選取 [進階]  ，即可建立新的網路安全性群組 (NSG，一種防火牆類型)。 
   1. 在 [設定網路安全性群組]  欄位中，選取 [新建]  。 
   1. 輸入 MyNetworkSecurityGroup  ，然後選取 [確定]  。 
   
   >[!NOTE]
   >請注意，根據預設，NSG 已有用於開啟連接埠 3389 (遠端桌面 (RDP) 連接埠) 的傳入規則。
   
1. 您可以將 VM 新增至您建立的負載平衡器後端集區：
   
   1. 在 [負載平衡]   > [將此虛擬機器放置到現有負載平衡解決方案後方？]  底下，選取 [是]  。 
   1. 針對 [負載平衡選項]  ，下拉並選取 [Azure Load Balancer]  。 
   1. 針對 [選取負載平衡器]  ，下拉並選取 [MyLoadBalancer]  。 
   1. 在 [選取後端集區]  底下選取 [新建]  ，然後輸入 [MyBackendPool]  並選取 [建立]  。 
   
   ![建立虛擬網路](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. 選取 [管理]  索引標籤，或選取 [下一步]   > [管理]  。 在 [監視]  下，將 [開機診斷]  設定為 [關閉]  。
   
1. 選取 [檢閱 + 建立]  。
   
1. 檢閱設定，並在驗證成功時選取 [建立]  。 

1. 請依照下列步驟來建立名為 *MyVM2* 的第二個 VM，其所有其他設定與 MyVM1 相同。 
   
   針對 [網路安全性群組]  ，請在選取 [進階]  之後下拉並選取您已建立的 **MyNetworkSecurityGroup**。 
   
   請確定 [選取後端集區]  底下的 [MyBackendPool]  已選取。 

### <a name="create-an-nsg-rule-for-the-vms"></a>建立 VM 的 NSG 規則

建立 VM 的網路安全性群組 (NSG) 規則，以允許輸入的網際網路 (HTTP) 連線。

>[!NOTE]
>根據預設，NSG 已有用於開啟連接埠 3389 (遠端桌面 (RDP) 連接埠) 的規則。

1. 在左側功能表上，選取 [所有資源]  。 從資源清單，選取 **MyResourceGroupLB** 資源群組中的 **MyNetworkSecurityGroup**。
   
1. 在 [設定]  下，選取 [輸入安全性規則]  ，然後選取 [新增]  。
   
1. 在 [新增輸入安全性規則]  對話方塊中，輸入或選取下列值：
   
   - **來源**：選取 [服務標記]  。  
   - **來源服務標記**：選取 [網際網路]  。 
   - **目的地連接埠範圍**：輸入 80  。
   - **通訊協定**：選取 [TCP]  。 
   - **動作**：選取 [允許]  。  
   - **優先順序**：輸入 100  。 
   - **Name**：輸入 MyHTTPRule  。 
   - **描述**：輸入「允許 HTTP」  。 
   
1. 選取 [新增]  。 
   
   ![建立 NSG 規則](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您將檢查負載平衡器後端集區，並設定負載平衡器健康情況探查與流量規則。

### <a name="view-the-back-end-address-pool"></a>檢視後端位址集區

若要將流量分散至 VM，負載平衡器會使用後端位址集區，其中包含已連線至負載平衡器的虛擬網路介面 (NIC) IP 位址。 

您已建立負載平衡器後端集區，並在建立 VM 時，將 VM 新增至此。 您也可以建立後端集區，然後從負載平衡器的 [後端集區]  頁面中新增或移除 VM。 

1. 選取左側功能表上的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  底下選取 [後端集區]  。
   
1. 在 [後端集區]  頁面上，展開 **MyBackendPool**，並確定 **VM1** 和 **VM2** 都已列出。

1. 選取 [MyBackendPool]  。 
   
   在 [MyBackendPool]  頁面上的 [虛擬機器]  和 [IP 位址]  下，您可以在集區中移除或新增可用 VM。

您可以在 [後端集區]  頁面上選取 [新增]  來建立新的後端集區。

### <a name="create-a-health-probe"></a>建立健康狀態探查

若要讓負載平衡器能監視 VM 狀態，請使用健康情況探查。 健康狀態探查會根據 VM 對健康狀態檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 

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
   
   ![新增探查](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則可定義如何將流量分散至 VM。 此規則會定義連入流量的前端 IP 組態、用來接收流量的後端 IP 集區，以及所需的來源和目的地連接埠。 

名為 **MyLoadBalancerRule** 的負載平衡器規則會接聽前端 **LoadBalancerFrontEnd** 中的連接埠 80。 此規則會將網路流量傳送到後端位址集區 **MyBackendPool**，也在連接埠 80 上。 

1. 選取左側功能表上的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  下選取 [負載平衡規則]  ，然後選取 [新增]  。
   
1. 在 [新增負載平衡規則]  頁面上，輸入或選取下列值：
   
   - **Name**：輸入 MyLoadBalancerRule  。
   - **通訊協定**：選取 [TCP]  。
   - **連接埠**：輸入 80  。
   - **後端連接埠**：輸入 80  。
   - **後端集區**：選取 [MyBackendPool]  。
   - **健康情況探查**：選取 [MyHealthProbe]  。 
   
1. 選取 [確定]  。
   
   ![新增負載平衡器規則](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>建立輸入 NAT 連接埠轉送規則

建立負載平衡器的輸入網路位址轉譯 (NAT) 規則，將流量從前端 IP 位址的特定連接埠轉送至後端 VM 的特定連接埠。

1. 選取左側功能表中的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  下，選取 [輸入 NAT 規則]  ，然後選取 [新增]  。 
   
1. 在 [新增輸入 NAT 規則]  頁面上，輸入或選取下列值：
   
   - **Name**：輸入 MyNATRuleVM1  。
   - **連接埠**：輸入 4221  。
   - **目標虛擬機器**：從下拉式清單選取 [MyVM1]  。
   - **網路 IP 設定**：從下拉式選單中選取 [ipconfig1]  。
   - **連接埠對應**：選取 [自訂]  。
   - **目標連接埠**：輸入 3389  。
   
1. 選取 [確定]  。
   
1. 重複步驟以新增名為 MyNATRuleVM2  的輸入 NAT 規則，請使用**連接埠**：4222  及**目標虛擬機器**：**MyVM2**。

## <a name="test-the-load-balancer"></a>測試負載平衡器

在本節中，您將在後端伺服器上安裝 Internet Information Services (IIS)，並自訂預設的網頁來顯示機器名稱。 然後，您將使用負載平衡器的公用 IP 位址來測試負載平衡器。 

每部後端 VM 都會提供不同版本的預設 IIS 網頁，以便您查看兩部 VM 之間的負載平衡器分散式要求。

### <a name="connect-to-the-vms-with-rdp"></a>使用 RDP 連線到 VM

使用遠端桌面 (RDP) 連線到每個 VM。 

1. 在入口網站的左側功能表上，選取 [所有資源]  。 從資源清單，選取 **MyResourceGroupLB** 資源群組中的每部 VM。
   
1. 在 [概觀]  頁面上，選取 [連線]  ，然後選取 [下載 RDP 檔案]  。 
   
1. 開啟所下載的 RDP 檔案，然後選取 [連線]  。
   
1. 在 [Windows 安全性] 畫面上，選取 [更多選擇]  ，然後選取 [使用不同的帳戶]  。 
   
   輸入使用者名稱 azureuser  和密碼 Azure1234567  ，然後選取 [確定]  。
   
1. 對任何憑證提示回應 [是]  。 
   
   VM 桌面會在新視窗中開啟。 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>安裝 IIS 並取代預設的 IIS 網頁 

使用 PowerShell 來安裝 IIS，並使用顯示 VM 名稱的頁面取代預設 IIS 網頁。

1. 在 MyVM1 和 MyVM2 上，從 [開始]  功能表啟動 **Windows PowerShell**。 

2. 執行下列命令來安裝 IIS 並取代預設 IIS 網頁：
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. 選取 [中斷連線]  ，以關閉與 MyVM1 和 MyVM2 的 RDP 連線。 請勿關閉 VM。

### <a name="test-load-balancing"></a>測試負載平衡

1. 在入口網站中 **MyLoadBalancer** 的 [概觀]  頁面上，於 [公用 IP 位址]  之下複製公用 IP 位址。 將滑鼠停在該位址上，然後選取 [複製]  圖示來複製它。 在此範例中為 **40.67.218.235**。 
   
1. 在網際網路瀏覽器的網址列中貼上或輸入負載平衡器的公用 IP 位址 (40.67.218.235  )。 
   
   自訂的 IIS Web 伺服器預設頁面會出現在瀏覽器中。 顯示的訊息如下：「來自 MyVM1 的 Hello World」  或「來自 MyVM2 的 Hello World」  。
   
   ![新的 IIS 預設網頁](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. 重新整理瀏覽器，以查看負載平衡器如何將流量分散於 VM。 有時候 **MyVM1** 頁面會出現，而有時候 **MyVM2** 頁面會出現，因為負載平衡器會將要求分散至每部後端 VM。
   
   >[!NOTE]
   >在每一次的嘗試之間，您可能需要清除瀏覽器快取，或開啟新的瀏覽器視窗。

## <a name="test-port-forwarding"></a>測試連接埠轉送

透過連接埠轉送，您可以使用負載平衡器的 IP 位址和 NAT 規則中所定義的前端連接埠值，以遠端桌面功能連線至後端 VM。 

1. 在入口網站中 **MyLoadBalancer** 的 [概觀]  頁面上，複製其公用 IP 位址。 將滑鼠停在該位址上，然後選取 [複製]  圖示來複製它。 在此範例中為 **40.67.218.235**。 
   
1. 開啟命令提示字元，使用下列命令及負載平衡器的公用 IP 位址和 VM NAT 規則中定義的前端連接埠，建立 MyVM2 的遠端桌面工作階段。 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. 開啟所下載的 RDP 檔案，然後選取 [連線]  。
   
1. 在 [Windows 安全性] 畫面上，選取 [更多選擇]  ，然後選取 [使用不同的帳戶]  。 
   
   輸入使用者名稱 azureuser  和密碼 Azure1234567  ，然後選取 [確定]  。
   
1. 對任何憑證提示回應 [是]  。 
   
   MyVM2 桌面會在新視窗中開啟。 

RDP 會連線成功，因為輸入 NAT 規則 **MyNATRuleVM2** 會將流量從負載平衡器的前端連接埠 4222 導向 MyVM2 的連接埠 3389 (RDP 連接埠)。

## <a name="clean-up-resources"></a>清除資源

當您不再需要時，若要刪除負載平衡器和所有相關的資源，請開啟 **MyResourceGroupLB** 資源群組並選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立了標準公用負載平衡器。 您還建立及設定了資源群組、後端伺服器、健康情況探查，以及負載平衡器的規則。 您已在後端 VM 上安裝 IIS，並使用負載平衡器的公用 IP 位址來測試負載平衡器。 您已設定並測試負載平衡器上所指定連接埠與後端 VM 上連接埠之間的連接埠轉送。 

若要深入了解 Azure Load Balancer，請繼續進行更多的負載平衡器教學課程。

> [!div class="nextstepaction"]
> [Azure Load Balancer 教學課程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
