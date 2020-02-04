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
ms.date: 01/22/2019
ms.author: kumud
ms.openlocfilehash: 96b6788e48b845ef7f0add11767eb36b47cac36b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775272"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站以路由表路由傳送網路流量

根據預設，Azure 會路由虛擬網路內所有子網路之間的流量。 您可以建立您自己的路由，以覆寫 Azure 的預設路由。 舉例來說，如果您想要通過網路虛擬設備 (NVA) 路由傳送子網路之間的流量，則建立自訂路由的能力很有幫助。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立路由表
> * 建立路由
> * 建立有多個子網路的虛擬網路
> * 建立路由表與子網路的關聯
> * 建立會路由傳送流量的 NVA
> * 將虛擬機器 (VM) 部署到不同子網路
> * 透過 NVA 從一個子網路將流量路由傳送到另一個子網路

您可以依偏好使用 [Azure CLI](tutorial-create-route-table-cli.md) 或 [Azure PowerShell](tutorial-create-route-table-powershell.md) 完成本教學課程。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-route-table"></a>建立路由表

1. 從 Azure 入口網站功能表選取 [建立資源]  。
2. 在搜尋方塊中，輸入「路由表」  。 當搜尋結果中出現**路由表**出時加以選取。
3. 在 [路由表]  頁面中，選取 [建立]  。
4. 在 [建立路由表]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *myRouteTablePublic*。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 [新建]  ，輸入 *myResourceGroup*，然後選取 [確定]  。 |
    | Location | 選取 [美國東部]  。
    | 虛擬網路閘道路由傳播 | 保留預設值 [啟用]  。 |
5. 選取 [建立]  。

## <a name="create-a-route"></a>建立路由

1. 在入口網站的搜尋列中，輸入 *myRouteTablePublic*。

1. 當 **myRouteTablePublic** 出現在搜尋結果時，選取它。

1. 在 **myRouteTablePublic** 中的 [設定]  下方，選取 [路由]   > [+ 新增]  。

    ![新增路由](./media/tutorial-create-route-table-portal/add-route.png)

1. 在 [新增路由]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 路由名稱 | 輸入 *ToPrivateSubnet*。 |
    | 位址首碼 | 輸入 *10.0.1.0/24*。 |
    | 下一個躍點類型 | 選取 [虛擬設備]  。 |
    | 下一個躍點位址 | 輸入 *10.0.2.4*。 |

1. 選取 [確定]  。

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

您必須先建立虛擬網路和子網路，才能讓路由表與子網路產生關聯。

### <a name="create-a-virtual-network"></a>建立虛擬網路

1. 在畫面的左上方，選取 [建立資源]   > [網路]   > [虛擬網路]  。

1. 在 [建立虛擬網路]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *myVirtualNetwork*。 |
    | 位址空間 | 輸入 *10.0.0.0/16*。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 [選取現有的] > [myResourceGroup]  。 |
    | Location | 保留預設值 [美國東部]  。 |
    | 子網路 - 名稱 | 輸入*公用*。 |
    | 子網路 - 位址範圍 | 輸入 *10.0.0.0/24*。 |

1. 保留其餘的預設值，然後選取 [建立]  。

### <a name="add-subnets-to-the-virtual-network"></a>將子網路新增至虛擬網路

1. 在入口網站的搜尋列中，輸入 *myVirtualNetwork*。

1. 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。

1. 在 **myVirtualNetwork** 中的 [設定]  下方，選取 [子網路]   > [+ 子網路]  。

    ![新增子網路](./media/tutorial-create-route-table-portal/add-subnet.png)

1. 在 [新增子網路]  中，輸入這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入*私人*。 |
    | 位址空間 | 輸入 *10.0.1.0/24*。 |

1. 保留其餘的預設值，然後選取 [確定]  。

1. 再次選取 [+ 子網路]  。 這次請輸入這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *DMZ*。 |
    | 位址空間 | 輸入 *10.0.2.0/24*。 |

1. 和上次一樣，保留其餘的預設值，然後選取 [確定]  。

    Azure 會顯示三個子網路：**公用**、**私人**和 **DMZ**。

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>讓 myRouteTablePublic 與公用子網路產生關聯

1. 選取 [公用]  。

1. 在 [公用]  中，選取 [路由表]   > [MyRouteTablePublic]   > [儲存]  。

    ![關聯路由表](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>建立 NVA

NVA 是可協助您最佳化路由和防火牆等網路功能的 VM。 如果您想要，您可以選取不同的作業系統。 本教學課程假設您使用 **Windows Server 2016 Datacenter**。

1. 在畫面的左上方，選取 [建立資源]   >  **[計算]**  > [Windows Server 2016 Datacenter]  。

1. 在 [建立虛擬機器 - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 *myVmNva*。 |
    | 區域 | 選取 [美國東部]  。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]  。 |
    | 映像 | 保留預設值 [Windows Server 2016 Datacenter]  。 |
    | 大小 | 保留預設值 [標準 DS1 v2]  。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 選取 [無]  。
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]  。 |

