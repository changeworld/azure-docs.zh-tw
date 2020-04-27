---
title: 教學課程：建立和測試 NAT 閘道 - Azure 入口網站
titlesuffix: Azure Virtual Network NAT
description: 本教學課程將說明如何使用 Azure 入口網站建立 NAT 閘道，並測試 NAT 服務
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: ceadbb4297ad0c5ce28470dd75b3f3496c9c5152
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084738"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>教學課程：使用 Azure 入口網站建立 NAT 閘道並測試 NAT 服務

在本教學課程中，您將建立 NAT 閘道，為 Azure 中的虛擬機器提供輸出連線能力。 若要測試 NAT 閘道，請部署來源和目的地虛擬機器。 您將會對從來源到目的地虛擬機器的公用 IP 位址進行輸出連線，藉此測試 NAT 閘道。  僅為了簡單起見，本教學課程會將來源和目的地部署在相同資源群組中的兩個不同虛擬網路中。

您可以依偏好使用 [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) 或 [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) (而非入口網站) 執行這些步驟。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="prepare-the-source-for-outbound-traffic"></a>準備輸出流量的來源

我們會在接下來的步驟中引導您完成完整測試環境的設定，以及測試本身的執行。 我們將從來源開始，其會使用我們在後續步驟中建立的 NAT 閘道資源。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在您部署 VM 並可使用 NAT 閘道之前，我們必須先建立資源群組和虛擬網路。

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | 美國東部 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>建立來源虛擬機器

我們現在會建立 VM 以使用 NAT 服務。 此 VM 具有公用 IP，可作為執行個體層級的公用 IP，讓您能夠存取 VM。 NAT 服務可感知流量方向，並將取代您子網路中的預設網際網路目的地。 VM 的公用 IP 位址不會用於輸出連線。

為了測試 NAT 閘道，我們會將公用 IP 位址資源指派為執行個體層級的公用 IP，以從外部存取此 VM。 此位址只會用來存取此 VM 以進行測試。  我們將示範如何讓 NAT 服務的優先順序高於其他輸出選項。

您也可以在不使用公用 IP 的情況下建立此 VM，並建立另一個 VM 作為 Jumpbox，而不需要公用 IP 來進行練習。

1. 在入口網站的左上方，選取 [建立資源]   > [計算]   > [Ubuntu Server 18.04 LTS]  ，或在 Marketplace 搜尋中搜尋 [Ubuntu Server 18.04 LTS]  。

2. 在 [建立虛擬機器]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：
   - [訂用帳戶]   > [資源群組]  ：選取 [myResourceGroupNAT]  。
   - **執行個體詳細資料** > **虛擬機器名稱**：輸入 **myVMsource**。
   - **執行個體詳細資料** > **區域** > 選取 [美國東部 2]  。
   - **系統管理員帳戶** > **驗證類型**：選取 [密碼]  。
   - **系統管理員帳戶** > 輸入 [使用者名稱]  、[密碼]  和 [確認密碼]  資訊。
   - **輸入連接埠規則** > **公用輸入連接埠**：選取 [允許選取的連接埠]  。
   - **輸入連接埠規則** > **選取輸入連接埠**：選取 [SSH (22)] 
   - 選取 [網路]  索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。

3. 在 [網路]  索引標籤中，確定已選取下列項目：
   - **虛擬網路**：**myVnetsource**
   - **子網路**：**mySubnetsource**
   - **公用 IP** > 選取 [新建]  。  在 [建立公用 IP 位址]  視窗中，於 [名稱]  欄位中輸入 **myPublicIPsourceVM**。 針對 [SKU]  選取 [標準]  。 保留其餘的預設值，然後按一下 [確定]  。
   - **NIC 網路安全性群組**：選取 [基本]  。
   - **公用輸入連接埠**：選取 [允許選取的連接埠]  。
   - **選取輸入連接埠**：確認已選取 [SSH]  。

4. 在 [管理]  索引標籤的 [監視]  下方，將 [開機診斷]  設為 [關閉]  。

5. 選取 [檢閱 + 建立]  。

6. 檢閱設定，然後按一下 [建立]  。

## <a name="create-the-nat-gateway"></a>建立 NAT 閘道

您可以搭配 NAT 閘道使用一或多個公用 IP 位址資源、公用 IP 前置詞或兩者。 我們會新增公用 IP 資源、公用 IP 前置詞和 NAT 閘道資源。

本節將詳細說明如何使用 NAT 閘道資源建立及設定下列 NAT 服務元件：
  - 公用 IP 集區和公用 IP 前置詞，將用於 NAT 閘道資源所轉譯的輸出流量。
  - 將閒置逾時時間從預設的 4 分鐘變更為 10 分鐘。

### <a name="create-a-public-ip-address"></a>建立公用 IP 位址

