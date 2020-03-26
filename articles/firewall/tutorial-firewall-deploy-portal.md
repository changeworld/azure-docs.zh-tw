---
title: 教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆
description: 在本教學課程中，您將了解如何使用 Azure 入口網站部署及設定 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223645"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆

控制輸出網路存取是整體網路安全性計畫的重要部分。 例如，您可以限制對網站的存取。 或者，限制可存取的輸出 IP 位址和連接埠。

要控制從 Azure 子網路的輸出網路存取，使用 Azure 防火牆是可行的方式之一。 透過 Azure 防火牆，您可以設定：

* 應用程式規則，用以定義可從子網路存取的完整網域名稱 (FQDN)。
* 網路規則，用以定義來源位址、通訊協定、目的地連接埠和目的地位址。

當您將網路流量路由傳送到防火牆作為子網路預設閘道時，網路流量必須遵守設定的防火牆規則。

在本教學課程中，您會建立包含三個子網路的簡易單一 VNet，以進行簡單的部署。 針對生產部署，建議使用[中樞與輪輻模型](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 防火牆位於自己的 VNet 中。 工作負載伺服器位於相同區域中的對等互連 VNet，其中包含一個或多個子網路。

* **AzureFirewallSubnet** - 防火牆位於此子網路。
* **Workload-SN** - 工作負載伺服器位於此子網路。 此子網路的網路流量會通過防火牆。
* **Jump-SN** - 跳板機位於此子網路。 跳板機具有公用 IP 位址，您可以使用遠端桌面與其連線。 接著，您可以從此處 (使用其他的遠端桌面) 連線到工作負載伺服器。

![教學課程網路基礎結構](media/tutorial-firewall-rules-portal/Tutorial_network.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定測試網路環境
> * 部署防火牆
> * 建立預設路由
> * 設定允許存取 www.google.com 的應用程式規則
> * 設定允許存取外部 DNS 伺服器的網路規則
> * 測試防火牆

您可以使用 [Azure PowerShell](deploy-ps.md) 完成本教學課程。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="set-up-the-network"></a>設定網路

首先，請建立資源群組，以包含部署防火牆所需的資源。 接著建立 VNet、子網路，和測試伺服器。

### <a name="create-a-resource-group"></a>建立資源群組

此資源群組中包含了教學課程中提到的所有資源。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在 Azure 入口網站功能表上，選取 [資源群組]  ，或從任何頁面搜尋並選取 [資源群組]  。 然後選取 [新增]  。
3. 在 [資源群組名稱]  中，輸入 *Test-FW-RG*。
4. 在 [訂用帳戶]  中，選取您的訂用帳戶。
5. 在 [資源群組位置]  中，選取位置。 您建立的所有其他資源都必須位於相同的位置。
6. 選取 [建立]  。

### <a name="create-a-vnet"></a>建立 VNet

此 VNet 會包含三個子網路。

> [!NOTE]
> AzureFirewallSubnet 子網路的大小是 /26。 如需有關子網路大小的詳細資訊，請參閱 [Azure 防火牆的常見問題集](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)。

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]  。
1. 選取 [網路]   > [虛擬網路]  。
1. 在 [名稱]  中，鍵入 **Test-FW-VN**。
1. 在 [位址空間]  中，鍵入 **10.0.0.0/16**。
1. 在 [訂用帳戶]  中，選取您的訂用帳戶。
1. 在 [資源群組]  中，選取 [Test-FW-RG]  。
1. 在 [位置]  中，選取您先前使用的相同位置。
1. 在 [子網路]  底下的 [名稱]  中，鍵入 **AzureFirewallSubnet**。 防火牆會在此子網路中，且子網路名稱**必須**是 AzureFirewallSubnet。
1. 在 [位址範圍]  中，輸入 **10.0.1.0/26**。
1. 接受其他預設設定，然後選取 [建立]  。

### <a name="create-additional-subnets"></a>建立其他子網路

接下來，建立跳板機的子網路，以及工作負載伺服器的子網路。

1. 在 Azure 入口網站功能表上，選取 [資源群組]  ，或從任何頁面搜尋並選取 [資源群組]  。 然後選取 **Test-FW-RG**。
2. 選取 **Test-FW-VN** 虛擬網路。
3. 選取 [子網路]   > [+子網路]  。
4. 在 [名稱]  中，鍵入 **Workload-SN**。
5. 在 [位址範圍]  中，鍵入 **10.0.2.0/24**。
6. 選取 [確定]  。

建立另一個名為 **Jump-SN** 的子網路，位址範圍 **10.0.3.0/24**。

### <a name="create-virtual-machines"></a>建立虛擬機器

現在建立跳板和工作負載虛擬機器，並將它們放在適當的子網路中。

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]  。
2. 選取 [計算]  ，然後選取 [精選] 清單中的 [Windows Server 2016 Datacenter]  。
3. 依虛擬機器輸入這些值：

   |設定  |值  |
   |---------|---------|
   |資源群組     |**Test-FW-RG**|
   |虛擬機器名稱     |**Srv-Jump**|
   |區域     |同前|
   |系統管理員使用者名稱     |**azureuser**|
   |密碼     |**Azure123456!**|