1. 選取 [下一步：  磁碟]。

1. 在 [建立虛擬機器 - 磁碟]  中，選取最適合您個人需求的設定。

1. 選取 [下一步：  網路]。

1. 在 [建立虛擬機器 - 網路]  中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設值 [myVirtualNetwork]  。 |
    | 子網路 | 選取 [DMZ (10.0.2.0/24)]  。 |
    | 公用 IP | 選取 [無]  。 您不需要公用 IP 位址。 VM 不會透過網際網路連線。|

1. 保留其餘的預設值，然後選取 [下一步：  管理]。

1. 在 [建立虛擬機器 - 管理]  中，針對 [診斷儲存體帳戶]  ，選取 [新建]  。

1. 在 [建立儲存體帳戶]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *mynvastorageaccount*。 |
    | 帳戶類型 | 保留預設值 [儲存體 (一般用途 v1)]  。 |
    | 效能 | 保留預設值 [標準]  。 |
    | 複寫 | 保留預設值 [本地備援儲存體 (LRS)]  。

1. 選取 [確定] 

1. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，且 Azure 會驗證您的設定。

1. 當您看到 [驗證成功]  時，請選取 [建立]  。

    建立 VM 需要幾分鐘的時間。 在 VM 建立完成前，請勿繼續操作。 [您的部署正在進行]  頁面會顯示您的部署詳細資料。

1. 您的 VM 準備就緒後，請選取 [前往資源]  。

## <a name="turn-on-ip-forwarding"></a>開啟 IP 轉送

為 *myVmNva* 開啟 IP 轉送。 當 Azure 將網路流量傳送至 *myVmNva* 時，如果流量的目的地為不同的 IP 位址，IP 轉送會將流量傳送至正確的位置。

1. 在 **myVmNva** 的 [設定]  下方，選取 [網路]  。

