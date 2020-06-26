---
title: 教學課程：建立 NAT 閘道 - Microsoft Azure 入口網站
titlesuffix: Azure Virtual Network NAT
description: 本快速入門說明如何使用 Azure 入口網站建立 NAT 閘道。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 5010dc08b695a0376ace5dde935c63caf0c39633
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711403"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站建立 NAT 閘道

本教學課程說明如何使用 Azure 虛擬網路 NAT 服務。 您將建立 NAT 閘道，為 Azure 中的虛擬機器提供輸出連線能力。 

您可以依偏好使用 [Azure CLI](quickstart-create-nat-gateway-cli.md)、[Azure PowerShell](quickstart-create-nat-gateway-powershell.md) (而非入口網站) 執行這些步驟，或部署 [ARM 範本](quickstart-create-nat-gateway-powershell.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在您部署 VM 並可使用 NAT 閘道之前，我們必須先建立資源群組和虛擬網路。

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 美國東部 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>建立 VM 以使用 NAT 閘道

我們現在會建立 VM 以使用 NAT 服務。 此 VM 具有公用 IP，可作為執行個體層級的公用 IP，讓您能夠存取 VM。 NAT 服務可感知流量方向，並將取代您子網路中的預設網際網路目的地。 VM 的公用 IP 位址不會用於輸出連線。

1. 在入口網站的左上方，選取 [建立資源]**** > [計算]**** > [Ubuntu Server 18.04 LTS]****，或在 Marketplace 搜尋中搜尋 [Ubuntu Server 18.04 LTS]****。

2. 在 [建立虛擬機器]**** 中，輸入或選取 [基本資訊]**** 索引標籤中的下列值：
   - [訂用帳戶]**** > [資源群組]****：選取 [myResourceGroupNAT]****。
   - **執行個體詳細資料** > **虛擬機器名稱**：輸入 **myVM**。
   - **執行個體詳細資料** > **區域** > 選取 [美國東部 2]****。
   - **系統管理員帳戶** > **驗證類型**：選取 [密碼]****。
   - **系統管理員帳戶** > 輸入 [使用者名稱]****、[密碼]**** 和 [確認密碼]**** 資訊。
   - **輸入連接埠規則** > **公用輸入連接埠**：選取 [允許選取的連接埠]****。
   - **輸入連接埠規則** > **選取輸入連接埠**：選取 [SSH (22)]****
   - 選取 [網路]**** 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。

3. 在 [網路]**** 索引標籤中，確定已選取下列項目：
   - **虛擬網路**：**MyVNet**
   - **子網路**：**mySubnet**
   - **公用 IP** > 選取 [新建]****。  在 [建立公用 IP 位址]**** 視窗中，於 [名稱]**** 欄位中輸入 **myPublicIPVM**，然後針對 [SKU]**** 選擇 [標準]****。  按一下 [確定]****。
   - **NIC 網路安全性群組**：選取 [基本]****。
   - **公用輸入連接埠**：選取 [允許選取的連接埠]****。
   - **選取輸入連接埠**：確認已選取 [SSH]****。

4. 在 [管理]**** 索引標籤的 [監視]**** 下方，將 [開機診斷]**** 設為 [關閉]****。

5. 選取 [檢閱 + 建立]****。 

6. 檢閱設定，然後按一下 [建立]****。

## <a name="create-the-nat-gateway"></a>建立 NAT 閘道

您可以使用一或多個公用 IP 位址資源、公用 IP 前置詞或兩者。 我們會新增公用 IP 資源、公用 IP 前置詞和 NAT 閘道資源。

本節將詳細說明如何使用 NAT 閘道資源建立及設定下列 NAT 服務元件：
  - 公用 IP 集區和公用 IP 前置詞，將用於 NAT 閘道資源所轉譯的輸出流量。
  - 將閒置逾時時間從預設的 4 分鐘變更為 10 分鐘。

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

1. 在入口網站的左上方，選取 [建立資源]**** > [網路]**** > [公用 IP 位址]****，或在 Marketplace 搜尋中搜尋 [公用 IP 位址]****。

2. 在 [建立公用 IP 位址]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | IP 版本 | 選取 [IPv4]****。
    | SKU | 選取 [標準]****。
    | 名稱 | 輸入 **myPublicIP**。 |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [myResourceGroupNAT]****。 |
    | Location | 選取 [美國東部 2]****。|

3. 保留其餘的預設值，然後選取 [建立]****。

### <a name="create-a-public-ip-prefix"></a>建立公用 IP 前置詞

1. 在入口網站的左上方，選取 [建立資源]**** > [網路]**** > [公用 IP 前置詞]****，或在 Marketplace 搜尋中搜尋 [公用 IP 前置詞]****。 

2. 在 [建立公用 IP 前置詞]**** 中，輸入或選取 [基本資訊]**** 索引標籤中的下列值：
   - [訂用帳戶]**** > [資源群組]****：選取 [myResourceGroupNAT]****>
   - [執行個體詳細資料]**** > [名稱]****：輸入 **myPublicIPprefix**。
   - **執行個體詳細資料** > **區域**：選取 [美國東部 2]****。
   - **執行個體詳細資料** > **前置詞大小**：選取 [/31 (2 個位址)]****

3. 保留其餘的預設值，然後選取 [檢閱 + 建立]****。

4. 檢閱設定，然後選取 [建立]****。
   

### <a name="create-a-nat-gateway-resource"></a>建立 NAT 閘道資源

1. 在入口網站的左上方，選取 [建立資源]**** > [網路]**** > [NAT 閘道]****，或在 Marketplace 搜尋中搜尋 [NAT 閘道]****。

2. 在 [建立網路位址轉譯 (NAT) 閘道]**** 中，輸入或選取 [基本資訊]**** 索引標籤中的下列值：
   - [訂用帳戶]**** > [資源群組]****：選取 [myResourceGroupNAT]****。
   - **執行個體詳細資料** > **NAT 閘道名稱**：輸入 **myNATgateway**。
   - **執行個體詳細資料** > **區域**：選取 [美國東部 2]****。
   - **執行個體詳細資料** > **閒置逾時 (分鐘)** ：輸入 **10**。
   - 選取 [公用 IP]**** 索引標籤，或選取 **[下一步：** 公用 IP]。

3. 在 [公用 IP]**** 索引標籤中，輸入或選取下列值：
   - **公用 IP 位址**：選取 [myPublicIP]****。
   - **公用 IP 前置詞**：選取 [myPublicIPprefix]****。
   - 選取 [子網路]**** 索引標籤，或選取 **[下一步：** 子網路]。

4. 在 [子網路]**** 索引標籤中，輸入或選取下列值：
   - **虛擬網路**：選取 [myResourceGroupNAT]**** > [myVnet]****。
   - **子網路名稱**：選取 [mySubnet]**** 旁邊的方塊。

5. 選取 [檢閱 + 建立]****。

6. 檢閱設定，然後選取 [建立]****。

## <a name="discover-the-ip-address-of-the-vm"></a>探索 VM 的 IP 位址

1. 在入口網站的左側，選取 [資源群組]****。
2. 選取 [myResourceGroupNAT]****。
3. 選取 [myVM]****。
4. 在 [概觀]**** 中，將 [公用 IP 位址]**** 值複製並貼到記事本中，以便您可將其用來存取 VM。

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便將其用來存取 VM。

## <a name="sign-in-to-vm"></a>登入 VM

在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

您現在已經準備好使用 NAT 服務。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、NAT 閘道和所有相關資源。 選取包含 NAT 閘道的資源群組 **myResourceGroupNAT**，然後選取 [刪除]****。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 NAT 閘道以及要使用該閘道的 VM。 

接著請檢閱 Azure 監視器中的計量，以了解 NAT 服務的運作。 診斷可用 SNAT 連接埠的資源耗盡相關問題。  新增額外的公用 IP 位址資源或公用 IP 前置詞資源 (或兩者)，都可以解決 SNAT 連接埠的資源耗盡問題。


- 了解 [Azure 虛擬網路 NAT](./nat-overview.md)
- 深入了解 [NAT 閘道資源](./nat-gateway-resource.md)。
- [使用 Azure CLI 部署 NAT 閘道資源](./quickstart-create-nat-gateway-cli.md)的快速入門。
- [使用 Azure PowerShell 部署 NAT 閘道資源](./quickstart-create-nat-gateway-powershell.md)的快速入門。
- [使用 Azure 入口網站部署 NAT 閘道資源](./quickstart-create-nat-gateway-portal.md)的快速入門。
> [!div class="nextstepaction"]

