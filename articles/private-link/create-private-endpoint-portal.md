---
title: 快速入門 - 管理 Azure 中的私人端點
description: 在本快速入門中，了解如何使用 Azure 入口網站建立私人端點
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 485eb14938fc7e490ea2d68c9090cdfdbf01cc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252568"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>快速入門：使用 Azure 入口網站建立私人端點

私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。 在本快速入門中，您將了解如何使用 Azure 入口網站在 Azure 虛擬網路上建立 VM，以及建立具有 Azure 私人端點的 SQL Database 伺服器。 然後，您就可以從 VM 安全地存取 SQL Database 伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vm"></a>建立 VM
在本節中，您將建立虛擬網路和子網路，以裝載用來存取 Private Link 資源 (在此範例中為 Azure 中的 SQL 伺服器) 的 VM。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您將建立虛擬網路和子網路，以裝載用來存取 Private Link 資源的 VM。

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | 美國中西部    |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源]   > [計算]   > [虛擬機器]  。

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
    |||


1. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

## <a name="create-a-sql-database-server"></a>建立 SQL 資料庫伺服器
在本節中，您將在 Azure 中建立 SQL 資料庫伺服器。 

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源]   > [資料庫]   > [SQL 資料庫]  。

1. 在 [建立SQL 資料庫 - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **資料庫詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 資料庫名稱  | 輸入 mydatabase  。 如果此名稱已被使用，請建立唯一名稱。 |
    |||
5. 在 [伺服器]  中，選取 [新建]  。 
6. 在 [新增伺服器]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |伺服器名稱  | 輸入 myserver  。 如果此名稱已被使用，請建立唯一名稱。|
    | 伺服器管理員登入| 輸入您所選擇的系統管理員名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 8 個字元，而且符合定義的需求。 |
    | Location | 選取您想要放置 SQL Server 的 Azure 區域。 |
    
7. 選取 [確定]  。 
8. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。 
9. 當您看到 [驗證成功] 訊息時，請選取 [建立]  。 
10. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 

## <a name="create-a-private-endpoint"></a>建立私人端點

在本節中，您將建立 SQL 伺服器，並在其中新增私人端點。 

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源]   > [網路]   > [Private Link 中心 (預覽)]  。
2. 在 [私人連結中心 - 概觀]  中，選取 [與服務建立私人連線]  選項上的 [開始]  。
1. 在 [建立私人端點 (預覽) - 基本]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | 名稱 | 輸入 myPrivateEndpoint  。 如果此名稱已被使用，請建立唯一名稱。 |
    |區域|選取 [WestCentralUS]  。|
    |||
5. 完成時，選取 [下一步:  資源]。
6. 在 [建立私人端點 - 資源]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 [連線到我目錄中的 Azure 資源]。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [Microsoft.Sql/servers]  。 |
    | 資源 |選取 [myServer] |
    |目標子資源 |選取 [sqlServer] |
    |||
7. 完成時，選取 [下一步:  組態]。
8. 在 [建立私人端點 (預覽) - 組態]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路**| |
    | 虛擬網路| 選取 [MyVirtualNetwork]  。 |
    | 子網路 | 選取 [mySubnet]  。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]  。 |
    |私人 DNS 區域 |選取 [(New)privatelink.database.windows.net]  |
    |||

1. 選取 [檢閱 + 建立]  。 您會移至 [檢閱 + 建立]  頁面，其中 Azure 會驗證您的設定。 
2. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用遠端桌面 (RDP) 連線到 VM


在您建立 **myVm** 之後，從網際網路與其連線，如下所示： 

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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>從 VM 私下存取 SQL 資料庫伺服器

1. 在 myVm  的遠端桌面中，開啟 PowerShell。

2. 輸入 `nslookup myserver.database.windows.net`。 

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. 安裝 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

4. 在 [連線至伺服器]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 伺服器類型| 選取 [資料庫引擎]  。|
    | 伺服器名稱| 選取 *myserver.database.windows.net* |
    | [使用者名稱] | 輸入在 SQL Server 建立期間所提供的 username@servername 作為使用者名稱。 |
    |密碼 |輸入 SQL Server 建立期間所提供的密碼。 |
    |記住密碼|選取 [是]  。|
    |||
1. 選取 [連接]  。
2. 瀏覽左側功能表中的資料庫。
3. (選擇性) 從 mydatabase 建立或查詢資訊。
4. 關閉對 myVm  的遠端桌面連線。 

## <a name="clean-up-resources"></a>清除資源 
在私人端點、SQL Server 和 VM 使用完畢後，請刪除資源群組及其包含的所有資源： 
1. 在入口網站頂端的 [搜尋]  方塊中輸入 *myResourceGroup*，然後從搜尋結果中選取 [myResourceGroup]  。 
2. 選取 [刪除資源群組]  。 
3. 針對 [輸入資源群組名稱]  輸入 myResourceGroup，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您在虛擬網路、SQL 資料庫伺服器和私人端點上建立了 VM 以進行私用存取。 您從網際網路連線到其中一個 VM，然後使用私人連結安全地與 SQL 資料庫伺服器進行通訊。 若要深入了解私人端點，請參閱[什麼是 Azure 私人端點？](private-endpoint-overview.md)。
