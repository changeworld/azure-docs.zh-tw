---
title: 教學課程：VM 的負載平衡網際網路流量 - Azure 入口網站
titleSuffix: Azure Load Balancer
description: 本教學課程說明如何使用 Azure 入口網站建立和管理標準負載平衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "80240370"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站針對網際網路至 VM 的流量進行負載平衡

負載平衡會將傳入要求分散於多部虛擬機器，藉此提供高可用性和範圍。 在本教學課程中，您會了解 Azure Standard Load Balancer 有哪些不同的元件可分散網際網路至 VM 的流量並提供高可用性。 您會了解如何：


> [!div class="checklist"]
> * 建立 Azure Load Balancer
> * 建立負載平衡器資源
> * 建立虛擬機器並安裝 IIS 伺服器
> * 檢視作用中的 Load Balancer
> * 從 Load Balancer 中新增和移除 VM

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-standard-load-balancer"></a>建立標準負載平衡器

在本節中，您會建立標準負載平衡器，以協助平衡虛擬機器的負載。 標準負載平衡器只支援標準公用 IP 位址。 當您建立標準負載平衡器時，您也必須建立新的標準公用 IP 位址，而該 IP 位址會設定為標準負載平衡器的前端 (預設的名稱為 LoadBalancerFrontend  )。 

1. 在畫面的左上方，按一下 [建立資源]   > [網路]   > [負載平衡器]  。
2. 在 [建立負載平衡器]  頁面的 [基本資料]  中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [檢閱 + 建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]  ，並在文字方塊中輸入 *myResourceGroupSLB*。|
    | 名稱                   | *myLoadBalancer*                                   |
    | 區域         | 選取 [西歐]  。                                        |
    | 類型          | 選取 [公用]  。                                        |
    | SKU           | 選取 [標準]  。                          |
    | 公用 IP 位址 | 選取 [建立新的]  。 |
    | 公用 IP 位址名稱              | 在文字方塊中輸入 *myPublicIP*。   |
    |可用性區域| 選取 [區域備援]  。    |

