---
title: 建立虛擬網路 -快速入門 - Azure 入口網站
titleSuffix: Azure Virtual Network
description: 快速入門：在 Azure 入口網站中建立虛擬網路。 這些網路可讓各種 Azure 資源 (例如 VM) 安全地互相通訊，也可與網路通訊。
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79214786"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立虛擬網路

在此快速入門中，您將了解如何使用 Azure 入口網站建立虛擬網路。 您將部署兩部虛擬機器 (VM)。 接下來，您可以在 VM 之間安全地進行通訊，並從網際網路連線到 VM。 虛擬網路是私人網路在 Azure 中的基本建置組塊。 其可讓 Azure 資源 (例如 VM) 安全地彼此通訊，以及與網際網路通訊。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 從 Azure 入口網站功能表選取 [建立資源]  。 從 Azure Marketplace 選取 [網路]   > [虛擬網路]  。

1. 在 [建立虛擬網路]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]  ，輸入 *myResourceGroup*，然後選取 [確定]  。 |
    | 名稱 | 輸入 *myVirtualNetwork*。 |
    | Location | 選取 [美國東部]  。|

1. 完成時，選取 [下一步:  IP 位址]，並在 [IPv4 位址空間]  中輸入 10.1.0.0/16  。

1. 選取 [新增子網路]  ，然後輸入 myVirtualSubnet  作為 [子網路名稱]  ，以及輸入 10.1.0.0/24  作為 [子網路位址範圍]  。

1. 選取 [新增]  ，然後選取 [檢閱 + 建立]  。 將其他項目保留為預設值，然後選取 [建立]  。

1. 在 [建立虛擬網路]  中，選取 [建立]  。

## <a name="create-virtual-machines"></a>建立虛擬機器

在虛擬網路內建立兩個 VM：

### <a name="create-the-first-vm"></a>建立第一個 VM

1. 從 Azure 入口網站功能表選取 [建立資源]  。

1. 從 Azure Marketplace 選取 [計算]   > [Windows Server 2019 資料中心]  。 選取 [建立]  。

1. 在 [建立虛擬機器 - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此資源群組。 |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 *myVm1*。 |
    | 區域 | 選取 [美國東部]  。 |
    | 可用性選項 | 預設值為 [不需要基礎結構備援]  。 |
    | 映像 | 預設值為 [Windows Server 2019 Datacenter]  。 |
    | 大小 | 預設值為 [標準 DS1 v2]  。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 重新輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]  。 |
    | 選取輸入連接埠 | 輸入 HTTP (80)  和 RDP (3389)  。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 預設值為 [否]  。 |

1. 完成時，選取 [下一步:  磁碟]。

1. 在 [建立虛擬機器 - 磁碟]  中，保留預設值並選取 [下一步：  網路功能]。

1. 在 [建立虛擬機器 - 網路]  中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 預設值為 [myVirtualNetwork]  。 |
    | 子網路 | 預設值為 [myVirtualSubnet (10.1.0.0/24)]  。 |
    | 公用 IP | 預設值為 [(新) myVm-ip]  。 |
    | NIC 網路安全性群組 | 預設值為 [基本]  。 |
    | 公用輸入連接埠 | 預設值為 [允許選取的連接埠]  。 |
    | 選取輸入連接埠 | 預設值為 [HTTP]  和 [RDP]  。

1. 完成時，選取 [下一步:  管理]。

1. 在 [建立虛擬機器 - 管理]  中，針對 [診斷儲存體帳戶]  ，選取 [新建]  。

1. 在 [建立儲存體帳戶]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *myvmstorageaccount*。 如果此名稱已被使用，請建立唯一名稱。|
    | 帳戶類型 | 預設值為 [儲存體 (一般用途 v1)]  。 |
    | 效能 | 預設值為 [標準]  。 |
    | 複寫 | 預設值為 [本地備援儲存體 (LRS)]  。 |

1. 選取 [確定]  ，選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

### <a name="create-the-second-vm"></a>建立第二個 VM

重複上一節中的程序，以建立另一個虛擬機器。

> [!IMPORTANT]
> 針對 [虛擬機器名稱]  輸入 *myVm2*。
>
> 在步驟 7 中，針對 [診斷儲存體帳戶]  ，務必選取 [myvmstorageaccount]  。

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

在您建立 *myVm1* 之後，請連線到網際網路。

1. 在 Azure 入口網站中，搜尋並選取 [myVm1]  。

1. 依序選取 [連線]  和 [RDP]  。

    ![連接到虛擬機器](./media/quick-create-portal/connect-to-virtual-machine.png)

    [連線]  頁面會隨即開啟。

1. 選取 [下載 RDP 檔案]  。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟 RDP 檔案。 如果出現提示，請選取 [連接]  。

1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

    > [!NOTE]
    > 您可能需要選取 [其他選擇]   > [使用不同的帳戶]  ，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]  。

1. 您可能會在登入時收到憑證警告。 如果您收到憑證警告，請選取 [是]  或 [繼續]  。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。

## <a name="communicate-between-vms"></a>虛擬機器之間的通訊

1. 在 *myVm1* 的遠端桌面中，開啟 PowerShell。

1. 輸入 `ping myVm2`。

    您將收到類似此輸出的訊息：

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    `ping` 失敗，因為 `ping` 使用網際網路控制訊息通訊協定 (ICMP)。 根據預設，ICMP 不允許通過 Windows 防火牆。

1. 為了讓 *myVm2* 在稍後的步驟中可以 Ping *myVm1*，輸入此命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    此命令可讓 ICMP 連入流量通過 Windows 防火牆：

1. 關閉對 *myVm1* 的遠端桌面連線。

1. 再次完成[從網際網路連線至 VM](#connect-to-a-vm-from-the-internet) 中的步驟，但連線至 *myVm2*。

1. 從命令提示字元，輸入 `ping myvm1`。

    您會收到類似此訊息：

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    您會從 *myVm1* 收到回覆，因為您在步驟 3 中允許 ICMP 通過 *myVm1* VM 上的 Windows 防火牆。

1. 關閉對 *myVm2* 的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源

在此快速入門中，您建立了一個預設的虛擬網路和兩個 VM。 您從網際網路連線到其中一部 VM，然後在兩部 VM 之間安全地進行通訊。

當您完成使用虛擬網路與 VM 時，可以刪除資源群組以及其包含的所有資源：

1. 搜尋並選取 [myResourceGroup]  。

1. 選取 [刪除資源群組]  。

1. 針對 [輸入資源群組名稱]  輸入 *myResourceGroup*，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬網路設定，請參閱[建立、變更或刪除虛擬網路](manage-virtual-network.md)。

根據預設，Azure 允許 VM 之間進行安全通訊。 Azure 只允許從網際網路連線到 Windows VM 的輸入遠端桌面連線。 若要深入了解 VM 網路通訊的類型，請參閱[篩選網路流量](tutorial-filter-network-traffic.md)。
