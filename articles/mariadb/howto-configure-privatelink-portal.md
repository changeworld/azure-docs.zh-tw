---
title: Private Link-Azure 入口網站-適用於 MariaDB 的 Azure 資料庫
description: 瞭解如何從 Azure 入口網站設定適用於 MariaDB 的 Azure 資料庫的 private link
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 79b3c3f8eca2fa4442a7845ca4aa3921d0302453
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241999"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>使用入口網站建立和管理適用於 MariaDB 的 Azure 資料庫的 Private Link

私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。  在本文中，您將瞭解如何使用 Azure 入口網站來建立 Azure 虛擬網路中的 VM，以及具有 Azure 私人端點的適用於 MariaDB 的 Azure 資料庫伺服器。

如您尚未擁有 Azure 訂用帳戶，請在開始之前先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 私人連結功能僅適用于一般用途或記憶體優化定價層中的適用於 MariaDB 的 Azure 資料庫伺服器。 確定資料庫伺服器是在其中一個定價層。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-an-azure-vm"></a>建立 Azure VM

在本節中，您將建立虛擬網路和子網，以裝載用來在 Azure) 中 (適用于 mariadb 伺服器存取 Private Link 資源的 VM。

### <a name="create-the-virtual-network"></a>建立虛擬網路
在本節中，您將建立虛擬網路和子網路，以裝載用來存取 Private Link 資源的 VM。

1. 在畫面的左上方，選取 [ **建立資源**  >  **網路**  >  **虛擬網路** ]。
2. 在 [建立虛擬網路] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 *MyVirtualNetwork* 。 |
    | 位址空間 | 輸入 *10.1.0.0/16* 。 |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]，輸入 *myResourceGroup* ，然後選取 [確定]。 |
    | 位置 | 選取 [西歐]。|
    | 子網路 - 名稱 | 輸入 *>mysubnet* 。 |
    | 子網路 - 位址範圍 | 輸入 *10.1.0.0/24* 。 |
    |||
3. 將其他項目保留為預設值，然後選取 [建立]。

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源] > [計算] > [虛擬機器]。

2. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup** 。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVm。 |
    | 區域 | 選取 [西歐]。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]。 |
    | 映像 | 選取 [Windows Server 2019 Datacenter]。 |
    | 大小 | 保留預設值 [標準 DS1 v2]。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設值 [無]。 |
    | **節省費用** |  |
    | 已經有 Windows 授權？ | 保留預設值 [否]。 |
    |||

1. 完成時，選取 [下一步:  磁碟]。

1. 在 [建立虛擬機器 - 磁碟]，保留預設值並選取 [下一步：網路功能]。

1. 在 [建立虛擬機器 - 網路] 中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 保留預設值 [MyVirtualNetwork]。  |
    | 位址空間 | 保留預設值 [10.1.0.0/24]。|
    | 子網路 | 保留預設值 [mySubnet (10.1.0.0/24)]。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]。 |
    | 選取輸入連接埠 | 選取 [HTTP] 和 [RDP]。|
    |||


1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

## <a name="create-an-azure-database-for-mariadb"></a>建立適用於 MariaDB 的 Azure 資料庫

在本節中，您將在 Azure 中建立適用於 MariaDB 的 Azure 資料庫伺服器。 

1. 在 Azure 入口網站畫面的左上方，選取 [ **建立資源**  >  **資料庫** ]  >  **適用於 MariaDB 的 Azure 資料庫** 。

1. 在 **適用於 MariaDB 的 Azure 資料庫** 提供下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup** 。 您已在上一節中建立此項目。|
    | **伺服器詳細資料** |  |
    |伺服器名稱  | 輸入 myserver。 如果此名稱已被使用，請建立唯一名稱。|
    | 系統管理員使用者名稱| 輸入您所選擇的系統管理員名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 8 個字元，而且符合定義的需求。 |
    | Location | 選取您想要適用于 mariadb 伺服器所在的 Azure 區域。 |
    |版本  | 選取所需適用于 mariadb 伺服器的資料庫版本。|
    | 計算 + 儲存體| 根據工作負載選取伺服器所需的定價層。 |
    |||

7. 選取 [確定]  。 
8. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。 
9. 當您看到 [通過驗證] 訊息時，請選取 [ **建立** ]。 
10. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 

> [!NOTE]
> 在某些案例中，適用於 MariaDB 的 Azure 資料庫和 VNet 子網路是位於不同的訂用帳戶。 在這些情況下，您必須確保下列設定：
> - 請確定這兩個訂用帳戶都已註冊 **DBforMariaDB** 資源提供者。 如需詳細資訊，請參閱 [resource-manager-registration][resource-manager-portal]

