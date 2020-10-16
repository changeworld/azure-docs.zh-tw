---
title: 教學課程：設定 ExpressRoute 線路的對等互連 - Azure 入口網站
description: 本教學課程示範如何使用 Azure 入口網站，建立和佈建 ExpressRoute 私人和 Microsoft 對等互連。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: cad098ed2dedc7abba57394ef1e26b9b7c87cd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855466"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站建立和修改 ExpressRoute 線路的對等互連

本教學課程示範如何使用 Azure 入口網站，建立和管理 Azure Resource Manager ExpressRoute 線路的路由設定。 您還可以檢查狀態、更新，或是刪除與取消佈建 ExpressRoute 線路的對等互連。 如果您想要對線路使用不同的方法，可選取下列清單中的文章：

> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [公用對等互連](about-public-peering.md)
> * [視訊 - 私用對等互連](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [視訊 - Microsoft 對等互連](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (傳統)](expressroute-howto-routing-classic.md)
> 

您可以為 ExpressRoute 線路設定私人對等互連和 Microsoft 對等互連 (針對新線路的 Azure 公用對等互連已被取代)。 您可以依自己選擇的任何順序設定對等互連。 不過，您必須確定一次只完成一個對等的設定。 如需路由網域和對等互連的詳細資訊，請參閱 [ExpressRoute 路由網域](expressroute-circuit-peerings.md)。 如需公用對等互連的詳細資訊，請參閱 [ExpressRoute 公用對等互連](about-public-peering.md)。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 設定、更新及刪除線路的 Microsoft 對等互連
> - 設定、更新及刪除線路的 Azure 私人對等互連

## <a name="prerequisites"></a>必要條件

* 開始設定之前，請確定您已檢閱下列頁面：
    * [先決條件](expressroute-prerequisites.md) 
    * [路由需求](expressroute-routing.md)
    * [工作流程](expressroute-workflows.md)
* 您必須擁有作用中的 ExpressRoute 線路。 繼續之前，請遵循指示來[建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)，並由您的連線提供者來啟用該線路。 若要設定對等互連，ExpressRoute 線路必須處於已佈建及已啟用的狀態。 
* 如果您打算使用共用金鑰/MD5 雜湊，請務必在通道的兩端使用金鑰。 限制是最多 25 個英數位元。 不支援特殊字元。 

這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。 如果您使用的服務提供者提供受控第 3 層服務 (通常是 IPVPN，如 MPLS)，連線提供者會為您設定和管理路由。 

> [!IMPORTANT]
> 我們目前不會透過服務管理入口網站來公告服務提供者所設定的對等。 我們正努力在近期推出這項功能。 設定 BGP 對等互連之前，請洽詢您的服務提供者。
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft 對等互連

本節將協助您為 ExpressRoute 線路建立、取得、更新和刪除 Microsoft 對等互連設定。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日以前設定之 ExpressRoute 線路的 Microsoft 對等互連，會透過 Microsoft 對等互連公告所有服務首碼，即使未定義路由篩選也一樣。 在 2017 年 8 月 1 日當日或以後設定之 ExpressRoute 線路的 Microsoft 對等互連，不會公告任何首碼，直到路由篩選連結至線路為止。 如需詳細資訊，請參閱[設定 Microsoft 對等互連的路由篩選](how-to-routefilter-powershell.md)。
> 
> 

### <a name="to-create-microsoft-peering"></a>建立 Microsoft 對等

1. 設定 ExpressRoute 電路。 檢查**提供者狀態**以確定連線提供者已完整佈建線路，再繼續作業。

   若您的連線提供者提供受控第 3 層服務，您可以要求連線提供者為您啟用 Microsoft 對等互連。 您不需要遵循後續幾節所列的指示。 不過，如果您的連線提供者不管理路由，請在建立線路之後繼續進行這些步驟。

   **線路 - 提供者狀態：未佈建**

   [![顯示 ExpressRoute 示範線路 [概觀] 頁面的螢幕擷取畫面，其中具有紅色方塊醒目提示設為「未佈建」的提供者狀態。](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **線路 - 提供者狀態：已佈建**

   [![顯示 ExpressRoute 示範線路 [概觀] 頁面的螢幕擷取畫面，其中具有紅色方塊醒目提示設為「已佈建」的提供者狀態。](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. 設定線路的 Microsoft 對等。 繼續之前，請確定您擁有下列資訊。

   * 主要連結的 /30 子網路。 地址區塊必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 前置詞。 您會從這個子網路將第一個可用 IP 位址指派給路由器，因為 Microsoft 會將第二個可用 IP 用於其路由器。
   * 次要連結的 /30 子網路。 地址區塊必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 前置詞。 您會從這個子網路將第一個可用 IP 位址指派給路由器，因為 Microsoft 會將第二個可用 IP 用於其路由器。
   * 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。 如果同時需要主要和次要連結，則必須使用相同的 VLAN 識別碼。
   * 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。
   * 公告的首碼：您要提供一份您打算透過 BGP 工作階段公告的所有前置詞清單。 只接受公用 IP 位址首碼。 如果計劃傳送一組首碼，可以傳送以逗號分隔的清單。 這些首碼必須在 RIR / IRR 中註冊給您。
   * **選用：** 客戶 ASN：如果您要公告的前置詞未註冊給對等 AS 編號，您可以指定其所註冊的 AS 編號。
   * 路由登錄名稱：您可以指定可供註冊 AS 編號和首碼的 RIR / IRR。
   * **選用：** MD5 雜湊 (如果選擇使用)。
3. 如下列範例所示，您可以選取要設定的對等互連。 選取 Microsoft 對等資料列。

   [![選取 Microsoft 對等互連資料列](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "選取 Microsoft 對等互連資料列")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. 設定 Microsoft 對等。 指定所有參數後，請**儲存**組態。 下圖顯示範例組態：

   ![設定 Microsoft 對等互連](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft 會驗證指定的「已公告公用首碼」和「對等互連 ASN」(或「客戶 ASN」) 是否已在網際網路路由登錄中指派給您。 如果您要從另一個實體取得公用首碼，以及如果未使用路由登錄來記錄指派，則自動驗證將不會完成，而且需要手動驗證。 如果自動驗證失敗，您會看到「需要驗證」訊息。 
>
> 如果您看到「需要驗證」訊息，請收集其中顯示實體已將公用前置詞指派給您組織的文件，而此實體列示為路由登錄中的前置詞擁有者，然後開啟支援票證，提交這些文件進行手動驗證。 
>

   如果您的線路處於「需要驗證」狀態，您必須開啟支援票證，向我們的支援小組出示首碼擁有權的證明。 如下列範例所示，您可以直接從入口網站開立支援票證：

   ![需要驗證 - 支援票證](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. 成功接受設定後，會出現類似下圖的畫面：

   ![對等互連狀態：已設定](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "對等互連狀態：已設定")

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>檢視 Microsoft 對等詳細資訊

選取對等互連的資料列，即可檢視 Microsoft 對等互連的屬性。

[![檢視 Microsoft 對等互連屬性](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "檢視屬性")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>更新 Microsoft 對等組態

您可以選取想要修改的對等互連資料列，然後修改對等互連屬性並儲存您的修改。

![選取對等互連資料列](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Azure 私用對等互連

本節將協助您為 ExpressRoute 線路建立、取得、更新和刪除 Azure 私用對等互連設定。

### <a name="to-create-azure-private-peering"></a>建立 Azure 私用對等

1. 設定 ExpressRoute 電路。 先確定連線提供者已完整佈建電路，再繼續作業。 

   若您的連線提供者是提供受控第 3 層服務，您可以要求連線提供者為您啟用 Azure 私人對等互連。 您不需要遵循後續幾節所列的指示。 不過，如果您的連線提供者不管理路由，請在建立線路之後繼續進行後續步驟。

   **線路 - 提供者狀態：未佈建**

   [![顯示 ExpressRoute 示範線路 [概觀] 頁面的螢幕擷取畫面，其中具有紅色方塊醒目提示設為「未佈建」的提供者狀態。](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **線路 - 提供者狀態：已佈建**

   [![顯示 ExpressRoute 示範線路 [概觀] 頁面的螢幕擷取畫面，其中具有紅色方塊醒目提示設為「已佈建」的提供者狀態。](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. 設定線路的 Azure 私用對等。 繼續執行接下來的步驟之前，請確定您有下列項目：

   * 主要連結的 /30 子網路。 子網路不能在保留給虛擬網路的任何位址空間中。 您會從這個子網路將第一個可用 IP 位址指派給路由器，因為 Microsoft 會將第二個可用 IP 用於其路由器。
   * 次要連結的 /30 子網路。 子網路不能在保留給虛擬網路的任何位址空間中。 您會從這個子網路將第一個可用 IP 位址指派給路由器，因為 Microsoft 會將第二個可用 IP 用於其路由器。
   * 供建立此對等的有效 VLAN ID。 請確定線路有沒有其他對等使用相同的 VLAN ID。 如果同時需要主要和次要連結，則必須使用相同的 VLAN 識別碼。
   * 對等的 AS 編號。 您可以使用 2 位元組和 4 位元組 AS 編號。 除了編號 65515 到 65520 (含) 以外，您可以將私人 AS 編號用於此對等互連。
   * 當您設定私人對等互連時，必須透過 BGP 將路由從內部部署邊緣路由器公告至 Azure。
   * **選用：** MD5 雜湊 (如果選擇使用)。
3. 如下列範例所示，選取 Azure 私人對等互連資料列：

   [![選取私人對等互連資料列](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "選取私人對等互連資料列")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. 設定私用對等。 指定所有參數後，請**儲存**組態。

   ![設定私用對等互連](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. 成功接受設定後，會出現類似下列範例的畫面：

   ![已儲存私人對等互連](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>檢視 Azure 私用對等詳細資訊

選取 Azure 私用對等，即可檢視該對等的屬性。

[![檢視私人對等互連屬性](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "檢視私人對等互連屬性")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>更新 Azure 私用對等組態

您可以選取對等的資料列並修改對等屬性。 更新之後，請儲存您的變更。

![更新私用對等互連](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

## <a name="clean-up-resources"></a>清除資源

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>刪除 Microsoft 對等

如下圖所示，您可以按一下刪除圖示來移除對等互連設定：

![刪除對等互連](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>刪除 Azure 私用對等

如下圖所示，您可以選取刪除圖示來移除對等互連設定：

> [!WARNING]
> 執行此範例之前，您必須確定已移除所有虛擬網路與 ExpressRoute Global Reach 連線。 
> 
> 

![刪除私用對等互連](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="next-steps"></a>後續步驟

設定 Azure 私人對等互連之後，您可以建立 ExpressRoute 閘道，以將虛擬網路連結至線路。 

> [!div class="nextstepaction"]
> [為 ExpressRoute 設定虛擬網路閘道](expressroute-howto-add-gateway-resource-manager.md)
