---
title: 專用連結 - Azure 門戶 - MariaDB 的 Azure 資料庫
description: 瞭解如何從 Azure 門戶為 MariaDB 配置 Azure 資料庫的專用連結
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 3f421cad64caf91b898bb1ec13dc909b93b7f72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370333"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>使用門戶為 MariaDB 創建和管理 Azure 資料庫的專用連結

私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。  在本文中，您將學習如何使用 Azure 門戶在 Azure 虛擬網路中創建 VM，以及使用 Azure 專用終結點為 MariaDB 伺服器創建 Azure 資料庫。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 此功能在所有 Azure 區域都可用，其中 MariaDB 的 Azure 資料庫支援通用和記憶體優化定價層。

## <a name="sign-in-to-azure"></a>登入 Azure
登錄到 Azure[門戶](https://portal.azure.com)。

## <a name="create-an-azure-vm"></a>建立 Azure VM

在本節中，您將創建虛擬網路和子網來承載用於訪問專用連結資源的 VM（Azure 中的 MariaDB 伺服器）。

### <a name="create-the-virtual-network"></a>建立虛擬網路
在本節中，您將建立虛擬網路和子網路，以裝載用來存取 Private Link 資源的 VM。

1. 在螢幕的左上角，選擇 **"創建資源** > **網路** > **虛擬網路**"。
2. 在 [建立虛擬網路]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入*我的虛擬網路*。 |
    | 位址空間 | 輸入 *10.1.0.0/16*。 |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [新建]****，輸入 *myResourceGroup*，然後選取 [確定]****。 |
    | Location | 選取 [西歐]****。|
    | 子網路 - 名稱 | 輸入 mySubnet**。 |
    | 子網路 - 位址範圍 | 輸入 *10.1.0.0/24*。 |
    |||
3. 將其他項目保留為預設值，然後選取 [建立]****。

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 Azure 門戶中螢幕的左上角，選擇 **"創建資源** > **計算** > **虛擬機器**"。

2. 在 [建立虛擬機器 - 基本]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選擇**我的資源組**。 您已在上一節中建立此項目。  |
    | **實例詳細資訊** |  |
    | 虛擬機器名稱 | 輸入 myVm**。 |
    | 區域 | 選取 [西歐]****。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]****。 |
    | 映像 | 選取 [Windows Server 2019 Datacenter]****。 |
    | 大小 | 保留預設值 [標準 DS1 v2]****。 |
    | **管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
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
    |||


1. 選擇 **"審閱" = 創建**。 您會移至 [檢閱 + 建立]**** 頁面，其中 Azure 會驗證您的設定。

1. 當您看到**驗證傳遞**的消息時，選擇 **"創建**"。

## <a name="create-an-azure-database-for-mariadb"></a>建立適用於 MariaDB 的 Azure 資料庫

在本節中，您將在 Azure 中為 MariaDB 伺服器創建 Azure 資料庫。 

1. 在 Azure 門戶中螢幕的左上角，選擇為**MariaDB****創建資源** > **資料庫** > Azure 資料庫 。

1. 在**MariaDB 的 Azure 資料庫中**提供了以下資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選擇**我的資源組**。 您已在上一節中建立此項目。|
    | **伺服器詳細資料** |  |
    |伺服器名稱  | 輸入 myserver**。 如果此名稱已被使用，請建立唯一名稱。|
    | 系統管理員使用者名稱| 輸入您所選擇的系統管理員名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 8 個字元，而且符合定義的需求。 |
    | Location | 選擇要希望 MariaDB 伺服器駐留的 Azure 區域。 |
    |版本  | 選擇所需的 MariaDB 伺服器的資料庫版本。|
    | 計算 + 存儲| 根據工作負荷選擇伺服器所需的定價層。 |
    |||
 
7. 選取 [確定]****。 
8. 選擇 **"審閱" = 創建**。 您會移至 [檢閱 + 建立]**** 頁面，其中 Azure 會驗證您的設定。 
9. 當您看到驗證傳遞的消息時，選擇 **"創建**"。 
10. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 

## <a name="create-a-private-endpoint"></a>建立私人端點

在本節中，您將創建到 MariaDB 伺服器的專用終結點。 

