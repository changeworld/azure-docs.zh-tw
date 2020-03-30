---
title: 在多個 IP 設定上進行負載平衡 - Azure 入口網站
titleSuffix: Azure Load Balancer
description: 在本文中，瞭解使用 Azure 門戶在主 IP 和輔助 IP 配置之間負載平衡。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076996"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>使用 Azure 入口網站在多個 IP 組態上進行負載平衡

> [!div class="op_single_selector"]
> * [入口網站](load-balancer-multiple-ip.md)
> * [電源外殼](load-balancer-multiple-ip-powershell.md)
> * [Cli](load-balancer-multiple-ip-cli.md)


在本文中，我們將說明如何對次要網路介面控制項 (NIC) 上的多個 IP 位址使用 Azure Load Balancer。 下圖說明我們的案例：

![負載平衡器案例](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

在我們的案例中，我們會使用下列設定：

- 執行 Windows 的兩部虛擬機器 (VM)。
- 每不 VM 都有主和次要 NIC。
- 每個次要 NIC 都有兩個 IP 組態。
- 每部 VM 都裝載兩個網站：contoso.com 和 fabrikam.com。
- 每個網站都會繫結到次要 NIC 上的一個 IP 組態。
- Azure Load Balancer 用來公開兩個前端 IP 位址 (每個網站一個)。 前端 IP 位址用於將流量分散至每個網站的各自 IP 組態。
- 前端 IP 位址和後端集區 IP 位址使用相同的連接埠號碼。

## <a name="prerequisites"></a>Prerequisites

我們的案例假設您有一個名為 **contosofabrikam** 的資源群組，其設定如下︰

- 此資源群組包含名為 **myVNet** 的虛擬網路。
- **myVNet** 網路包含名為兩個名為 **VM1** 和 **VM2** 的 VM。
- VM1 和 VM2 位於名為 **myAvailset** 的相同可用性設定組中。 
- VM1 與 VM2 分別有名為 **VM1NIC1** 和 **VM2NIC1** 的主要 NIC。 
- VM1 與 VM2 分別有名為 **VM1NIC2** 和 **VM2NIC2** 的次要 NIC。

如需如何建立具有多個 NIC 之 VM 的詳細資訊，請參閱[使用 PowerShell 建立具有多個 NIC 的 VM](../virtual-machines/windows/multiple-nics.md)。

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>在多個 IP 組態上執行負載平衡

請完成下列步驟來達成本文所述案例。

### <a name="step-1-configure-the-secondary-nics"></a>步驟 1︰設定次要 NIC

為虛擬網路中的每部 VM，新增次要 NIC 的 IP 組態︰  

1. 瀏覽至 Azure 入口網站：https://portal.azure.com。 使用您的 Azure 帳戶進行登入。

2. 選取畫面左上方的 [資源群組]**** 圖示。 然後選取 VM 所在的資源群組 (例如，**contosofabrikam**)。 [資源群組]**** 窗格會顯示 VM 的所有資源和 NIC。

3. 針對每部 VM 的次要 NIC，新增 IP 組態︰

    1. 選取您想要設定的次要 NIC。
    
    2. 選取 [IP 組態]****。 在下一個窗格中，選取頂端附近的 [新增]****。

    3. 在 [新增 IP 組態]**** 之下，對 NIC 新增第二個 IP 組態︰ 

        1. 輸入次要 IP 組態的名稱。 (例如，將 VM1 和 VM2 的 IP 組態分別命名為 **VM1NIC2-ipconfig2** 和 **VM2NIC2-ipconfig2**)。

        2. 針對 [私人 IP 位址]**** 的 [配置]**** 設定，選取 [靜態]****。

        3. 選取 [確定]****。

在次要 NIC 的第二個 IP 設定完成時，它會顯示在指定 NIC 的 [IP 組態]**** 設定之下。

### <a name="step-2-create-the-load-balancer"></a>步驟 2：建立負載平衡器

建立組態的負載平衡器：

1. 瀏覽至 Azure 入口網站：https://portal.azure.com。 使用您的 Azure 帳戶進行登入。

2. 在螢幕的左上角，選擇 **"創建資源** > **網路** > **負載平衡器**"。 接下來，選取 [建立]****。

3. 在 [建立負載平衡器]**** 之下，輸入負載平衡器的名稱。 在此案例中，我們會使用名稱 **mylb**。

4. **在公共 IP 位址**下，創建一個名為**PublicIP1**的新公共 IP。

5. 在 [資源群組]**** 底下，選取 VM 的現有資源群組 (例如，**contosofabrikam**)。 選取要部署負載平衡器的位置，然後選取 [確定]****。

開始部署負載平衡器。 部署需要幾分鐘的時間才能順利完成。 部署完成後，負載平衡器會顯示為資源群組中的資源。

### <a name="step-3-configure-the-front-end-ip-pool"></a>步驟 3︰設定前端 IP 集區

針對每個網站 (contoso.com 和 fabrikam.com)，設定負載平衡器上的前端 IP 集區︰

1. 在入口網站中，選取 [更多服務]****。 在篩選方塊中，輸入**公用 IP 位址**，然後選取 [公用 IP 位址]****。 在下一個窗格中，選取頂端附近的 [新增]****。

2. 為這兩個網站 (contoso.com 和 fabrikam.com) 設定兩個公用 IP 位址 (**PublicIP1** 和 **PublicIP2**)︰

   1. 輸入前端 IP 位址的名稱。

   2. 在 [資源群組]**** 中，選取 VM 的現有資源群組 (例如，**contosofabrikam**)。

   3. 在 [位置]**** 中選取和 VM 相同的位置。

   4. 選取 [確定]****。

      建立公用 IP 位址之後，它們便會顯示在 [公用 IP]**** 位址之下。

3. <a name="step3-3"></a>在入口網站中，選取 [更多服務]****。 在篩選方塊中，輸入**負載平衡器**，然後選取 [負載平衡器]****。 

4. 選擇要將前端 IP 池添加到的負載等化器 （**mylb**）。

5. 在 [設定]**** 底下選取 [前端 IP 組態]****。 在下一個窗格中，選取頂端附近的 [新增]****。

6. 輸入前端 IP 位址的名稱 (例如 **contosofe** 或 **fabrikamfe**)。

7. <a name="step3-7"></a>選擇**IP 位址**。 在 [選擇公用 IP 位址]**** 之下，選取前端的 IP 位址 (**PublicIP1** 或 **PublicIP2**)。

8. 重複本節中的<a href="#step3-3">步驟 3</a> 到<a href="#step3-7">步驟 7</a>，以建立第二個前端 IP 位址。

設定前端集區之後，IP 位址會顯示在負載平衡器的 [前端 IP 組態]**** 設定之下。 
    
### <a name="step-4-configure-the-back-end-pool"></a>步驟 4：設定後端集區

針對每個網站 (contoso.com 和 fabrikam.com)，設定負載平衡器上的後端位址集區︰
        
1. 在入口網站中，選取 [更多服務]****。 在篩選方塊中，輸入**負載平衡器**，然後選取 [負載平衡器]****。

2. 選取要新增後端集區的負載平衡器 (**mylb**)。

3. 在 **"設置"** 下，選擇 **"後端池**"。 鍵入後端池的名稱（例如 **，contosopool**或**fabrikampool）。** 在下一個窗格中，選取頂端附近的 [新增]****。 

4. 在 [關聯到]**** 中，選取 [可用性設定組]****。

5. 在 [可用性設定組]**** 中，選取 [myAvailset]****。

6. 為這兩個 VM 新增目標網路 IP 組態︰ 

    ![設定負載平衡器的後端集區](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. 對於**目標虛擬機器**，選擇要添加到後端池的 VM（例如**VM1**或**VM2）。**

    2. 在 [網路 IP 組態]**** 中，針對您在前一個步驟中選取的 VM，選取次要 NIC 的 IP 組態 (例如，**VM1NIC2-ipconfig2** 或 **VM2NIC2-ipconfig2**)。

7. 選取 [確定]****。

設定後端集區之後，位址會顯示在負載平衡器的 [後端集區]**** 設定之下。

### <a name="step-5-configure-the-health-probe"></a>步驟 5：設定健康狀態探查

設定負載平衡器的健康狀態探查：

1. 在入口網站中，選取 [更多服務]****。 在篩選方塊中，輸入**負載平衡器**，然後選取 [負載平衡器]****。

2. 選取要新增健康狀態探查的負載平衡器 (**mylb**)。

3. 在 [設定]**** 下，選取 [健康狀態探查]****。 在下一個窗格中，選取頂端附近的 [新增]****。 

4. 輸入健康狀態探查的名稱 (例如，**HTTP**)。 選取 [確定]****。

### <a name="step-6-configure-load-balancing-rules"></a>步驟 6：設定負載平衡規則

針對每個網站 (contoso.com 和 fabrikam.com)，設定負載平衡規則：
    
1. <a name="step6-1"></a>在 **"設置"** 下，選擇 **"負載平衡規則**"。 在下一個窗格中，選取頂端附近的 [新增]****。 

2. 在 [名稱]**** 中，輸入負載平衡規則的名稱 (例如，對 contoso.com 輸入 **HTTPc**，或對 fabrikam.com 輸入 **HTTPf**)。

3. 在 [前端 IP 位址]**** 中，選取您先前建立的前端 IP 位址 (例如，**contosofe** 或 **fabrikamfe**)。

4. 在 [連接埠]**** 和 [後端連接埠]**** 中，保留預設值 **80**。

5. 針對 [浮動 IP (伺服器直接回傳)]****，選取 [停用]****。

6. <a name="step6-6"></a>選擇 **"確定**"。

7. 重複本節中的<a href="#step6-1">步驟 1</a> 到<a href="#step6-6">步驟 6</a>，以建立第二個負載平衡器規則。

設定規則之後，它們會顯示在負載平衡器的 [負載平衡規則]**** 設定之下。

### <a name="step-7-configure-dns-records"></a>步驟 7︰設定 DNS 記錄

在最後一個步驟中，將 DNS 資源記錄設定為指向負載平衡器的個別前端 IP 位址。 您可以在 Azure DNS 中裝載網域。 如需搭配使用 Azure DNS 與 Load Balancer 的詳細資訊，請參閱[使用 Azure DNS 搭配其他 Azure 服務](../dns/dns-for-azure-services.md)。

## <a name="next-steps"></a>後續步驟
- 若要深入了解如何在 Azure 中合併負載平衡服務，請參閱[在 Azure 中使用負載平衡服務](../traffic-manager/traffic-manager-load-balancing-azure.md)。
- 瞭解如何使用不同類型的日誌來管理[Azure 負載等化器的 Azure 監視器日誌中的負載等化器](../load-balancer/load-balancer-monitor-log.md)。
