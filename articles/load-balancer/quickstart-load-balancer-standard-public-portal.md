---
title: 快速入門：建立公用負載平衡器 - Azure 入口網站
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure 入口網站建立負載平衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: eab8298362bfb3ad790d13fcbf47e0fe624ed3fd
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470185"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立負載平衡器以平衡 VM 的負載

負載平衡會將傳入要求分散於多部虛擬機器，藉此提供高可用性和範圍。 您可使用 Azure 入口網站建立負載平衡器，以平衡虛擬機器 (VM) 的負載。 本快速入門示範如何使用公用負載平衡器來平衡 VM 的負載。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-load-balancer"></a>建立負載平衡器

在本節中，您會建立負載平衡器，協助平衡虛擬機器的負載。 您可以建立公用 Load Balancer 或內部 Load Balancer。 當您建立公用 Load Balancer 時，也必須建立新的公用 IP 位址，而該 IP 位址會設定為負載平衡器的前端 (預設的名稱為 LoadBalancerFrontend  )。

1. 在畫面的左上方，選取 [建立資源]   > [網路]   > [負載平衡器]  。
2. 在 [建立負載平衡器]  頁面的 [基本資料]  中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [檢閱 + 建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]  ，並在文字方塊中輸入 *myResourceGroupSLB*。|
    | 名稱                   | *myLoadBalancer*                                   |
    | 區域         | 選取 [西歐]  。                                        |
    | 類型          | 選取 [公用]  。                                        |
    | SKU           | 選取 [標準]  或 [基本]  。 Microsoft 建議對生產工作負載使用「標準」。 |
    | 公用 IP 位址 | 選取 [建立新的]  。 如果您有想要使用的現有公用 IP，請選取 [使用現有]  。 |
    | 公用 IP 位址名稱              | 在文字方塊中輸入 *myPublicIP*。   使用 ```-SKU Basic``` 來建立基本公用 IP。 基本公用 IP 與 **標準**負載平衡器不相容。 Microsoft 建議對生產工作負載使用都需要**標準**。|
    | 可用性區域 | 輸入「區域備援」  以建立復原性 Load Balancer。 若要建立區域性 Load Balancer，請從 1、2 或 3 選取特定區域 |

> [!IMPORTANT]
> 本快速入門的其餘部分假設在上述 SKU 選取程序期間會選擇**標準** SKU。