1. 選取 [myvmnva123]  。 這是 Azure 為您的 VM 建立的網路介面。 它會有唯一的數字字串供您明確識別。

    ![VM 網路功能](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. 在 [設定]  下方，選取 [IP 組態]  。

1. 在 [myvmnva123 - IP 組態]  上，針對 [IP 轉送]  選取 [啟用]  ，然後選取 [儲存]  。

    ![啟用 IP 轉送](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>建立公用和私人虛擬機器

在虛擬網路中建立公用 VM 和私人 VM。 您稍後將用它們來檢視 Azure 透過 NVA 將「公用」  子網路的流量路由至「私人」  子網路。

完成[建立 NVA](#create-an-nva) 的步驟 1-12。 所使用的設定大致相同。 必須不同的值如下：

| 設定 | 值 |
| ------- | ----- |
| **公用 VM** | |
| 基本 |  |
| 虛擬機器名稱 | 輸入 *myVmPublic*。 |
| 網路 | |
| 子網路 | 選取 [公用 (10.0.0.0/24)]  。 |
| 公用 IP 位址 | 接受預設值。 |
| 公用輸入連接埠 | 選取 [允許選取的連接埠]  。 |
| 選取輸入連接埠 | 選取 [HTTP]  和 [RDP]  。 |
| 管理 | |
| 診斷儲存體帳戶 | 保留預設值 [mynvastorageaccount]  。 |
| **私人 VM** | |
| 基本 |  |
| 虛擬機器名稱 | 輸入 *myVmPrivate*。 |
| 網路 | |
| 子網路 | 選取 [私人 (10.0.1.0/24)]  。 |
| 公用 IP 位址 | 接受預設值。 |
| 公用輸入連接埠 | 選取 [允許選取的連接埠]  。 |
| 選取輸入連接埠 | 選取 [HTTP]  和 [RDP]  。 |
| 管理 | |
| 診斷儲存體帳戶 | 保留預設值 [mynvastorageaccount]  。 |

您可以在 Azure 建立 myVmPublic  虛擬機器的時候建立 myVmPrivate  虛擬機器。 在 Azure 完成兩部虛擬機器建立之前，請勿繼續進行其餘步驟。

## <a name="route-traffic-through-an-nva"></a>透過 NVA 路由傳送流量

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>透過遠端桌面登入 myVmPrivate

1. 在入口網站的搜尋列中，輸入 *myVmPrivate*。

1. 當 **myVmPrivate** 虛擬機器出現在搜尋結果中時，請加以選取。

1. 選取 [連線]  ，以建立對 *myVmPrivate* VM 的遠端桌面連線。

1. 在 [連線至虛擬機器]  中，選取 [下載 RDP 檔案]  。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 *.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]  。

    1. 輸入您在建立私人 VM 時指定的使用者名稱和密碼。

    1. 您可能需要選取 [其他選擇]   > [使用不同的帳戶]  ，以使用私人 VM 認證。

1. 選取 [確定]  。

    您可能會在登入過程中收到憑證警告。

1. 選取 [是]  以連線至 VM。

### <a name="enable-icmp-through-the-windows-firewall"></a>讓 ICMP 可通過 Windows 防火牆

在後續步驟中，您將使用追蹤路由工具來測試路由。 追蹤路由會使用網際網路控制訊息通訊協定 (ICMP)，但 Windows 防火牆依預設會加以拒絕。 讓 ICMP 可通過 Windows 防火牆。

1. 在 myVmPrivate  的遠端桌面中，開啟 PowerShell。

1. 輸入此命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    在本教學課程中，您將使用追蹤路由來測試路由。 在生產環境中，我們不建議允許 ICMP 通過 Windows 防火牆。

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>在 myVmNva 內開啟 IP 轉送

您已使用 Azure 為 VM 的網路介面[開啟 IP 轉送](#turn-on-ip-forwarding)。 VM 的作業系統也必須轉送網路流量。 請使用下列命令為 *myVmNva* VM 的作業系統開啟 IP 轉送。

1. 從 myVmPrivate  VM 上的命令提示字元，開啟 myVmNva  VM 的遠端桌面：

    ```cmd
    mstsc /v:myvmnva
    ```

1. 從 myVmNva  上的 PowerShell，輸入下列命令以開啟 IP 轉送：

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. 重新啟動 myVmNva  VM。 在工作列中，選取 [啟動] 按鈕   > [電源] 按鈕  、[其他 (計劃性)]   > [繼續]  。

    這也會中斷遠端桌面工作階段的連線。

1. 在 myVmNva  VM 重新啟動後，建立 myVmPublic  VM 的遠端桌面工作階段。 在與 myVmPrivate  VM 保持連線的情況下，開啟命令提示字元，並執行此命令：

    ```cmd
    mstsc /v:myVmPublic
    ```
1. 在 myVmPublic  的遠端桌面中，開啟 PowerShell。

1. 輸入下列命令，讓 ICMP 可通過 Windows 防火牆：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>測試網路流量的路由

首先，我們將測試從 myVmPublic  VM 到 myVmPrivate  VM 的網路流量路由。

1. 從 myVmPublic  VM 上的 PowerShell，輸入此命令：

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

    您可以看到第一個躍點是 10.0.2.4。 這是 NVA 的私人 IP 位址。 第二個躍點是 myVmPrivate  VM 的私人 IP 位址：10.0.1.4。 您先前已將路由新增至 myRouteTablePublic  路由表，並建立該路由表與公用  子網路的關聯。 因此，Azure 會透過 NVA 傳送流量，而不是直接傳送至私人  子網路。

1. 關閉 myVmPublic  虛擬機器的遠端桌面工作階段，但您仍然與 myVmPrivate  虛擬機器連線。

1. 從 myVmPrivate  VM 上的命令提示字元，輸入此命令：

    ```cmd
    tracert myVmPublic
    ```

    這會測試從 myVmPrivate  VM 到 myVmPublic  VM 的網路流量路由。 回應如下列範例所示：

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    您可以看到 Azure 將流量直接從 myVmPrivate  VM 路由至 myVmPublic  VM。 根據預設，Azure 會直接路由傳送子網路之間的流量。

1. 關閉 myVmPrivate  虛擬機器的遠端桌面工作階段。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，請將資源群組及其包含的所有資源刪除：

1. 在入口網站的搜尋列中，輸入 myResourceGroup  。

1. 當您在搜尋結果中看到 myResourceGroup  時，請加以選取。

1. 選取 [刪除資源群組]  。

1. 針對 [輸入資源群組名稱:]  輸入 myResourceGroup  ，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立路由表，並將其與子網路產生關聯。 您已建立簡單的 NVA，它會將來自公用子網路的流量路由傳送至私人子網路。 您已了解其作法，現在您可以從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) 部署不同的預先設定 NVA。 其中包含許多您會認為很有用的網路功能。 若要深入了解路由，請參閱[路由概觀](virtual-networks-udr-overview.md)和[管理路由表](manage-route-table.md)。

雖然您可以在虛擬網路內部署許多 Azure 資源，但 Azure 無法將某些 PaaS 服務的資源部署到虛擬網路中。 您可以限制對某些 Azure PaaS 服務所含資源的存取。 但此類限制必須是來自虛擬網路子網路的流量。 若要了解如何限制對 Azure PaaS 資源的網路存取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [限制對 PaaS 資源的網路存取](tutorial-restrict-network-access-to-resources.md)