1. 在 Azure 門戶中螢幕的左上角，選擇 **"創建資源** > **網路** > **專用連結**"。
2. 在 [私人連結中心 - 概觀]**** 中，選取 [與服務建立私人連線]**** 選項上的 [開始]****。

    ![專用連結概述](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. 在 **"創建專用終結點 - 基礎知識"中**，輸入或選擇此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選擇**我的資源組**。 您已在上一節中建立此項目。|
    | **實例詳細資訊** |  |
    | 名稱 | 輸入 myPrivateEndpoint**。 如果此名稱已被使用，請建立唯一名稱。 |
    |區域|選取 [西歐]****。|
    |||
5. 選擇 **"下一步"：資源**。
6. 在 [建立私人端點 - 資源]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 [連線到我目錄中的 Azure 資源]。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選擇**微軟.DBforMariaDB/伺服器**。 |
    | 資源 |選取 [myServer]**|
    |目標子資源 |選擇*瑪麗亞德銀行伺服器*|
    |||
7. 選擇 **"下一步"：配置**。
8. 在 **"創建專用終結點 - 配置**"中，輸入或選擇此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路**| |
    | 虛擬網路| 選擇*我的虛擬網路*。 |
    | 子網路 | 選取 [mySubnet] **。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]****。 |
    |私人 DNS 區域 |選擇 *（新）私人連結.mariadb.資料庫.azure.com* |
    |||

1. 選擇 **"審閱" = 創建**。 您會移至 [檢閱 + 建立]**** 頁面，其中 Azure 會驗證您的設定。 
2. 當您看到**驗證傳遞**的消息時，選擇 **"創建**"。 

    ![已創建專用連結](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > 客戶 DNS 設置中的 FQDN 不會解析為配置的專用 IP。 您必須為配置的 FQDN 設置 DNS 區域，[如下所示](../dns/dns-operations-recordsets-portal.md)。

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>使用遠端桌面 (RDP) 連線到 VM


在您建立 **myVm** 之後，從網際網路與其連線，如下所示： 

1. 在入口網站的搜尋列中，輸入 myVm**。

1. 選取 [連線] **** 按鈕。 選取 [連線]**** 按鈕之後，隨即會開啟 [連線至虛擬機器]****。

1. 選擇**下載 RDP 檔**。 Azure 會建立一個「遠端桌面通訊協定」(*.rdp*) 檔案，並下載至您的電腦。

1. 開啟 *downloaded.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]****。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選擇**更多選項** > **"使用其他帳戶**"來指定創建 VM 時輸入的憑據。

1. 選取 [確定]****。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是]**** 或 [繼續]****。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>從 VM 私下訪問 MariaDB 伺服器

1. 在 myVM ** 的遠端桌面中，開啟 PowerShell。

2. 輸入  `nslookup mydemomserver.privatelink.mariadb.database.azure.com`。 

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用任何可用用戶端測試 MariaDB 伺服器的專用鏈路連接。 在下面的示例中，我使用[MySQL 工作臺](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html)執行操作。


4. 在 **"新建連接**"中，輸入或選擇此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 伺服器類型| 選擇**MariaDB**。|
    | 伺服器名稱| 選擇*mydemoserver.privatelink.mariadb.database.azure.com* |
    | [使用者名稱] | 輸入在username@servernameMariaDB 伺服器創建期間提供的使用者名。 |
    |密碼 |輸入在 MariaDB 伺服器創建期間提供的密碼。 |
    |SSL|選擇 **"必需**"。|
    ||

5. 選擇 **"測試連接**"**或"確定**"。

6. （可選）從左側功能表流覽資料庫，並從 MariaDB 資料庫中創建或查詢資訊

7. 關閉遠端桌面連線到 myVm。

## <a name="clean-up-resources"></a>清除資源
使用專用終結點 MariaDB 伺服器和 VM 完成後，請刪除資源組及其包含的所有資源：

1. 在門戶頂部的 **"搜索"** 框中輸入 *"我的資源組* "，然後從搜尋結果中選擇 *"我的資源組* "。
2. 選取 [刪除資源群組]****。
3. 輸入"資源組 **"以鍵入資源組名稱**，然後選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

在此執行中，您可以在虛擬網路上創建了 VM、MariaDB 的 Azure 資料庫以及用於專用訪問的專用終結點。 您從 Internet 連接到一個 VM，並使用專用鏈路安全地連接到 MariaDB 伺服器。 要瞭解有關私有終結點的更多內容，請參閱[什麼是 Azure 專用終結點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)。