1. 在入口網站的左上方，選取 [建立資源]   > [網路]   > [公用 IP 位址]  ，或在 Marketplace 搜尋中搜尋 [公用 IP 位址]  。 

2. 在 [建立公用 IP 位址]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | IP 版本 | 選取 [IPv4]  。
    | SKU | 選取 [標準]  。
    | 名稱 | 輸入 **myPublicIPsource**。 |
    | 訂用帳戶 | 選取您的訂用帳戶。|
    | 資源群組 | 選取 [myResourceGroupNAT]  。 |
    | Location | 選取 [美國東部 2]  。|

3. 保留其餘的預設值，然後選取 [建立]  。

### <a name="create-a-public-ip-prefix"></a>建立公用 IP 前置詞

1. 在入口網站的左上方，選取 [建立資源]   > [網路]   > [公用 IP 前置詞]  ，或在 Marketplace 搜尋中搜尋 [公用 IP 前置詞]  。

2. 在 [建立公用 IP 前置詞]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：
   - [訂用帳戶]   > [資源群組]  ：選取 [myResourceGroupNAT]  >
   - **執行個體詳細資料** > **名稱**：輸入 **myPublicIPprefixsource**。
   - **執行個體詳細資料** > **區域**：選取 [美國東部 2]  。
   - **執行個體詳細資料** > **前置詞大小**：選取 [/31 (2 個位址)] 

3. 保留其餘的預設值，然後選取 [檢閱 + 建立]  。

4. 檢閱設定，然後選取 [建立]  。


### <a name="create-a-nat-gateway-resource"></a>建立 NAT 閘道資源

1. 在入口網站的左上方，選取 [建立資源]   > [網路]   > [NAT 閘道]  ，或在 Marketplace 搜尋中搜尋 [NAT 閘道]  。

2. 在 [建立網路位址轉譯 (NAT) 閘道]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：
   - [訂用帳戶]   > [資源群組]  ：選取 [myResourceGroupNAT]  。
   - **執行個體詳細資料** > **NAT 閘道名稱**：輸入 **myNATgateway**。
   - **執行個體詳細資料** > **區域**：選取 [美國東部 2]  。
   - **執行個體詳細資料** > **閒置逾時 (分鐘)** ：輸入 **10**。
   - 選取 [公用 IP]  索引標籤，或選取 **[下一步：** 公用 IP]。

3. 在 [公用 IP]  索引標籤中，輸入或選取下列值：
   - **公用 IP 位址**：選取 [myPublicIPsource]  。
   - **公用 IP 前置詞**：選取 [myPublicIPprefixsource]  。
   - 選取 [子網路]  索引標籤，或選取 **[下一步：** 子網路]。

4. 在 [子網路]  索引標籤中，輸入或選取下列值：
   - **虛擬網路**：選取 [myResourceGroupNAT]   > [myVnetsource]  。
   - **子網路名稱**：選取 [mySubnetsource]  旁邊的方塊。

5. 選取 [檢閱 + 建立]  。

6. 檢閱設定，然後選取 [建立]  。

所有連結到網際網路目的地的輸出流量現在都會使用 NAT 服務。  您不需要設定 UDR。


## <a name="prepare-destination-for-outbound-traffic"></a>準備輸出流量的目的地

我們現在會針對 NAT 服務所轉譯的輸出流量建立目的地，以便您進行測試。


## <a name="virtual-network-and-parameters-for-destination"></a>目的地的虛擬網路和參數

在您為目的地部署 VM 之前，我們必須先建立目的地虛擬機器可駐留的虛擬網路。 以下是與來源 VM 相同的步驟 (有些小變動)，用以公開目的地端點。

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | 美國東部 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>建立目的地虛擬機器

1. 在入口網站的左上方，選取 [建立資源]   > [計算]   > [Ubuntu Server 18.04 LTS]  ，或在 Marketplace 搜尋中搜尋 [Ubuntu Server 18.04 LTS]  。

2. 在 [建立虛擬機器]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：
   - [訂用帳戶]   > [資源群組]  ：選取 [myResourceGroupNAT]  。
   - **執行個體詳細資料** > **虛擬機器名稱**：輸入 **myVMdestination**。
   - **執行個體詳細資料** > **區域** > 選取 [美國東部 2]  。
   - **系統管理員帳戶** > **驗證類型**：選取 [密碼]  。
   - **系統管理員帳戶** > 輸入 [使用者名稱]  、[密碼]  和 [確認密碼]  資訊。
   - **輸入連接埠規則** > **公用輸入連接埠**：選取 [允許選取的連接埠]  。
   - **輸入連接埠規則** > **選取輸入連接埠**：選取 [SSH (22)]  和 [HTTP (80)]  。
   - 選取 [網路]  索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。

