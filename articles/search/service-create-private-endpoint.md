---
title: 建立安全連線的私人端點
titleSuffix: Azure Cognitive Search
description: 設定虛擬網路中的私人端點，以安全地連接到 Azure 認知搜尋服務。
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: bbbc79a129ec3140ea6d286cbdce0165e2f6ae7b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280405"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>建立私人端點，以 Azure 認知搜尋的安全連線

在本文中，您將使用 Azure 入口網站建立無法透過網際網路存取的新 Azure 認知搜尋服務實例。 接下來，您將在相同的虛擬網路中設定 Azure 虛擬機器，並使用它透過私人端點來存取搜尋服務。

私人端點是由 [Azure Private Link](../private-link/private-link-overview.md)以個別服務的形式提供。 如需成本的詳細資訊，請參閱 [定價頁面](https://azure.microsoft.com/pricing/details/private-link/)。

> [!Important]
> 您可以使用 Azure 入口網站或 [管理 REST API 2020-03-13 版](/rest/api/searchmanagement/)來設定 Azure 認知搜尋的私人端點支援。 當服務端點為私用時，會停用某些入口網站功能。 您將能夠查看和管理服務等級資訊，但基於安全性考慮，會限制入口網站存取索引資料和服務中的各種元件，例如索引、索引子和技能集定義。

## <a name="why-use-a-private-endpoint-for-secure-access"></a>為何要使用私人端點進行安全存取？

Azure 認知搜尋的[私人端點](../private-link/private-endpoint-overview.md)可讓虛擬網路上的用戶端透過[Private Link](../private-link/private-link-overview.md)，安全地存取搜尋索引中的資料。 私人端點會使用您搜尋服務的 [虛擬網路位址空間](../virtual-network/private-ip-addresses.md) 中的 IP 位址。 用戶端與搜尋服務之間的網路流量會在 Microsoft 骨幹網路上進行虛擬網路和私人連結，以消除公用網際網路的暴露。 如需支援 Private Link 的其他 PaaS 服務清單，請參閱產品檔中的「 [可用性」一節](../private-link/private-link-overview.md#availability) 。

搜尋服務的私人端點可讓您：

- 封鎖您的搜尋服務在公用端點上的所有連接。
- 藉由讓您封鎖從虛擬網路遭到外泄的資料，提高虛擬網路的安全性。
- 使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [expressroute](../expressroute/expressroute-locations.md) 搭配私人對等互連，安全地從連線到虛擬網路的內部部署網路連接到您的搜尋服務。

## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您將建立虛擬網路和子網，以裝載將用來存取搜尋服務私人端點的 VM。

1. 從 Azure 入口網站的 [首頁] 索引標籤中，選取 [**建立資源**  >  **網路**  >  **虛擬網路**]。

1. 在 [建立虛擬網路] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 訂用帳戶 | 選取您的訂用帳戶|
    | 資源群組 | 選取 [ **建立新**的]，輸入 *myResourceGroup*，然後選取 **[確定]** |
    | 名稱 | 輸入 *MyVirtualNetwork* |
    | 區域 | 選取您想要的區域 |
    |||

1. 保留其餘設定的預設值。 按一下 [ **審核] + [建立** ]，然後 **建立**

## <a name="create-a-search-service-with-a-private-endpoint"></a>使用私人端點建立搜尋服務

在本節中，您將建立具有私人端點的新 Azure 認知搜尋服務。 

1. 在 Azure 入口網站畫面的左上方，選取 [**建立資源**  >  **Web**  >  **Azure 認知搜尋**。

1. 在 [ **新的搜尋服務-基本**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | **執行個體詳細資料** |  |
    | URL | 輸入唯一名稱。 |
    | 位置 | 選取所需的區域。 |
    | 定價層 | 選取 [ **變更定價層** ]，然後選擇您想要的服務層級。 **免費**層上的 (不支援。 必須是 **基本** 或更高的版本。 )  |
    |||
  
1. 選取 **[下一步：調整]**。

1. 將值保留為預設值，然後選取 **[下一步：網路]**。

1. 在 [ **新的搜尋服務-網路**] 中，選取 [ **私人** ]， ** (資料) 的端點連線能力 **。

1. 在 [**新搜尋服務-網路**] 中，選取 [**私人端點**] 底下的 [ **+ 新增**]。 

1. 在 [ **建立私人端點**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。|
    | 位置 | 選取 [美國西部]****。|
    | 名稱 | 輸入 myPrivateEndpoint。  |
    | 目標子資源 | 保留預設 **searchService**。 |
    | **網路** |  |
    | 虛擬網路  | 從 [資源群組*myResourceGroup*] 中選取 [ *MyVirtualNetwork* ]。 |
    | 子網路 | 選取 [mySubnet]。 |
    | **私人 DNS 整合** |  |
    | 與私人 DNS 區域整合  | 保留預設值 [ **是]**。 |
    | 私人 DNS 區域  | 保留預設值 [ (新的) privatelink.search.windows.net * *。 |
    |||

1. 選取 [確定]  。 

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。 

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 

1. 新服務的布建完成後，請流覽至您剛才建立的資源。

1. 從左側內容功能表中選取 [ **金鑰** ]。

1. 連接到服務時，請複製 **主要管理金鑰** 以供稍後進行。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 在 Azure 入口網站畫面的左上方，選取 [**建立資源**  >  **計算**  >  **虛擬機器**]。

1. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此項目。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVm。 |
    | 區域 | 選取 [ **美國西部** ] 或您使用的任何區域。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]。 |
    | 映像 | 選取 [Windows Server 2019 Datacenter]。 |
    | 大小 | 保留預設值 [標準 DS1 v2]。 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 保留預設的 [ **允許選取的埠**]。 |
    | 選取輸入連接埠 | 將預設的 **RDP (3389) **。 |
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
    ||

   > [!NOTE]
   > IPv4 位址可以用 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 格式表示。 請記得避免保留給私用網路的 IP 範圍，如 [RFC 1918](https://tools.ietf.org/html/rfc1918)中所述：
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。 

## <a name="connect-to-the-vm"></a>連接至 VM

下載並連接至 VM *myVm* ，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm。

1. 選取 [連線]  按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp* 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]  。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="test-connections"></a>測試連接

在本節中，您將會驗證對搜尋服務的私人網路存取，並使用私人端點私下連接至。

當搜尋服務端點為私用時，會停用某些入口網站功能。 您將能夠查看和管理服務等級設定，但基於安全性考慮，會限制入口網站存取索引資料和服務中的各種其他元件（例如索引、索引子和技能集定義）。

1. 在 myVm 的遠端桌面中，開啟 PowerShell。

1. 輸入 ' nslookup [search service name]. net '

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. 從 VM 連接到搜尋服務，並建立索引。 您可以遵循本 [快速入門](search-get-started-postman.md) ，使用 REST API 在 Postman 的服務中建立新的搜尋索引。 從 Postman 設定要求需要搜尋服務端點 (HTTPs：//[搜尋服務名稱].. a n t) ，以及您在上一個步驟中複製的管理員 api 金鑰。

1. 從 VM 完成快速入門是您確認服務可完全運作。

1. 關閉 *myVM*的遠端桌面連線。 

1. 若要確認您的服務無法在公用端點上存取，請在您的本機工作站上開啟 Postman，然後嘗試快速入門中的前幾個工作。 如果您收到遠端伺服器不存在的錯誤，表示您已成功設定搜尋服務的私人端點。

## <a name="clean-up-resources"></a>清除資源 
當您完成使用私人端點、搜尋服務和 VM 時，請刪除資源群組及其包含的所有資源：
1.  *myResourceGroup*   在入口網站頂端的 [**搜尋**] 方塊中輸入 myResourceGroup，然後 *myResourceGroup*   從搜尋結果中選取 [myResourceGroup]。 
1. 選取 [刪除資源群組]。 
1. 輸入 *myResourceGroup*   **資源組名類型的**MyResourceGroup，然後選取 [**刪除**]。

## <a name="next-steps"></a>後續步驟
在本文中，您已在虛擬網路上建立 VM，並在具有私人端點的搜尋服務上建立。 您已從網際網路連線到 VM，並使用 Private Link 安全地傳達給搜尋服務。 若要深入瞭解私人端點，請參閱 [什麼是 Azure 私人端點？](../private-link/private-endpoint-overview.md)。