4. 在 [輸入連接埠規則]  底下，針對 [公用輸入連接埠]  選取 [允許選取的連接埠]  。
5. 在 [選取輸入連接埠]  中，選取 [RDP (3389)]  。

6. 接受其他預設值，然後選取 [下一步：  磁碟]。
7. 接受磁碟預設值，然後選取 [下一步：  網路]。
8. 確定您已選取 [Test-FW-VN]  作為虛擬網路，而且子網路是 [Jump-SN]  。
9. 針對 [公用 IP]  ，接受預設新的公用 IP 位址名稱 (Srv-Jump-ip)。
11. 接受其他預設值，然後選取 [下一步：  管理]。
12. 選取 [關閉]  來停用開機診斷。 接受其他預設值，然後選取 [檢閱 + 建立]  。
13. 檢閱摘要頁面上的設定，然後選取 [建立]  。

使用下表中的資訊來設定另一個名為 **Srv-Work** 的虛擬機器。 其餘的組態與 Srv-Jump 虛擬機器相同。

|設定  |值  |
|---------|---------|
|子網路|**Workload-SN**|
|公用 IP|**None**|
|公用輸入連接埠|**None**|

## <a name="deploy-the-firewall"></a>部署防火牆

將防火牆部署到 VNet 中。

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]  。
2. 在搜尋方塊中輸入 **firewall**，然後按 **Enter**。
3. 選取 [防火牆]  ，然後選取 [建立]  。
4. 在 [建立防火牆]  頁面上，使用下表來設定防火牆：

   |設定  |值  |
   |---------|---------|
   |訂用帳戶     |\<您的訂用帳戶\>|
   |資源群組     |**Test-FW-RG** |
   |名稱     |**Test-FW01**|
   |Location     |選取您先前使用的相同位置|
   |選擇虛擬網路     |**使用現有項目**︰**Test-FW-VN**|
   |公用 IP 位址     |**新增**。 公用 IP 位址必須是標準 SKU 類型。|

5. 選取 [檢閱 + 建立]  。
6. 檢閱摘要，然後選取 [建立]  來建立防火牆。

   這需要幾分鐘才能部署。
7. 部署完成之後，請前往 **Test-FW-RG** 資源群組，然後選取 **Test-FW01** 防火牆。
8. 請記下私人 IP 位址。 稍後當您建立預設路由時將使用到它。

## <a name="create-a-default-route"></a>建立預設路由

在 **Workload-SN** 子網路中，設定通過防火牆的輸出預設路由。

1. 在 Azure 入口網站功能表上，選取 [所有服務]  ，或從任何頁面搜尋並選取 [所有服務]  。
2. 在 [網路]  底下，選取 [路由表]  。
3. 選取 [新增]  。
4. 在 [名稱]  中，鍵入 **Firewall-route**。
5. 在 [訂用帳戶]  中，選取您的訂用帳戶。
6. 在 [資源群組]  中，選取 [Test-FW-RG]  。
7. 在 [位置]  中，選取您先前使用的相同位置。
8. 選取 [建立]  。
9. 選取 [重新整理]  ，然後選取 **Firewall-route** 路由表。
10. 選取 [子網路]  ，然後選取 [建立關聯]  。
11. 選取 [虛擬網路]   > [Test-FW-VN]  。
12. 在 [子網路]  中，選取 [Workload-SN]  。 請確定您只為此路由選取 **Workload-SN** 子網路，否則防火牆將無法正常運作。

13. 選取 [確定]  。
14. 選取 [路由]  ，然後選取 [確定]  。
15. 在 [路由名稱]  中，鍵入 **fw-dg**。
16. 在 [位址首碼]  中，鍵入 **0.0.0.0/0**。
17. 在 [下一個躍點類型]  中，選取 [虛擬設備]  。

    Azure 防火牆實際上是受控服務，但虛擬設備可在此情況下運作。
18. 在 [下一個躍點位址]  中，鍵入您先前記下的防火牆私人 IP 位址。
19. 選取 [確定]  。

