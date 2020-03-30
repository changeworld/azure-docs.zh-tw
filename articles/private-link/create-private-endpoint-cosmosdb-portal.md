---
title: 使用 Azure 專用連結連接到 Azure 宇宙帳戶
description: 瞭解如何通過創建專用終結點從 VM 安全地訪問 Azure Cosmos 帳戶。
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252592"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>使用 Azure 專用連結私下連接到 Azure 科斯莫斯帳戶

Azure 專用終結點是 Azure 中專用連結的基本構建基塊。 它使 Azure 資源（如虛擬機器 （VM））能夠與專用連結資源進行私下通信。

在本文中，您將瞭解如何使用 Azure 門戶在 Azure 虛擬網路和具有專用終結點的 Azure Cosmos 帳戶上創建 VM。 然後，可以從 VM 安全地訪問 Azure Cosmos 帳戶。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 Azure[門戶。](https://portal.azure.com)

## <a name="create-a-vm"></a>建立 VM

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您將創建一個虛擬網路和子網來承載用於訪問專用連結資源的 VM（本示例中的 Azure Cosmos 帳戶）。

在本節中，您需要將步驟中的以下參數替換為以下資訊：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<資源組名稱>**  | myResourceGroup|
| **\<虛擬網路名稱>** | myVirtualNetwork         |
| **\<區功能變數名稱稱>**          | 美國中西部     |
| **\<IPv4 位址空間>**   | 10.1.0.0\16          |
| **\<子網名稱>**          | mySubnet        |
| **\<子網位址範圍>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>建立虛擬機器

1. 在 Azure 門戶中螢幕的左上角，選擇 **"創建資源** > **計算** > **虛擬機器**"。

1. 在 [建立虛擬機器 - 基本]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選擇**我的資源組**。 您已在上一節中建立此項目。  |
    | **實例詳細資訊** |  |
    | 虛擬機器名稱 | 輸入 myVm**。 |
    | 區域 | 選取 [WestCentralUS]****。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]****。 |
    | 映像 | 選取 [Windows Server 2019 Datacenter]****。 |
    | 大小 | 保留預設值 [標準 DS1 v2]****。 |
    | **管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 重新輸入密碼。 |
    | **入站連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設值 [無]****。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]****。 |
    |||

1. 選擇 **"下一步"：磁片**。

1. 在 **"創建虛擬機器磁片"中**，保留預設值並選擇 **"下一步：網路**"。

1. 在 [建立虛擬機器 - 網路]**** 中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設**的"我的虛擬網路**"。  |
    | 位址空間 | 保留預設值 [10.1.0.0/24]****。|
    | 子網路 | 保留預設值 [mySubnet (10.1.0.0/24)]****。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]****。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]****。 |
    | 選取輸入連接埠 | 選取 [HTTP]**** 和 [RDP]****。|
    ||

1. 選擇 **"審閱" = 創建**。 您會移至 [檢閱 + 建立]**** 頁面，其中 Azure 會驗證您的設定。

1. 當您看到**驗證傳遞**的消息時，選擇 **"創建**"。

## <a name="create-an-azure-cosmos-account"></a>建立 Azure Cosmos 帳戶

創建[Azure 宇宙 SQL API 帳戶](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)。 為簡單起見，可以在與其他資源（即"WestCentralUS"）相同的區域創建 Azure Cosmos 帳戶。

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>為 Azure Cosmos 帳戶創建專用終結點

使用連結文章的 Azure 門戶部分為 Azure Cosmos 帳戶創建專用連結，如"[創建專用連結](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal)"中所述。

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm**，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm**。

1. 選取 [連線] **** 按鈕。 選取 [連線]**** 按鈕之後，隨即會開啟 [連線至虛擬機器]****。

1. 選擇**下載 RDP 檔**。 Azure 會建立一個「遠端桌面通訊協定」(*.rdp*) 檔案，並下載至您的電腦。

1. 打開下載的 *.rdp*檔。

    1. 如果出現提示，請選取 [連接]****。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選擇**更多選項** > **"使用其他帳戶**"來指定創建 VM 時輸入的憑據。

1. 選取 [確定]****。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是]**** 或 [繼續]****。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>從 VM 私下訪問 Azure 宇宙帳戶

在本節中，您將使用專用終結點私下連接到 Azure Cosmos 帳戶。 

1. 要包括 IP 位址和 DNS 映射，請登錄到虛擬機器*myVM，* 打開`c:\Windows\System32\Drivers\etc\hosts`該檔，並包含上一步中的 DNS 資訊，其格式如下：

   [私人 IP 位址][帳戶終結點]文檔.azure.com

   **例子：**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. 在*myVM*的遠端桌面中，安裝[Microsoft Azure 存儲資源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。

1. 選擇**宇宙資料庫帳戶（預覽），** 只需按右鍵即可。

1. 選擇 **"連接到宇宙資料庫**"。

1. 選擇**API**。

1. 通過粘貼以前複製的資訊來輸入連接字串。

1. 選取 [下一步]****。

1. 選取 [連接]****。

1. 從*mycosmos 帳戶*流覽 Azure 宇宙資料庫和容器。

1. （可選）向*我的宇宙帳戶*添加新專案。

1. 關閉遠端桌面連線到*myVM。*

## <a name="clean-up-resources"></a>清除資源

使用專用終結點、Azure Cosmos 帳戶和 VM 完成後，請刪除資源組及其包含的所有資源： 

1. 在入口網站頂端的 [搜尋]**** 方塊中輸入 *myResourceGroup*，然後從搜尋結果中選取 [myResourceGroup]**。

1. 選取 [刪除資源群組]****。

1. 輸入 *"資源組***"以鍵入資源組名稱**，然後選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

在本文中，您將在虛擬網路、Azure Cosmos 帳戶和專用終結點上創建了 VM。 您從 Internet 連接到 VM，並使用專用連結安全地連接到 Azure Cosmos 帳戶。

* 要瞭解有關私有終結點的更多內容，請參閱[什麼是 Azure 專用終結點？](private-endpoint-overview.md)

* 要瞭解有關使用 Azure Cosmos DB 時私有終結點限制的更多詳細資訊，請參閱[Azure 專用連結與 Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md)一文。