3. 在 [檢閱 + 建立]  索引標籤中，選取 [建立]  。   

    ![建立標準負載平衡器](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您會設定後端位址集區的負載平衡器設定、健康狀態探查，並指定平衡器規則。

### <a name="create-a-backend-pool"></a>建立後端集區

若要將流量分散至 VM，後端位址集區包含已連線至負載平衡器之虛擬 (NIC) 的 IP 位址。 建立後端位址集區 *myBackendPool*，以包含用於平衡網際網路流量負載的虛擬機器。

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單中選取 **myLoadBalancer**。
2. 在 [設定]  底下選取 [後端集區]  ，然後選取 [新增]  。
3. 在 [新增後端集區]  頁面上，針對名稱輸入 *myBackEndPool* 作為後端集區的名稱，然後選取 [新增]  。

### <a name="create-a-health-probe"></a>建立健康狀態探查

若要讓負載平衡器監視應用程式的狀態，請使用健康狀態探查。 健康狀態探查會根據 VM 對健康情況檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 建立健康狀態探查 myHealthProbe  以監視 VM 的健康狀態。

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單中選取 **myLoadBalancer**。
2. 在 [設定]  底下選取 [健康狀態探查]  ，然後選取 [新增]  。
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *myHealthProbe*。 |
    | 通訊協定 | 選取 [HTTP]  。 |
    | 連接埠 | 輸入 *80*。|
    | 間隔 | 輸入 *15* 作為探查嘗試之間的 [間隔]  秒數。 |
    | 狀況不良臨界值 | 選取 [2]  作為 [狀況不良閾值]  的數值，或將 VM 視為狀況不良之前，必須達到的連續探查失敗次數。|
    | | |
4. 選取 [確定]  。

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則
負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 建立負載平衡器規則 *myLoadBalancerRuleWeb*，用來接聽前端 *FrontendLoadBalancer* 中的連接埠 80，以及用來將負載平衡的網路流量傳送到後端位址集區 *myBackEndPool* (也是使用連接埠 80)。 

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單中選取 **myLoadBalancer**。
2. 在 [設定]  下選取 [負載平衡規則]  ，然後選取 [新增]  。
3. 使用下列值來設定負載平衡規則：
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *myHTTPRule*。 |
    | 通訊協定 | 選取 [TCP]  。 |
    | 連接埠 | 輸入 *80*。|
    | 後端連接埠 | 輸入 *80*。 |
    | 後端集區 | 選取 [myBackendPool]  。|
    | 健全狀況探查 | 選取 [myHealthProbe]  。 |
4. 保留其餘的預設值，然後選取 [確定]  。


## <a name="create-backend-servers"></a>建立後端伺服器

在本節中，您會建立一個虛擬網路、針對 Load Balancer 的後端集區建立三部虛擬機器，然後在虛擬機器上安裝 IIS 以協助測試 Load Balancer。

### <a name="create-a-virtual-network"></a>建立虛擬網路
1. 在畫面的左上方，選取 [建立資源]   > [網路]   > [虛擬網路]  。

1. 在 [建立虛擬網路]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *myVNet*。 |
    | 位址空間 | 輸入 *10.1.0.0/16*。 |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取現有的資源 - *myResourceGroupSLB*。 |
    | Location | 選取 [西歐]  。|
    | 子網路 - 名稱 | 輸入 *myBackendSubnet*。 |
    | 子網路 - 位址範圍 | 輸入 *10.1.0.0/24*。 |
1. 保留其餘的預設值，然後選取 [建立]  。

### <a name="create-virtual-machines"></a>建立虛擬機器
公用 IP SKU 和 Load Balancer SKU 必須相符。 對於標準負載平衡器，使用後端集區中具有標準 IP 位址的 VM。 在本節中，您將使用三個不同區域 (區域 1  、區域 2  及區域 3  ) 中的標準公用 IP 位址來建立三部 VM (*myVM1*、*myVM2* 及 *myVM3*)，這些區域後續會新增至先前建立的 Load Balancer 後端集區。 如果選取了 [基本]，請使用具有基本 IP 位址的 VM。

1. 在入口網站的左上方，選取 [建立資源]   >  **[計算]**  > [Windows Server 2019 Datacenter]  。 
   
1. 在 [建立虛擬機器]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：
   - [訂用帳戶]   > [資源群組]  ：選取 [myResourceGroupSLB]  。
   - **執行個體詳細資料** > **虛擬機器名稱**：輸入 *myVM1*。
   - [執行個體詳細資料]   > [區域]  > 選取 [歐洲西部]  。
   - [執行個體詳細資料]   > [可用性選項]  > 選取 [可用性區域]  。 
   - [執行個體詳細資料]   > [可用性區域]  > 選取 [1]  。
   - **系統管理員帳戶**> 輸入 [使用者名稱]  、[密碼]  和 [確認密碼]  資訊。
   - 選取 [網路]  索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。
  
1. 在 [網路]  索引標籤中，確定已選取下列項目：
   - **虛擬網路**：*MyVNet*
   - **子網路**：*MyBackendSubnet*
   - [公用 IP]  > 選取 [新建]  ，然後在 [建立公用 IP 位址]  視窗中，針對 [SKU]  選取 [標準]  ，並針對 [可用性區域]  選取 [區域備援]  ，然後選取 [確定]  。 如果建立了基本 Load Balancer，請選取 [基本]。 Microsoft 建議對生產工作負載使用標準 SKU。
   - 若要在 [網路安全性群組]  之下建立新的網路安全性群組 (NSG，一種防火牆類型)，請選取 [進階]  。 
       1. 在 [設定網路安全性群組]  欄位中，選取 [新建]  。 
       1. 輸入 *myNetworkSecurityGroup*，然後選取 [確定]  。
   - 若要使 VM 成為負載平衡器後端集區的一部分，請完成下列步驟：
        - 在 [負載平衡]  中，針對 [要將此虛擬機器放在現有負載平衡解決方案後面嗎?]  選取 [是]  。
        - 在 [負載平衡設定]  中，針對 [負載平衡選項]  選取 [Azure Load Balancer]  。
        - 針對 [選取負載平衡器]  選取 [myLoadBalancer]  。
        - 選取 [管理]  索引標籤，或選取 [下一步]   > [管理]  。
2. 在 [管理]  索引標籤的 [監視]  下方，將 [開機診斷]  設為 [關閉]  。 
1. 選取 [檢閱 + 建立]  。   
1. 檢閱設定，然後選取 [建立]  。
1. 依照步驟 2 到 6，使用下列值建立兩個額外的 VM (其他所有設定則與 *myVM1* 相同)：

    | 設定 | VM 2| VM 3|
    | ------- | ----- |---|
    | 名稱 |  *myVM2* |*myVM3*|
    | 可用性區域 | 2 |3|
    |公用 IP| **標準** SKU|**標準** SKU|
    | 公用 IP - 可用性區域| **區域備援** |**區域備援**|
    | 網路安全性群組 | 選取現有的 *myNetworkSecurity 群組*| 選取現有的 *myNetworkSecurity 群組*|

 ### <a name="create-nsg-rule"></a>建立 NSG 規則

在本節中，您會建立網路安全性群組規則，以允許使用 HTTP 的輸入連線。

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單選取 **myResourceGroupSLB** 資源群組中的 [myNetworkSecurityGroup]  。
2. 在 [設定]  下，選取 [輸入安全性規則]  ，然後選取 [新增]  。
3. 輸入輸入安全性規則 (名為 myHTTPRule  ) 的下列值，以允許使用連接埠 80 的輸入 HTTP 連線：
    - **來源**：服務標記 
    -  **來源服務標記**：*網際網路*
    - **目的地連接埠範圍**：*80*
    - **通訊協定**：*TCP*
    - **動作**：*允許*
    - **優先順序**：*100*
    - **名稱**：*myHTTPRule* 
    - **描述**：允許 HTTP  
4. 選取 [新增]  。
5. 使用下列不同的值，視需要針對輸入 RDP 規則重複這些步驟：
   - **目的地連接埠範圍**：輸入 3389  。
   - **優先順序**：輸入 200  。 
   - **Name**：輸入 MyRDPRule  。 
   - **描述**：輸入「允許 RDP」  。 
 
### <a name="install-iis"></a>安裝 IIS

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單選取 *myResourceGroupSLB* 資源群組中的 [myVM1]  。
2. 在 [概觀]  頁面上，選取 [連線]  以透過 RDP 連入 VM。
5. 使用您在此 VM 建立期間提供的認證登入 VM。 這會啟動虛擬機器的遠端桌面工作階段 - *myVM1*。
6. 在伺服器桌面上，瀏覽至 [Windows 系統管理工具]  >[Windows PowerShell]  。
7. 在 PowerShell 視窗中，執行下列命令以安裝 IIS 伺服器、移除預設 iisstart.htm 檔案，然後新增會顯示 VM 名稱的 iisstart.htm 檔案：

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. 使用 myVM1  關閉 RDP 工作階段。
7. 重複步驟 1 到 6，在 myVM2  和 myVM3  上安裝 IIS 和更新的 iisstart.htm 檔案。

## <a name="test-the-load-balancer"></a>測試負載平衡器
1. 在 [概觀]  畫面上尋找負載平衡器的公用 IP 位址。 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後選取 [myPublicIP]  。

2. 將公用 IP 位址複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

   ![IIS Web 伺服器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、負載平衡器和所有相關資源。 若要這樣做，請選取包含負載平衡器的資源群組 (*myResourceGroupSLB*)，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立標準負載平衡器、將 VM 連結到標準負載平衡器、設定負載平衡器流量規則、健康狀態探查，然後測試負載平衡器。 若要深入了解 Azure Load Balancer，請繼續進行 [Azure 負載平衡器教學課程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)。

深入了解 [Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。
