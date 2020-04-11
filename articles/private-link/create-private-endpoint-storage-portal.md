---
title: 使用 Azure 私人端點私下連線至儲存體帳戶
description: 瞭解如何使用專用終結點將專用連接到 Azure 中的存儲帳戶。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 111e6e2f80c3460f363c496b7b32befdca16250d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115116"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>使用 Azure 私人端點私下連線至儲存體帳戶
Azure 專用終結點是 Azure 中專用連結的基本構建基塊。 它使 Azure 資源(如虛擬機器 (VM))能夠與專用連結資源進行私下通信。

在此快速入門中,您將學習如何在 Azure 虛擬網路上創建 VM,該虛擬網路是使用 Azure 門戶使用專用終結點的儲存帳戶。 然後,您可以安全地從 VM 存取儲存帳戶。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vm"></a>建立 VM
在本節中,您將創建虛擬網路和子網來承載用於訪問專用連結資源的 VM(本示例中的存儲帳戶)。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中,您將創建虛擬網路和子網來承載用於訪問專用連結資源的 VM。

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | 美國中西部      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 Azure 門戶中螢幕的左上角,選擇 **「創建資源** > **計算** > **虛擬機器**」。

1. 在 [建立虛擬機器 - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVm  。 |
    | 區域 | 選取 [WestCentralUS]  。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]  。 |
    | 映像 | 選取 [Windows Server 2019 Datacenter]  。 |
    | 大小 | 保留預設值 [標準 DS1 v2]  。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設值 [無]  。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]  。 |
    |||

1. 完成時，選取 [下一步:  磁碟]。

1. 在 [建立虛擬機器 - 磁碟]  ，保留預設值並選取 [下一步：  網路功能]。

1. 在 [建立虛擬機器 - 網路]  中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設值 [MyVirtualNetwork]  。  |
    | 位址空間 | 保留預設值 [10.1.0.0/24]  。|
    | 子網路 | 保留預設值 [mySubnet (10.1.0.0/24)]  。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]  。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]  。 |
    | 選取輸入連接埠 | 選取 [HTTP]  和 [RDP]  。|
    ||

1. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

## <a name="create-your-private-endpoint"></a>建立專用終結點
在本節中,您將使用專用終結點創建專用存儲帳戶。 

1. 在 Azure 門戶中螢幕的左上角,選擇 **「創建資源** > **存儲** > **存儲帳戶**」。

1. 在 **'建立儲存帳戶 - 基礎知識' 中**,輸入或選擇此資訊:

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 儲存體帳戶名稱  | 輸入*我的儲存帳戶*。 如果此名稱已被使用，請建立唯一名稱。 |
    | 區域 | 選取 [WestCentralUS]  。 |
    | 效能| 保留預設值 [標準]****。 |
    | 帳戶類型 | 保留預設**儲存(通用 v2)。** |
    | 複寫 | 選擇**讀取存取異地冗餘儲存 (RA-GRS)。** |
    |||
  
3. 選擇**下一個:網路**。
4. 在**建立儲存帳號 - 網路**,連線方法中,選擇**專用終結點**。
5. 在 **"創建儲存帳戶 - 網路**"中,選擇 **"添加專用終結點**"。 
6. 在 **"建立專用終結點**"中,輸入或選擇此資訊:

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    |Location|選取 [WestCentralUS]  。|
    |名稱|輸入 myPrivateEndpoint  。  |
    |儲存子資源|保留預設**的 Blob**。 |
    | **網路** |  |
    | 虛擬網路  | 從資源群組 *「我的資源群組」* 中選擇*我的虛擬網路*。 |
    | 子網路 | 選取 [mySubnet]  。 |
    | **私人 DNS 整合**|  |
    | 與私人 DNS 區域整合  | 保留預設**的"是**"。 |
    | 私人 DNS 區域  | 將預設 **(新增) privatelink.blob.core.windows.net**。 |
    |||
7. 選取 [確定]  。 
8. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。 
9. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。 
10. 瀏覽到您剛剛建立的儲存帳戶資源。
11. 從左側內容功能表中選擇 **「存取鍵**」。
12. 選擇鍵1的連接字串上的 **「複製**」。
 
## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm**，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm  。

1. 選取 [連線]  按鈕。 選取 [連線]  按鈕之後，隨即會開啟 [連線至虛擬機器]  。

1. 選取 [下載 RDP 檔案]  。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟 *downloaded.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]  。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇]   > [使用不同的帳戶]  ，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]  。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是]  或 [繼續]  。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-storage-account-privately-from-the-vm"></a>從 VM 私下存取儲存帳戶

在本節中,您將使用專用終結點私下連接到存儲帳戶。

1. 在 myVm  的遠端桌面中，開啟 PowerShell。
2. 輸入`nslookup mystorageaccount.blob.core.windows.net`您將收到類似於此訊息的消息:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. 安裝 [Microsoft Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。
4. 選擇此鍵按下的**儲存帳號**。
5. 選擇 **「連接到 Azure 儲存**」 。
6. 選擇**使用連接字串**。
7. 選取 [下一步]  。
8. 通過貼上以前複製的資訊來輸入連接字串。
9. 選取 [下一步]  。
10. 選取 [連接]  。
11. 從我的儲存帳戶瀏覽 Blob 容器 
12. ( 選擇性的 )建立資料夾與/或將檔案上傳到*我的儲存帳戶*。 
13. 關閉遠端桌面連接到*myVM。* 

存取儲存帳戶的其他選項:
- Microsoft Azure 儲存資源管理員是Microsoft的一個獨立免費應用,使您能夠在Windows、macOS和Linux上直覺式處理Azure儲存資料。 您可以安裝應用程式以私下瀏覽儲存帳戶內容。 
 
- AzCopy 實用程式是 Azure 儲存高性能可文本數據傳輸的另一個選項。 請使用 AzCopy 在 Blob、檔案和表格儲存體之間傳輸資料。 


## <a name="clean-up-resources"></a>清除資源 
使用專用終結點、儲存帳戶和 VM 完成後,請刪除資源群組及其包含的所有資源: 
1. 在入口網站頂端的 [搜尋]  方塊中輸入 *myResourceGroup*，然後從搜尋結果中選取 [myResourceGroup]  。 
2. 選取 [刪除資源群組]  。 
3. 輸入 *「資源群組***」 以鍵入資源群組名稱**,然後選擇 **「刪除**」 。 

## <a name="next-steps"></a>後續步驟
在此快速入門中,您可以在虛擬網路和存儲帳戶和專用終結點上創建了 VM。 您從 Internet 連接到一個 VM,並使用專用鏈路安全地連接到存儲帳戶。 要瞭解有關私有終結點的更多內容,請參閱[什麼是 Azure 專用終結點?](private-endpoint-overview.md)
