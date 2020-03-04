---
title: 使用 Azure 私人連結連線至 Azure Cosmos 帳戶
description: 瞭解如何藉由建立私人端點，從 VM 安全地存取 Azure Cosmos 帳戶。
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252592"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>使用 Azure 私人連結私下連接到 Azure Cosmos 帳戶

Azure 私用端點是 Azure 中私人連結的基本建立區塊。 它可讓 Azure 資源（例如虛擬機器（Vm））私下與私人連結資源進行通訊。

在本文中，您將瞭解如何在 Azure 虛擬網路上建立 VM，以及如何使用 Azure 入口網站在私人端點上建立 Azure Cosmos 帳戶。 然後，您可以從 VM 安全地存取 Azure Cosmos 帳戶。

## <a name="sign-in-to-azure"></a>登入 Azure

登入[Azure 入口網站。](https://portal.azure.com)

## <a name="create-a-vm"></a>建立 VM

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您將建立虛擬網路和子網，以裝載用來存取私人連結資源的 VM （在此範例中為 Azure Cosmos 帳戶）。

在本節中，您將需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<的資源群組名稱 >**  | myResourceGroup|
| **\<虛擬網路-名稱 >** | myVirtualNetwork         |
| **\<區功能變數名稱稱 >**          | 美國中西部     |
| **\<IPv4-位址-空間 >**   | 10.1.0.0 \ 16          |
| **\<子網名稱 >**          | mySubnet        |
| **\<子網-位址範圍 >** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>建立虛擬機器

1. 在 Azure 入口網站 畫面的左上方，選取 **建立資源** > **計算** > **虛擬機器**。

1. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVm。 |
    | 區域 | 選取 [WestCentralUS]。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]。 |
    | 影像 | 選取 [Windows Server 2019 Datacenter]。 |
    | 大小 | 保留預設值 [標準 DS1 v2]。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | Password | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 重新輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設值 [無]。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]。 |
    |||

1. 選取 **[下一步：磁片]** 。

1. 在 [**建立虛擬機器-磁片**] 中保留預設值，然後選取 **[下一步：網路]** 。

1. 在 [建立虛擬機器 - 網路] 中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設值 [MyVirtualNetwork]。  |
    | 位址空間 | 保留預設值 [10.1.0.0/24]。|
    | 子網路 | 保留預設值 [mySubnet (10.1.0.0/24)]。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]。 |
    | 選取輸入連接埠 | 選取 [HTTP] 和 [RDP]。|
    ||

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

## <a name="create-an-azure-cosmos-account"></a>建立 Azure Cosmos 帳戶

建立[Azure COSMOS SQL API 帳戶](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)。 為了簡單起見，您可以在與其他資源相同的區域（也就是 "WestCentralUS"）中建立 Azure Cosmos 帳戶。

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>建立 Azure Cosmos 帳戶的私人端點

建立 Azure Cosmos 帳戶的私人連結，如使用連結文章的[Azure 入口網站一節建立私人連結](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal)中所述。

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm。

1. 選取 [連線] 按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 *.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 **[確定]** 。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>從 VM 私下存取 Azure Cosmos 帳戶

在本節中，您會使用私用端點，私下連線至 Azure Cosmos 帳戶。 

1. 若要包含 IP 位址和 DNS 對應，請登入您的虛擬機器*myVM*，開啟 `c:\Windows\System32\Drivers\etc\hosts` 檔案，並以下列格式包含上一個步驟中的 DNS 資訊：

   [私人 IP 位址][帳戶端點]. 檔. azure .com

   **範例︰**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. 在*myVM*的遠端桌面中，安裝[Microsoft Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。

1. 選取 [ **Cosmos DB 帳戶（預覽）** ]，並按一下滑鼠右鍵。

1. 選取 **[連線至 Cosmos DB]** 。

1. 選取 [API]。

1. 貼入先前複製的資訊，以輸入連接字串。

1. 選取 [下一步]。

1. 選取 [連接]。

1. 從*mycosmosaccount*流覽 Azure Cosmos 資料庫和容器。

1. （選擇性）將新的專案加入至*mycosmosaccount*。

1. 關閉對*myVM*的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源

當您使用私人端點、Azure Cosmos 帳戶和 VM 完成時，請刪除資源群組及其包含的所有資源： 

1. 在入口網站頂端的 [搜尋] 方塊中輸入 **myResourceGroup**，然後從搜尋結果中選取 [myResourceGroup]。

1. 選取 [刪除資源群組]。

1. 針對 [輸入資源群組名稱] 輸入 **myResourceGroup**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本文中，您已在虛擬網路、Azure Cosmos 帳戶和私人端點上建立 VM。 您已從網際網路連線至 VM，並使用私人連結安全地與 Azure Cosmos 帳戶通訊。

* 若要深入瞭解私用端點，請參閱[什麼是 Azure 私人端點？](private-endpoint-overview.md)。

* 若要深入瞭解搭配 Azure Cosmos DB 使用私人端點的限制，請參閱[Azure Cosmos DB 文章的 Azure 私用連結](../cosmos-db/how-to-configure-private-endpoints.md)。
