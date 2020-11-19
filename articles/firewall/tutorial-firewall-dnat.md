---
title: 教學課程：使用入口網站以 Azure 防火牆 DNAT 篩選輸入網際網路流量
description: 在本教學課程中，您將了解如何使用 Azure 入口網站部署及設定 Azure 防火牆 DNAT。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 71eda40abd38b4885b8e88085e338667b608902f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655131"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站以 Azure 防火牆 DNAT 篩選輸入網際網路流量

您可以設定 Azure 防火牆目的地網路位址轉譯 (DNAT)，將輸入網際網路流量轉譯及篩選至您的子網路。 當您設定 DNAT 時，NAT 規則集合動作是設為 **Dnat**。 接著，NAT 規則集合中的每個規則均可用來將防火牆公用 IP 和連接埠轉譯為私人 IP 和連接埠。 DNAT 規則會隱含地新增對應的網路規則，以允許已轉譯的流量。 若要覆寫這個行為，您可以明確地使用符合已轉譯流量的拒絕規則來新增網路規則集合。 若要深入了解 Azure 防火牆規則處理邏輯，請參閱 [Azure 防火牆規則處理邏輯](rule-processing.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定測試網路環境
> * 部署防火牆
> * 建立預設路由
> * 設定 DNAT 規則
> * 測試防火牆

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。



## <a name="create-a-resource-group"></a>建立資源群組

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在 Azure 入口網站首頁上選取 [資源群組]  ，然後選取 [新增]  。
3. 在 [資源群組名稱]  中，輸入 **RG-DNAT-Test**。
4. 在 [訂用帳戶]  中，選取您的訂用帳戶。
5. 在 [資源群組位置]  中，選取位置。 您建立的所有後續資源都必須位於相同的位置。
6. 選取 [建立]  。

## <a name="set-up-the-network-environment"></a>設定網路環境

針對本教學課程，您會建立兩個對等互連 Vnet：

- **VN-Hub** - 防火牆位於此 VNet 中。
- **VN-Spoke** - 工作負載伺服器位於此 VNet 中。

首先建立 Vnet，然後將其對等互連。

### <a name="create-the-hub-vnet"></a>建立中樞 VNet

1. 從 Azure 入口網站首頁，選取 [所有服務]  。
2. 在 [網路]  底下，選取 [虛擬網路]  。
3. 選取 [新增]  。
4. 在 [名稱]  中，輸入 **VN-Hub**。
5. 在 [位址空間]  中，鍵入 **10.0.0.0/16**。
6. 在 [訂用帳戶]  中，選取您的訂用帳戶。
7. 在 [資源群組]  中，選取 [使用現有的]  ，然後選取 [RG-DNAT-Test]  。
8. 在 [位置]  中，選取您先前使用的相同位置。
9. 在 [子網路]  底下的 [名稱]  中，鍵入 **AzureFirewallSubnet**。

     防火牆會在此子網路中，且子網路名稱 **必須** 是 AzureFirewallSubnet。
     > [!NOTE]
     > AzureFirewallSubnet 子網路的大小是 /26。 如需有關子網路大小的詳細資訊，請參閱 [Azure 防火牆的常見問題集](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)。

10. 在 [位址範圍]  中，輸入 **10.0.1.0/26**。
11. 使用其他預設設定，然後選取 [建立]  。

### <a name="create-a-spoke-vnet"></a>建立輪輻 VNet

1. 從 Azure 入口網站首頁，選取 [所有服務]  。
2. 在 [網路]  底下，選取 [虛擬網路]  。
3. 選取 [新增]  。
4. 在 [名稱]  中，輸入 **VN-Spoke**。
5. 在 [位址空間]  中，輸入 **192.168.0.0/16**。
6. 在 [訂用帳戶]  中，選取您的訂用帳戶。
7. 在 [資源群組]  中，選取 [使用現有的]  ，然後選取 [RG-DNAT-Test]  。
8. 在 [位置]  中，選取您先前使用的相同位置。
9. 在 [子網路]  底下的 [名稱]  中，輸入 **SN-Workload**。

    伺服器會在此子網路中。
10. 在 [位址範圍]  中，輸入 **192.168.1.0/24**。
11. 使用其他預設設定，然後選取 [建立]  。

### <a name="peer-the-vnets"></a>將 VNet 對等互連

現在對等互連兩個 VNet。

1. 選取 [VN-Hub]  虛擬網路。
2. 在 [設定]  底下，選取 [對等互連]  。
3. 選取 [新增]  。
4. 輸入 **Peer-HubSpoke**，作為 [從 VN-Hub 到 VN-Spoke 的對等互連名稱]  。
5. 針對虛擬網路選取 [VN-Spoke]  。
6. 輸入 **Peer-SpokeHub**，作為 [從 VN-Spoke 到 VN-Hub 的對等互連名稱]  。
7. 針對 [允許將流量從 VN-Spoke 轉送至 VN-Hub]  ，選取 [啟用]  。
8. 選取 [確定]  。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

建立工作負載虛擬機器，並將它放在 **SN-Workload** 子網路中。

1. 從 Azure 入口網站功能表選取 [建立資源]  。
2. 在 [熱門]  底下，選取 [Windows Server 2016 Datacenter]  。

**基本概念**

1. 在 [訂用帳戶]  中，選取您的訂用帳戶。
1. 在 [資源群組]  中，選取 [使用現有的]  ，然後選取 [RG-DNAT-Test]  。
1. 針對 [虛擬機器名稱]  ，輸入 **Srv-Workload**。
1. 針對 [區域]  ，選取您先前使用的相同位置。
1. 鍵入使用者名稱和密碼。
1. 完成時，選取 [下一步:  磁碟]。

**磁碟**
1. 完成時，選取 [下一步:  網路]。

**網路功能**

1. 針對 [虛擬網路]  ，選取 [VN-Spoke]  。
2. 在 [子網路]  中，選取 [SN-Workload]  。
3. 針對 [公用 IP 位址]  ，選取 [無]  。
4. 針對 [公用輸入連接埠]  ，選取 [無]  。 
2. 保留其他預設設定，然後選取 [下一步：  管理]。

**管理**

1. 針對 [開機診斷]  ，選取 [關閉]  。
1. 選取 [檢閱 + 建立]  。

**檢閱 + 建立**

檢閱摘要，然後選取 [建立]  。 這需要幾分鐘才能完成。

部署完成之後，請記下虛擬機器的私人 IP 位址。 稍後當您設定防火牆時將會用到該位址。 選取虛擬機器名稱，然後在 [設定]  底下選取 [網路]  ，以尋找私人 IP 位址。

## <a name="deploy-the-firewall"></a>部署防火牆

1. 從入口網站首頁選取 [建立資源]  。
2. 選取 [網路]  ，並在 [精選]  後選取 [查看全部]  。
3. 選取 [防火牆]  ，然後選取 [建立]  。 
4. 在 [建立防火牆]  頁面上，使用下表來設定防火牆：

   |設定  |值  |
   |---------|---------|
   |名稱     |FW-DNAT-test|
   |訂用帳戶     |\<your subscription\>|
   |資源群組     |**使用現有的**：RG-DNAT-Test |
   |Location     |選取您先前使用的相同位置|
   |選擇虛擬網路     |**使用現有的**：VN-Hub|
   |公用 IP 位址     |**建立新項目**。 公用 IP 位址必須是標準 SKU 類型。|

5. 選取 [檢閱 + 建立]。
6. 檢閱摘要，然後選取 [建立] 來建立防火牆。

   這需要幾分鐘才能部署。
7. 部署完成之後，請移至 **RG-DNAT-Test** 資源群組，然後選取 **FW-DNAT-test** 防火牆。
8. 請記下私人 IP 位址。 稍後當您建立預設路由時將使用到它。

## <a name="create-a-default-route"></a>建立預設路由

在 **SN-Workload** 子網路中，您要設定通過防火牆的輸出預設路由。

1. 從 Azure 入口網站首頁，選取 [所有服務]。
2. 在 [網路] 底下，選取 [路由表]。
3. 選取 [新增]。
4. 在 [名稱] 中，輸入 **RT-FWroute**。
5. 在 [訂用帳戶] 中，選取您的訂用帳戶。
6. 在 [資源群組] 中，選取 [使用現有的]，然後選取 [RG-DNAT-Test]。
7. 在 [位置] 中，選取您先前使用的相同位置。
8. 選取 [建立]。
9. 選取 [重新整理]，然後選取 **RT-FWroute** 路由表。
10. 選取 [子網路]，然後選取 [建立關聯]。
11. 選取 [虛擬網路]，然後選取 **VN-Spoke**。
12. 在 [子網路] 中，選取 [SN-Workload]。
13. 選取 [確定]。
14. 選取 [路由]，然後選取 [確定]。
15. 在 [路由名稱] 中，鍵入 **FW-DG**。
16. 在 [位址首碼] 中，鍵入 **0.0.0.0/0**。
17. 在 [下一個躍點類型] 中，選取 [虛擬設備]。

    Azure 防火牆實際上是受控服務，但虛擬設備可在此情況下運作。
18. 在 [下一個躍點位址] 中，鍵入您先前記下的防火牆私人 IP 位址。
19. 選取 [確定]。

## <a name="configure-a-nat-rule"></a>設定 NAT 規則

1. 開啟 **RG-DNAT-Test**，然後選取 **FW-DNAT-test** 防火牆。 
2. 在 [FW-DNAT-test] 頁面的 [設定] 底下，選取 [規則]。 
3. 選取 [新增 NAT 規則集合]。 
4. 在 [名稱] 中，輸入 **RC-DNAT-01**。 
5. 在 [優先順序] 中，鍵入 **200**。 
6. 在 [規則] 底下的 [名稱] 中，輸入 **RL-01**。
7. 在 [通訊協定] 中，選取 [TCP]。
8. 在 [來源位址] 中，輸入 *。 
9. 在 [目的地位址] 中，輸入防火牆的公用 IP 位址。 
10. 在 [目的地連接埠] 中，輸入 **3389**。 
11. 在 [轉譯的位址] 中，輸入 Srv-Workload 虛擬機器的私人 IP 位址。 
12. 在 [轉譯的連接埠] 中，輸入 **3389**。 
13. 選取 [新增]。 

## <a name="test-the-firewall"></a>測試防火牆

1. 將遠端桌面連線至防火牆公用 IP 位址。 您應該會連線到 **Srv-Workload** 虛擬機器。
2. 關閉遠端桌面。

## <a name="clean-up-resources"></a>清除資源

您可以保留防火牆資源供下一個教學課程使用，若不再需要，則可刪除 **RG-DNAT-Test** 資源群組來刪除所有防火牆相關資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定測試網路環境
> * 部署防火牆
> * 建立預設路由
> * 設定 DNAT 規則
> * 測試防火牆

接下來，您可以監視 Azure 防火牆記錄。

> [!div class="nextstepaction"]
> [教學課程：監視 Azure 防火牆記錄](./firewall-diagnostics.md)