## <a name="create-a-private-endpoint"></a>建立私人端點

在本節中，您將建立適用于 mariadb 伺服器的私人端點。 

1. 在 Azure 入口網站畫面的左上方，選取 [ **建立資源**  >  **網路** ]  >  **Private Link** 。
2. 在 [私人連結中心 - 概觀] 中，選取 [與服務建立私人連線] 選項上的 [開始]。

    ![Private Link 總覽](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. 在 [ **建立私人端點-基本** ] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup** 。 您已在上一節中建立此項目。|
    | **實例詳細資料** |  |
    | 名稱 | 輸入 myPrivateEndpoint。 如果此名稱已被使用，請建立唯一名稱。 |
    |區域|選取 [西歐]。|
    |||
5. 完成時，選取 [下一步:資源]。
6. 在 [建立私人端點 - 資源] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 [連線到我目錄中的 Azure 資源]。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [ **DBforMariaDB]/[伺服器** ]。 |
    | 資源 |選取 [myServer]|
    |目標子資源 |選取 *mariadbServer*|
    |||
7. 完成時，選取 [下一步:組態]。
8. 在 [建立私人端點 - 設定] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路**| |
    | 虛擬網路| 選取 [MyVirtualNetwork]。 |
    | 子網路 | 選取 [mySubnet] ** 。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]。 |
    |私人 DNS 區域 |選取 *(新增) privatelink.mariadb.database.azure.com* |
    |||

    > [!Note] 
    > 針對您的服務使用預先定義的私人 DNS 區域，或提供慣用的 DNS 區功能變數名稱稱。 如需詳細資訊，請參閱 [Azure 服務 DNS 區域](../private-link/private-endpoint-dns.md) 設定。

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。 
2. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 

    ![Private Link 建立](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > 客戶 DNS 設定中的 FQDN 無法解析為已設定的私人 IP。 您將必須為設定的 FQDN 設定 DNS 區域[，如下所示。](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用遠端桌面 (RDP) 連線到 VM


在您建立 **myVm** 之後，從網際網路與其連線，如下所示： 

1. 在入口網站的搜尋列中，輸入 myVm。

1. 選取 [連線]  按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp* ) 檔案，並下載至您的電腦。

1. 開啟 *downloaded.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]  。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]  。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>從 VM 私下存取適用于 mariadb 伺服器

1. 在 myVm 的遠端桌面中，開啟 PowerShell。

2. 輸入  `nslookup mydemomserver.privatelink.mariadb.database.azure.com`。 

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用任何可用的用戶端來測試適用于 mariadb 伺服器的私人連結連線。 在下列範例中，我使用了 [MySQL 工作臺](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) 來進行操作。


4. 在 [ **新增連接** ] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 伺服器類型| 選取 [ **適用于 mariadb** ]。|
    | 伺服器名稱| 選取 *mydemoserver.privatelink.mariadb.database.azure.com* |
    | 使用者名稱 | 輸入 username@servername 在適用于 mariadb 伺服器建立期間提供的使用者名稱。 |
    |密碼 |輸入在適用于 mariadb 伺服器建立期間提供的密碼。 |
    |SSL|選取 [ **必要** ]。|
    ||

5. 選取 [ **測試連接** **] 或 [確定]** 。

6.  (選擇性地) 從左側功能表流覽資料庫，然後從適用于 mariadb 資料庫建立或查詢資訊

7. 關閉 myVm 的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源
當您完成使用私人端點、適用于 mariadb 伺服器和 VM 時，請刪除資源群組及其包含的所有資源：

1.  *myResourceGroup*   在入口網站頂端的 [ **搜尋** ] 方塊中輸入 myResourceGroup，然後  *myResourceGroup*   從搜尋結果中選取 [myResourceGroup]。
2. 選取 [刪除資源群組]。
3. 輸入 **資源組名類型的** myResourceGroup，然後選取 [ **刪除** ]。

## <a name="next-steps"></a>下一步

在此操作說明中，您已在虛擬網路、適用於 MariaDB 的 Azure 資料庫和私人端點上建立 VM 以進行私人存取。 您已從網際網路連線到其中一個 VM，並使用 Private Link 安全地與適用于 mariadb 伺服器通訊。 若要深入瞭解私人端點，請參閱 [什麼是 Azure 私人端點](../private-link/private-endpoint-overview.md)。

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md