3. 在 [網路]  索引標籤中，確定已選取下列項目：
   - **虛擬網路**：**myVnetdestination**
   - **子網路**：**mySubnetdestination**
   - **公用 IP** > 選取 [新建]  。  在 [建立公用 IP 位址]  視窗中，於 [名稱]  欄位中輸入 **myPublicIPdestinationVM**。 針對 [SKU]  選取 [標準]  。 保留其餘的預設值，然後按一下 [確定]  。
   - **NIC 網路安全性群組**：選取 [基本]  。
   - **公用輸入連接埠**：選取 [允許選取的連接埠]  。
   - **選取輸入連接埠**：確認已選取 [SSH]  和 [HTTP]  。

4. 在 [管理]  索引標籤的 [監視]  下方，將 [開機診斷]  設為 [關閉]  。

5. 選取 [檢閱 + 建立]  。

6. 檢閱設定，然後選取 [建立]  。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>在目的地 VM 上準備 Web 伺服器和測試承載

首先，我們需要探索目的地 VM 的 IP 位址。 

1. 在入口網站的左側，選取 [資源群組]  。
2. 選取 [myResourceGroupNAT]  。
3. 選取 [myVMdestination]  。
4. 在 [概觀]  中，將 [公用 IP 位址]  值複製並貼到記事本中，讓您可以使用它來存取 VM。

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便在後續步驟中使用。 指出這是目的地虛擬機器。

### <a name="sign-in-to-destination-vm"></a>登入目的地 VM

在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com)。 使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

當您登入之後，請複製並貼上下列命令。  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

這些命令會更新您的虛擬機器、安裝 nginx，並建立 100 Kb 的檔案。 系統會使用 NAT 服務從來源 VM 擷取此檔案。

關閉目的地 VM 的 SSH 工作階段。

## <a name="prepare-test-on-source-vm"></a>在來源 VM 上準備測試

首先，我們需要探索來源 VM 的 IP 位址。

1. 在入口網站的左側，選取 [資源群組]  。
2. 選取 [myResourceGroupNAT]  。
3. 選取 [myVMsource]  。
4. 在 [概觀]  中，將 [公用 IP 位址]  值複製並貼到記事本中，以便您可將其用來存取 VM。

>[!IMPORTANT]
>複製公用 IP 位址，然後將其貼到記事本中，以便在後續步驟中使用。 指出這是來源虛擬機器。

### <a name="log-into-source-vm"></a>登入來源 VM

在瀏覽器中開啟 [Azure Cloud Shell](https://shell.azure.com) 的新索引標籤。  使用在上一個步驟中擷取的 IP 位址，透過 SSH 連線到虛擬機器。 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

複製並貼上下列命令，以準備測試 NAT 服務。

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

此命令會更新您的虛擬機器、安裝 go、從 GitHub 安裝 [hey](https://github.com/rakyll/hey)，以及更新您的殼層環境。

您現在已經準備好測試 NAT 服務。

## <a name="validate-nat-service"></a>驗證 NAT 服務

登入來源 VM 時，您可以使用 **curl** 和 **hey** 來產生目的地 IP 位址的要求。

使用 curl 來擷取 100 KB 的檔案。  以您先前複製的目的地 IP 位址取代下列範例中的 **\<ip-address-destination>** 。  **--output** 參數表示將會捨棄擷取的檔案。

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

您也可以使用 **hey** 來產生一系列的要求。 同樣地，使用您先前複製的目的地 IP 位址來取代 **\<ip-address-destination>** 。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

此命令將會產生 100 個要求 (一次 10 個)，並將逾期時間設為 30 秒，而不需要重複使用 TCP 連線。  每個要求都會擷取 100 KB。  在回合結束時，**hey** 會回報 NAT 服務執行效能的相關統計資料。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、NAT 閘道和所有相關資源。 選取包含 NAT 閘道的資源群組 **myResourceGroupNAT**，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立 NAT 閘道、建立來源和目的地 VM，然後測試了 NAT 閘道。

接著請檢閱 Azure 監視器中的計量，以了解 NAT 服務的運作。 診斷可用 SNAT 連接埠的資源耗盡相關問題。  新增額外的公用 IP 位址資源或公用 IP 前置詞資源 (或兩者)，都可以輕鬆地解決 SNAT 連接埠的資源耗盡問題。

- 了解[虛擬網路 NAT](./nat-overview.md)
- 深入了解 [NAT 閘道資源](./nat-gateway-resource.md)。
- [使用 Azure CLI 部署 NAT 閘道資源](./quickstart-create-nat-gateway-cli.md)的快速入門。
- [使用 Azure PowerShell 部署 NAT 閘道資源](./quickstart-create-nat-gateway-powershell.md)的快速入門。
- [使用 Azure 入口網站部署 NAT 閘道資源](./quickstart-create-nat-gateway-portal.md)的快速入門。

> [!div class="nextstepaction"]