3. 在 [檢閱 + 建立]  索引標籤中，按一下 [建立]  。

   ![建立標準負載平衡器](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您會設定後端位址集區的負載平衡器設定、健康狀態探查，並指定平衡器規則。

### <a name="create-a-backend-address-pool"></a>建立後端位址集區

若要將流量分散至 VM，後端位址集區包含已連線至負載平衡器之虛擬 (NIC) 的 IP 位址。 建立後端位址集區 *myBackendPool*，以包含用於平衡網際網路流量負載的虛擬機器。

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單中按一下 [myLoadBalancer]  。
2. 在 [設定]  之下，依序按一下 [後端集區]  和 [新增]  。
3. 在 [新增後端集區]  頁面上，針對名稱輸入 *myBackEndPool* 作為後端集區的名稱，然後選取 [新增]  。

### <a name="create-a-health-probe"></a>建立健康狀態探查

若要讓負載平衡器監視應用程式的狀態，請使用健康狀態探查。 健康狀態探查會根據 VM 對健康情況檢查的回應，以動態方式從負載平衡器輪替中新增或移除 VM。 建立健康狀態探查 myHealthProbe  以監視 VM 的健康狀態。

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單中按一下 [myLoadBalancer]  。
2. 在 [設定]  之下，依序按一下 [健康狀態探查]  和 [新增]  。
3. 使用下列值來建立健康狀態探查：
     
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *myHealthProbe*。 |
    | 通訊協定 | 選取 [HTTP]  。 |
    | 連接埠 | 輸入 *80*。|
    | 間隔 | 輸入 *15* 作為探查嘗試之間的 [間隔]  秒數。 |
    | 狀況不良臨界值 | 選取 [2]  作為 [狀況不良閾值]  的數值，或將 VM 視為狀況不良之前，必須達到的連續探查失敗次數。|
    
4. 選取 [確定]  。

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 建立負載平衡器規則 *myLoadBalancerRuleWeb*，用來接聽前端 *FrontendLoadBalancer* 中的連接埠 80，以及用來將負載平衡的網路流量傳送到後端位址集區 *myBackEndPool* (也是使用連接埠 80)。

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單中按一下 [myLoadBalancer]  。
2. 在 [設定]  之下，按一下 [負載平衡規則]  ，然後按一下 [新增]  。
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

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB (選取現有的資源群組) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西歐      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>建立虛擬機器

標準負載平衡器只支援在後端集區中具有標準 IP 位址的 VM。 在本節中，您將使用三個不同區域 (區域 1**、區域 2** 及區域 3**) 中的標準公用 IP 位址來建立三部 VM (*myVM1*、*myVM2* 及 *myVM3*)，這些區域已新增至稍早建立的 Standard Load Balancer 後端集區。

1. 在入口網站的左上方，選取 [建立資源] >  **[計算]**  > [Windows Server 2016 Datacenter]。 
   
1. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的下列值：
   - [訂用帳戶]**** > [資源群組]****：選取 [myResourceGroupSLB]****。
   - **執行個體詳細資料** > **虛擬機器名稱**：輸入 *myVM1*。
   - [執行個體詳細資料]**** > [區域]**** > 選取 [歐洲西部]****。
   - [執行個體詳細資料]**** > [可用性選項]**** > 選取 [可用性區域]****。 
   - [執行個體詳細資料]**** > [可用性區域]**** > 選取 [1]****。
  
1. 選取 [網路] 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。 
   
   - 請確定已選取下列項目：
       - **虛擬網路**：**MyVNet**
       - **子網路**：**MyBackendSubnet**
       - [公用 IP]**** > 選取 [新建]****，然後在 [建立公用 IP 位址]**** 視窗中，針對 [SKU]**** 選取 [標準]****，並針對 [可用性區域]**** 選取 [區域備援]****
      
   - 若要在 [網路安全性群組]**** 之下建立新的網路安全性群組 (NSG，一種防火牆類型)，請選取 [進階]****。 
       1. 在 [設定網路安全性群組]**** 欄位中，選取 [新建]****。 
       1. 輸入 *myNetworkSecurityGroup*，然後選取 [確定]****。

   - 若要使 VM 成為負載平衡器後端集區的一部分，請完成下列步驟：
        - 在 [負載平衡]**** 中，針對 [要將此虛擬機器放在現有負載平衡解決方案後面嗎?]**** 選取 [是]****。
        - 在 [負載平衡設定]**** 中，針對 [負載平衡選項]**** 選取 [Azure Load Balancer]****。
        - 針對 [選取負載平衡器]**** 選取 [myLoadBalancer]**。 
1. 選取 [管理] 索引標籤，或選取 [下一步] > [管理]。 在 [監視] 下，將 [開機診斷] 設定為 [關閉]。 
1. 選取 [檢閱 + 建立]。   
1. 檢閱設定，然後選取 [建立]。
1. 依照下列步驟，分別使用**可用性區域** **2** 和 **3** 中的標準 SKU 公用 IP 位址來建立兩部額外的 VM (*myVM2* 和 *myVM3*)，而所有其他設定則與 *myVM1* 相同。  

### <a name="create-network-security-group-rule"></a>建立網路安全性群組規則

在本節中，您會建立網路安全性群組規則，以允許使用 HTTP 的輸入連線。

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中按一下 [myNetworkSecurityGroup]****，其位於 **myResourceGroupSLB** 資源群組中。
2. 在 [設定] 底下，按一下 [輸入安全性規則]，然後按一下 [新增]。
3. 輸入輸入安全性規則 (名為 myHTTPRule) 的下列值，以允許使用連接埠 80 的輸入 HTTP 連線：
    - 服務標記 - 作為 [來源]。
    - 網際網路 - 作為 [來源服務標記]
    - 80 - 作為 [目的地連接埠範圍]
    - TCP - 作為 [通訊協定]
    - 允許 - 作為 [動作]
    - 100 作為 [優先順序]
    - myHTTPRule 作為名稱
    - 允許 HTTP - 作為描述
4. 選取 [新增]  。

### <a name="install-iis-on-vms"></a>在 VM 上安裝 IIS

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中按一下 [myVM1]****，其位於 *myResourceGroupSLB* 資源群組中。
2. 在 [概觀] 頁面上，按一下 [連線] 以透過 RDP 連入 VM。
3. 在 [連線至虛擬機器]**** 快顯視窗中選取 [下載 RDP 檔案]****，然後開啟下載的 RDP 檔案。
4. 在 [遠端桌面連線]**** 視窗中，按一下 [連線]****。
5. 使用您在此 VM 建立期間提供的認證登入 VM。 這會啟動虛擬機器的遠端桌面工作階段 - *myVM1*。
6. 在伺服器桌面上，瀏覽至 [Windows 系統管理工具]****>[Windows PowerShell]****。
7. 在 PowerShell 視窗中，執行下列命令以安裝 IIS 伺服器、移除預設 iisstart.htm 檔案，然後新增會顯示 VM 名稱的 iisstart.htm 檔案：

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. 使用 myVM1 關閉 RDP 工作階段。
7. 重複步驟 1 到 6，在 myVM2** 和 myVM3** 上安裝 IIS 和更新的 iisstart.htm 檔案。

## <a name="test-the-load-balancer"></a>測試負載平衡器
1. 在 [概觀]**** 畫面上尋找負載平衡器的公用 IP 位址。 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後按一下 [myPublicIP]****。

2. 將公用 IP 位址複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

      ![IIS Web 伺服器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要查看 Load Balancer 如何將流量分散於這三部執行您應用程式的 VM，您可以強制重新整理網頁瀏覽器。

## <a name="remove-or-add-vms-from-the-backend-pool"></a>從後端集區移除或新增 VM
您可能需要在執行您應用程式的 VM 上執行維護，例如安裝 OS 更新。 若要處理您應用程式增加的流量，您可能需要新增額外的 VM。 本節示範如何從 Load Balancer 中移除或新增 VM (*myVM1*)。

### <a name="remove-vm-from-a-backend-pool"></a>從後端集區移除 VM
若要從後端集區移除 *myVM1*，請完成下列步驟：

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中按一下 [myLoadBalancer]****。
2. 在 [設定]**** 下按一下 [後端集區]****，然後在後端集區的清單中，按一下 [myBackendPool]****。
3. 在 [myBackendPool]**** 頁面上，若要移除 *VM1*，請選取顯示 *myVM1* 之資料列結尾的 [刪除] 圖示，然後按一下 [儲存]****。

當 myVM1** 已不在後端位址集區中之後，您即可對 myVM1** 執行任何維護工作，例如安裝軟體更新。 在沒有 *VM1* 的情況下，負載現在會在 *myVM2* 與 *myVM3* 之間取得平衡。 

### <a name="add-vm-to-a-backend-pool"></a>將 VM 新增至後端集區
若要將 *myVM1* 新增回後端集區，請完成下列步驟：

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中選取 [myVM1]****。
2. 在 [VM1]**** 頁面的 [設定]**** 下方，選取 [網路]****。
3. 在 [網路]**** 頁面上，選取 [負載平衡]**** 索引標籤，然後選取 [新增負載平衡]****。
4. 在 [新增負載平衡]**** 頁面中，執行下列動作：
   1. 針對 [負載平衡選項]**** 選取 [Azure Load Balancer]****。
   2. 針對 [選取負載平衡器]**** 選取 [myLoadBalancer]**。
   3. 針對 [選取後端集區]**** 選取 [MyBackendPool]**。 

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除資源群組、Load Balancer 和所有相關的資源。 若要這樣做，請選取包含 Load Balancer 的 *myResouceGroupSLB* 資源群組，然後選取 [刪除]****。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 Standard Load Balancer、將 VM 與其連結、設定 Load Balancer 流量規則、健康狀態探查，接著測試了 Load Balancer。 您也已從負載平衡集中移除 VM，並重新將該 VM 新增至後端位址集區。 若要深入了解 Azure Load Balancer，請繼續 Azure Load Balancer 的教學課程。

> [!div class="nextstepaction"]
> [Azure Load Balancer 教學課程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
