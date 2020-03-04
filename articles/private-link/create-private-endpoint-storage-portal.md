---
title: 使用 Azure 私用端點私下連接至儲存體帳戶
description: 瞭解如何在 Azure 中使用私人端點，私下連線到儲存體帳戶。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8c76333d5a2be8a2c589dbe54389b023fef34854
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252537"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>使用 Azure 私用端點私下連接至儲存體帳戶
Azure 私用端點是 Azure 中私人連結的基本建立區塊。 它可讓 Azure 資源（例如虛擬機器（Vm））私下與私人連結資源進行通訊。

在本快速入門中，您將瞭解如何在 Azure 虛擬網路上建立 VM、使用 Azure 入口網站的儲存體帳戶與私人端點。 然後，您可以從 VM 安全地存取儲存體帳戶。

> [!NOTE]
> 私人端點不允許與相同子網路中的服務端點搭配使用！

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vm"></a>建立 VM
在本節中，您將建立虛擬網路和子網，以裝載用來存取私人連結資源的 VM （在此範例中為儲存體帳戶）。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您將建立虛擬網路和子網，以裝載用來存取私人連結資源的 VM。

在本節中，您將需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<的資源群組名稱 >**  | myResourceGroup |
| **\<虛擬網路-名稱 >** | myVirtualNetwork          |
| **\<區功能變數名稱稱 >**          | 美國中西部      |
| **\<IPv4-位址-空間 >**   | 10.1.0.0 \ 16          |
| **\<子網名稱 >**          | mySubnet        |
| **\<子網-位址範圍 >** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>建立虛擬機器

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
    | 確認密碼 | 再次輸入密碼。 |
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

## <a name="create-your-private-endpoint"></a>建立您的私用端點
在本節中，您將使用私人端點來建立私人儲存體帳戶。 

1. 在 [Azure 入口網站] 畫面的左上方，選取 [**建立資源**] [ > **儲存體**] [ > **儲存體帳戶**]。

1. 在 [**建立儲存體帳戶-基本**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 儲存體帳戶名稱  | 輸入*mystorageaccount*。 如果此名稱已被使用，請建立唯一名稱。 |
    | 區域 | 選取 [WestCentralUS]。 |
    | 效能| 保留預設值 [標準]。 |
    | 帳戶類型 | 保留預設**儲存體（一般用途 v2）** 。 |
    | 複寫 | 選取 **[讀取權限異地多餘儲存體（RA-GRS）** ]。 |
    |||
  
3. 選取 **[下一步：網路]** 。
4. 在 [**建立儲存體帳戶-網路功能**、連線方式] 中，選取 [**私人端點**]。
5. 在 [**建立儲存體帳戶-網路**] 中，選取 [**新增私人端點**]。 
6. 在 [**建立私人端點**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    |位置|選取 [WestCentralUS]。|
    |名稱|輸入 myPrivateEndpoint。  |
    |儲存體子資源|保留預設**Blob**。 |
    | **網路** |  |
    | 虛擬網路  | 從 [資源群組] *myResourceGroup*中選取 [ *MyVirtualNetwork* ]。 |
    | 子網路 | 選取 [mySubnet]。 |
    | **私人 DNS 整合**|  |
    | 與私人 DNS 區域整合  | 保留預設值 [**是]** 。 |
    | 私人 DNS 區域  | 保留預設值 [ **（新增）] privatelink.blob.core.windows.net**。 |
    |||
7. 選取 **[確定]** 。 
8. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。 
9. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 
10. 流覽至您剛才建立的儲存體帳戶資源。
11. 從左側內容功能表中選取 [**存取金鑰**]。
12. 選取 [key1] 連接字串上的 [**複製**]。
 
## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm。

1. 選取 [連線] 按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟 *downloaded.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 **[確定]** 。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-storage-account-privately-from-the-vm"></a>從 VM 私下存取儲存體帳戶

在本節中，您會使用私用端點，私下連接到儲存體帳戶。

1. 在 myVm 的遠端桌面中，開啟 PowerShell。
2. 輸入 `nslookup mystorageaccount.blob.core.windows.net` 您會收到類似下面的訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. 安裝 [Microsoft Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)。
4. 按一下滑鼠右鍵，選取 [**儲存體帳戶**]。
5. 選取 **[連線到 azure 儲存體]** 。
6. 選取 [**使用連接字串**]。
7. 選取 [下一步]。
8. 貼入先前複製的資訊，以輸入連接字串。
9. 選取 [下一步]。
10. 選取 [連接]。
11. 從 mystorageaccount 流覽 Blob 容器 
12. 也建立資料夾及/或將檔案上傳至*mystorageaccount*。 
13. 關閉對*myVM*的遠端桌面連線。 

存取儲存體帳戶的其他選項：
- Microsoft Azure 儲存體總管是 Microsoft 提供的獨立免費應用程式，可讓您在 Windows、macOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。 您可以安裝應用程式，以私下流覽儲存體帳戶內容。 
 
- AzCopy 公用程式是適用于 Azure 儲存體的高效能可編寫腳本資料傳輸的另一個選項。 請使用 AzCopy 在 Blob、檔案和表格儲存體之間傳輸資料。 


## <a name="clean-up-resources"></a>清除資源 
當您使用私人端點、儲存體帳戶和 VM 完成時，請刪除資源群組及其包含的所有資源： 
1. 在入口網站頂端的 [搜尋] 方塊中輸入 **myResourceGroup**，然後從搜尋結果中選取 [myResourceGroup]。 
2. 選取 [刪除資源群組]。 
3. 針對 [輸入資源群組名稱] 輸入 **myResourceGroup**，然後選取 [刪除]。 

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已在虛擬網路和儲存體帳戶和私人端點上建立 VM。 您已從網際網路連線至其中一個 VM，並使用私人連結安全地向儲存體帳戶通訊。 若要深入瞭解私用端點，請參閱[什麼是 Azure 私人端點？](private-endpoint-overview.md)。
