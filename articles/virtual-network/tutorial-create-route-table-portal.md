---
title: 路由網路流量 - 教學課程 - Azure 入口網站
titlesuffix: Azure Virtual Network
description: 在本教學課程中，您會了解如何使用 Azure 入口網站以路由表路由網路流量。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d630a41f9b83a852605ffad2a85ad6dd14bbac73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86079644"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站以路由表路由傳送網路流量

根據預設，Azure 會路由虛擬網路內所有子網路之間的流量。 您可以建立您自己的路由，以覆寫 Azure 的預設路由。 舉例來說，當您想要通過網路虛擬設備 (NVA) 路由傳送子網路之間的流量時，自訂路由便很有用。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立會路由傳送流量的 NVA
> * 建立路由表
> * 建立路由
> * 建立路由表與子網路的關聯
> * 將虛擬機器 (VM) 部署到不同子網路
> * 透過 NVA 從一個子網路將流量路由傳送到另一個子網路

本教學課程會使用 [Azure 入口網站](https://portal.azure.com)。 您也可以使用 [Azure CLI](tutorial-create-route-table-cli.md) 或 [Azure PowerShell](tutorial-create-route-table-powershell.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-nva"></a>建立 NVA

網路虛擬設備 (NVA) 是可協助路由和防火牆最佳化等網路功能的虛擬機器。 本教學課程假設您使用 **Windows Server 2016 Datacenter**。 如果您想要，您可以選取不同的作業系統。

1. 從 [Azure 入口網站](https://portal.azure.com)功能表或 [首頁] 頁面，選取 [建立資源]。

1. 選擇 [安全性] > [Windows Server 2016 Datacenter]。

    ![Windows Server 2016 Datacenter, 建立 VM, Azure 入口網站](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. 在 [建立虛擬機器] 頁面的 [基本資料] 底下，輸入或選取下列資訊：

    | 區段 | 設定 | 動作 |
    | ------- | ------- | ----- |
    | **專案詳細資料** | 訂用帳戶 | 選擇您的訂用帳戶。 |
    | | 資源群組 | 選取 [新建]，輸入 *myResourceGroup*，然後選取 [確定]。 |
    | **執行個體詳細資料** | 虛擬機器名稱 | 輸入 *myVmNva*。 |
    | | 區域 | 選擇 [(美國) 美國東部]。 |
    | | 可用性選項 | 選擇 [不需要基礎結構備援]。 |
    | | 映像 | 選擇 [Windows Server 2016 Datacenter]。 |
    | | 大小 | 保留預設值 [標準 DS1 v2]。 |
    | **系統管理員帳戶** | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | | 密碼 | 輸入您選擇的密碼，其長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 |
    | | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** | 公用輸入連接埠 | 挑選 [無]。 |
    | **節省費用** | 已經有 Windows Server 授權了嗎? | 挑選 [沒有]。 |

    ![基本資料, 建立虛擬機器, Azure 入口網站](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    然後，選取 **[下一步：磁碟 >]** 。

1. 在 [磁碟] 底下，選取適合您需求的設定，然後選取 [下一步：**網路 >]** 。

1. 在 [網路] 底下：

    1. 在 [虛擬網路] 中選取 [新建]。
    
    1. 在 [建立虛擬網路] 對話方塊的 [名稱] 底下，輸入「myVirtualNetwork」。

    1. 在 [位址空間] 中，將現有位址範圍取代為 [10.0.0.0/16]。

    1. 在 [子網路] 中，選取 [刪除] 圖示來刪除現有子網路，然後輸入下列 [子網路名稱] 和 [位址範圍] 的組合。 輸入有效的名稱和範圍之後，其下方就會出現新的空白資料列。

        | 子網路名稱 | 位址範圍 |
        | ----------- | ------------- |
        | *公開* | *10.0.0.0/24* |
        | *私用* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. 選取 [確定] 以結束對話方塊。

    1. 在 [子網路] 中，選擇 [DMZ (10.0.2.0/24)]。

    1. 此 VM 不會透過網際網路來連線，因此請在 [公用 IP] 中選擇 [無]。

    1. 選取 [下一步：**管理 >]** 。

1. 在 [管理] 底下：

    1. 在 [診斷儲存體帳戶] 中，選取 [新建]。
    
    1. 在 [建立儲存體帳戶] 對話方塊中，輸入或選取下列資訊：

        | 設定 | 值 |
        | ------- | ----- |
        | 名稱 | *mynvastorageaccount* |
        | 帳戶類型 | **儲存體 (一般用途 v1)** |
        | 效能 | **Standard** |
        | 複寫 | **本地備援儲存體 (LRS)** |
    
    1. 選取 [確定] 以結束對話方塊。

    1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，且 Azure 會驗證您的設定。

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

    建立 VM 需要幾分鐘的時間。 等候 Azure 建立好 VM。 [您的部署正在進行] 頁面會顯示您的部署詳細資料。

1. 您的 VM 準備就緒後，請選取 [前往資源]。

## <a name="create-a-route-table"></a>建立路由表

1. 從 [Azure 入口網站](https://portal.azure.com)功能表或 [首頁] 頁面，選取 [建立資源]。

2. 在搜尋方塊中，輸入「路由表」。 當搜尋結果中出現**路由表**出時加以選取。

3. 在 [路由表] 頁面中，選取 [建立]。

4. 在 [建立路由表] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | *myRouteTablePublic* |
    | 訂用帳戶 | 您的訂用帳戶 |
    | 資源群組 | **myResourceGroup** |
    | Location | **(美國) 美國東部** |
    | 虛擬網路閘道路由傳播 | **已啟用** |

    ![建立路由表, Azure 入口網站](./media/tutorial-create-route-table-portal/create-route-table.png)

5. 選取 [建立]。

## <a name="create-a-route"></a>建立路由

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的路由表。 搜尋並選取 [路由表]。

1. 挑選路由表的名稱 (**myRouteTablePublic**)。

1. 選擇 [路由] > [新增]。

    ![新增路由, 路由表, Azure 入口網站](./media/tutorial-create-route-table-portal/add-route.png)

1. 在 [新增路由] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 路由名稱 | *ToPrivateSubnet* |
    | 位址首碼 | *10.0.1.0/24* (稍早所建立「私人」子網路的位址範圍) |
    | 下一個躍點類型 | **虛擬設備** |
    | 下一個躍點位址 | *10.0.2.4* (DMZ 子網路位址範圍內的位址) |

1. 選取 [確定]。

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的虛擬網路。 搜尋並選取 [虛擬網路]。

1. 挑選虛擬網路的名稱 (**myVirtualNetwork**)。

1. 在虛擬網路的功能表列中，選擇 [子網路]。

1. 在虛擬網路的子網路清單中，選擇 [公用]。

1. 在 [路由表] 中，選擇您建立的路由表 (**myRouteTablePublic**)，然後選取 [儲存]，以將路由表關聯至「公用」子網路。

    ![關聯路由表, 子網路清單, 虛擬網路, Azure 入口網站](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>開啟 IP 轉送

接下來，為新的 NVA 虛擬機器 (myVmNva) 開啟 IP 轉送。 當 Azure 將網路流量傳送至 *myVmNva* 時，如果流量的目的地為不同的 IP 位址，IP 轉送會將流量傳送至正確的位置。

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的 VM。 搜尋並選取 [虛擬機器]。

1. 挑選 VM 的名稱 (**myVmNva**)。

1. 在 NVA 虛擬機器的功能表列中，選取 [網路]。

1. 選取 [myvmnva123]。 這是 Azure 為您的 VM 建立的網路介面。 Azure 會加上數字以確保名稱是唯一的。

    ![網路, 網路虛擬設備 (NVA) 虛擬機器 (VM), Azure 入口網站](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. 在 [網路介面] 功能表列中，選取 [IP 設定]。

1. 在 [IP 設定] 頁面上，將 [IP 轉送] 設定為 [啟用]，然後選取 [儲存]。

    ![啟用 IP 轉送, IP 設定, 網路介面, 網路虛擬設備 (NVA) 虛擬機器 (VM), Azure 入口網站](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>建立公用和私人虛擬機器

在虛擬網路中建立公用 VM 和私人 VM。 您稍後將用它們來檢視 Azure 透過 NVA 將「公用」子網路的流量路由至「私人」子網路。

若要建立公用 VM 和私人 VM，請遵循稍早[建立 NVA](#create-an-nva)的步驟。 您不需要等待部署完成或移至 VM 資源。 您將會使用大部分的相同設定，但下面所述設定除外。

在選取 [建立] 以建立公用或私人 VM 之前，請先移至下列兩個子區段 ([公用 VM](#public-vm) 和[私人 VM](#private-vm))，以了解必須不同的值。 在 Azure 完成這兩個 VM 的部署後，您可以繼續進行下一節 ([透過 NVA 路由傳送流量](#route-traffic-through-an-nva))。

### <a name="public-vm"></a>公用 VM

| 索引標籤 | 設定 | 值 |
| --- | ------- | ----- |
| 基本概念 | 資源群組 | **myResourceGroup** |
| | 虛擬機器名稱 | *myVmPublic* |
| | 公用輸入連接埠 | **允許選取的連接埠** |
| | 選取輸入連接埠 | **RDP** |
| 網路功能 | 虛擬網路 | **myVirtualNetwork** |
| | 子網路 | **公用 (10.0.0.0/24)** |
| | 公用 IP 位址 | 預設值 |
| 管理性 | 診斷儲存體帳戶 | **mynvastorageaccount** |

### <a name="private-vm"></a>私人 VM

| 索引標籤 | 設定 | 值 |
| --- | ------- | ----- |
| 基本概念 | 資源群組 | **myResourceGroup** |
| | 虛擬機器名稱 | *myVmPrivate* |
| | 公用輸入連接埠 | **允許選取的連接埠** |
| | 選取輸入連接埠 | **RDP** |
| 網路功能 | 虛擬網路 | **myVirtualNetwork** |
| | 子網路 | **私人 (10.0.1.0/24)** |
| | 公用 IP 位址 | 預設值 |
| 管理性 | 診斷儲存體帳戶 | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>透過 NVA 路由傳送流量

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>透過遠端桌面登入 myVmPrivate

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的私人 VM。 搜尋並選取 [虛擬機器]。

1. 挑選私人 VM 的名稱 (**myVmPrivate**)。

1. 在 VM 的功能表列中選取 [連線]，以建立與私人 VM 的遠端桌面連線。

1. 在 [使用 RDP 連線] 頁面上，選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 *.rdp* 檔案。 如果出現提示，請選取 [連接]。 選取 [更多選擇] > [使用其他帳戶]，然後輸入您在建立私人 VM 時所指定的使用者名稱和密碼。

1. 選取 [確定]。

1. 如果您在登入程序進行期間收到憑證警告，請選取 [是] 以連線到 VM。

### <a name="enable-icmp-through-the-windows-firewall"></a>讓 ICMP 可通過 Windows 防火牆

在後續步驟中，您將使用追蹤路由工具來測試路由。 追蹤路由會使用網際網路控制訊息通訊協定 (ICMP)，但 Windows 防火牆依預設會加以拒絕。 讓 ICMP 可通過 Windows 防火牆。

1. 在 myVmPrivate 的遠端桌面中，開啟 PowerShell。

1. 輸入此命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    在本教學課程中，您將使用追蹤路由來測試路由。 在生產環境中，我們不建議允許 ICMP 通過 Windows 防火牆。

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>在 myVmNva 內開啟 IP 轉送

您已使用 Azure 為 VM 的網路介面[開啟 IP 轉送](#turn-on-ip-forwarding)。 VM 的作業系統也必須轉送網路流量。 請使用下列命令為 *myVmNva* VM 的作業系統開啟 IP 轉送。

1. 從 myVmPrivate VM 上的命令提示字元，開啟 myVmNva VM 的遠端桌面：

    ```cmd
    mstsc /v:myvmnva
    ```

1. 從 myVmNva VM 上的 PowerShell，輸入下列命令以開啟 IP 轉送：

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. 重新啟動 myVmNva VM：從工作列中，選取 [啟動] > [電源]、[其他 (已計劃)] > [繼續]。

    這也會中斷遠端桌面工作階段的連線。

1. 在 myVmNva VM 重新啟動後，建立 myVmPublic VM 的遠端桌面工作階段。 在與 myVmPrivate VM 保持連線的情況下，開啟命令提示字元，並執行此命令：

    ```cmd
    mstsc /v:myVmPublic
    ```
1. 在 myVmPublic 的遠端桌面中，開啟 PowerShell。

1. 輸入下列命令，讓 ICMP 可通過 Windows 防火牆：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>測試網路流量的路由

首先，我們將測試從 myVmPublic VM 到 myVmPrivate VM 的網路流量路由。

1. 從 myVmPublic VM 上的 PowerShell，輸入此命令：

    ```powershell
    tracert myVmPrivate
    ```

    回應如下列範例所示：

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    您可以看到第一個躍點是 10.0.2.4，也就是 NVA 的私人 IP 位址。 第二個躍點是 myVmPrivate VM 的私人 IP 位址：10.0.1.4。 您先前已將路由新增至 myRouteTablePublic 路由表，並建立該路由表與公用子網路的關聯。 因此，Azure 會透過 NVA 傳送流量，而不是直接傳送至私人子網路。

1. 關閉 myVmPublic 虛擬機器的遠端桌面工作階段，但您仍然與 myVmPrivate 虛擬機器連線。

1. 從 myVmPrivate VM 上的命令提示字元，輸入此命令：

    ```cmd
    tracert myVmPublic
    ```

    此命令會測試從 myVmPrivate VM 到 myVmPublic VM 的網路流量路由。 回應如下列範例所示：

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    您可以看到 Azure 將流量直接從 myVmPrivate VM 路由至 myVmPublic VM。 根據預設，Azure 會直接路由傳送子網路之間的流量。

1. 關閉 myVmPrivate 虛擬機器的遠端桌面工作階段。

## <a name="clean-up-resources"></a>清除資源

不再需要資源群組時，請刪除 myResourceGroup 及其擁有的所有資源：

1. 移至 [Azure 入口網站](https://portal.azure.com)，以管理您的資源群組。 搜尋並選取 [資源群組]。

1. 挑選資源群組的名稱 (**myResourceGroup**)。

1. 選取 [刪除資源群組]。

1. 在 [確認] 對話方塊的 [輸入資源群組名稱] 中，輸入「myResourceGroup」，然後選取 [刪除]。 Azure 便會刪除 myResourceGroup 以及繫結至該資源群組的所有資源，包括路由表、儲存體帳戶、虛擬網路、VM、網路介面和公用 IP 位址。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立路由表，並將其與子網路產生關聯。 您已建立簡單的 NVA，它會將來自公用子網路的流量路由傳送至私人子網路。 現在您可以從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) 部署不同的預先設定 NVA，以獲得許多有用的網路功能。 若要深入了解路由，請參閱[路由概觀](virtual-networks-udr-overview.md)和[管理路由表](manage-route-table.md)。

雖然您可以在虛擬網路內部署許多 Azure 資源，但 Azure 無法將某些 PaaS 服務的資源部署到虛擬網路中。 您可以限制對某些 Azure PaaS 服務的資源存取，但能限制的只有來自虛擬網路子網路的流量。 若要了解如何限制對 Azure PaaS 資源的網路存取，請參閱下一個教學課程。

> [!div class="nextstepaction"]
> [限制對 PaaS 資源的網路存取](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Azure 服務成本費用。 Azure 成本管理可協助您設定預算和設定警示以控制費用。 使用成本管理來分析、管理和最佳化您的 Azure 成本。 若要深入了解，請參閱[分析成本的快速入門](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。