## <a name="configure-an-application-rule"></a>設定應用程式規則

此應用程式規則允許對 www.google.com 進行輸出存取。

1. 開啟 **Test-FW-RG**，然後選取 **Test-FW01** 防火牆。
2. 在 [Test-FW01]  頁面的 [設定]  底下，選取 [規則]  。
3. 選取 [應用程式規則集合]  索引標籤。
4. 選取 [新增應用程式規則集合]  。
5. 在 [名稱]  中，鍵入 **App-Coll01**。
6. 在 [優先順序]  中，鍵入 **200**。
7. 在 [動作]  中，選取 [允許]  。
8. 在 [規則]  、[目標 FQDN]  底下，針對 [名稱]  輸入 **Allow-Google**。
9. 針對 [來源類型]  ，選取 [IP 位址]  。
10. 針對 [來源]  ，輸入 **10.0.2.0/24**。
11. 在 [通訊協定:連接埠]  中，鍵入 **http、https**。
12. 在 [目標 FQDN]  中，鍵入 **www.google.com**
13. 選取 [新增]  。

Azure 防火牆包含內建的規則集合，適用於依預設允許的基礎結構 FQDN。 這些 FQDN 是平台特定的，且無法用於其他用途。 如需詳細資訊，請參閱[基礎結構 FQDN](infrastructure-fqdns.md)。

## <a name="configure-a-network-rule"></a>設定網路規則

此網路規則允許透過連接埠 53，對兩個 IP 位址進行輸出存取 (DNS)。

1. 選取 [網路規則集合]  索引標籤。
2. 選取 [新增網路規則集合]  。
3. 在 [名稱]  中，鍵入 **Net-Coll01**。
4. 在 [優先順序]  中，鍵入 **200**。
5. 在 [動作]  中，選取 [允許]  。
6. 在 [規則]  、[IP 位址]  底下，針對 [名稱]  輸入 **Allow-DNS**。
7. 在 [通訊協定]  中，選取 [UDP]  。
9. 針對 [來源類型]  ，選取 [IP 位址]  。
1. 針對 [來源]  ，輸入 **10.0.2.0/24**。
2. 針對 [目的地位址]  ，輸入 **209.244.0.3,209.244.0.4**

   這些是由 CenturyLink 運作的公用 DNS 伺服器。
1. 在 [目的地連接埠]  中，鍵入 **53**。
2. 選取 [新增]  。

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>變更 **Srv-Work** 網路介面的主要和次要 DNS 位址

本教學課程中，您可以就測試目的設定伺服器的主要和次要 DNS 位址。 這不是一般的 Azure 防火牆需求。

1. 在 Azure 入口網站功能表上，選取 [資源群組]  ，或從任何頁面搜尋並選取 [資源群組]  。 選取 **Test-FW-RG** 資源群組。
2. 選取 **Srv-Work** 虛擬機器的網路介面。
3. 選取 [設定]  底下的 [DNS 伺服器]  。
4. 選取 [DNS 伺服器]  底下的 [自訂]  。
5. 在 [新增 DNS 伺服器]  文字方塊中，鍵入 **209.244.0.3**，然後在下一個文字方塊中鍵入 **209.244.0.4**。
6. 選取 [儲存]  。
7. 重新啟動 **Srv-Work** 虛擬機器。

## <a name="test-the-firewall"></a>測試防火牆

現在請測試防火牆，以確認其運作符合預期。

1. 從 Azure 入口網站中，檢閱 **Srv-Work** 虛擬機器的網路設定，並記下私人 IP 位址。
2. 將遠端桌面連線到 **Srv-Jump** 虛擬機器，然後登入。 登入之後，開啟對 **Srv-Work** 私人 IP 位址的遠端桌面連線。
3. 開啟 Internet Explorer 並瀏覽至 https://www.google.com 。
4. 在 Internet Explorer 安全性警示上，選取 [確認]   > [關閉]  。

   您應該會看到 Google 首頁。

5. 瀏覽至 https://www.microsoft.com 。

   您應該會遭到防火牆封鎖。

因此，現在您已確認防火牆規則正在運作：

* 您可以瀏覽至允許 FQDN 的防火牆規則，但不可瀏覽至任何其他的防火牆規則。
* 您可以使用設定的外部 DNS 伺服器來解析 DNS 名稱。

## <a name="clean-up-resources"></a>清除資源

您可以保留防火牆資源供下一個教學課程使用。若不再需要，則可刪除 **Test-FW-RG** 資源群組，以將所有防火牆相關資源刪除。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
