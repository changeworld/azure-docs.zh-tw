---
title: 在您自己的虛擬網路快速入門中創建 Azure 資料塊工作區
description: 本文介紹如何將 Azure 資料塊部署到虛擬網路。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132666"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>快速入門：在您自己的虛擬網路中創建 Azure 資料磚塊工作區

Azure 資料磚的預設部署將創建由資料磚塊管理的新虛擬網路。 此快速入門演示如何在您自己的虛擬網路中創建 Azure 資料塊工作區。 您還可以在工作區中創建 Apache Spark 群集。 

有關為何選擇在您自己的虛擬網路中創建 Azure 資料磚塊工作區的詳細資訊，請參閱[在 Azure 虛擬網路中部署 Azure 資料塊（VNet 注入）。](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

如果沒有 Azure 訂閱，請創建[一個免費帳戶](https://azure.microsoft.com/free/databricks/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 Azure[門戶](https://portal.azure.com/)。

> [!Note]
> 本教學課程不適用 **Azure 免費試用版的訂用帳戶**。
> 如果您有免費帳戶，請移至您的設定檔，並將訂用帳戶變更為**隨用隨付**。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。 然後，為您所在區域的 vCPU [移除消費限制](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)並[要求增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。 當您建立 Azure Databricks 工作區時，您可以選取 [試用版 (進階 - 14 天的免費 DBU)]**** 定價層，讓工作區可免費存取進階 Azure Databricks DBU 14 天。

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 從 Azure 入口網站功能表選取 [建立資源]****。 然後選擇**網路>虛擬網路**。

    ![在 Azure 門戶上創建虛擬網路](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. 在 **"創建虛擬網路"** 下，應用以下設置： 

    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |訂用帳戶|\<您的訂用帳戶\>|選取您要使用的 Azure 訂用帳戶。|
    |資源群組|資料磚-快速啟動|選擇 **"新建"** 並輸入帳戶的新資源組名稱。|
    |名稱|資料磚-快速啟動|為虛擬網路選擇名稱。|
    |區域|\<選取最接近使用者的區域\>|選擇可以託管虛擬網路的地理位置。 使用最靠近您的使用者的位置。|

    ![Azure 門戶上虛擬網路的基礎知識](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. 選擇 **"下一步"：IP 位址>** 並應用以下設置。 然後選擇 **"審閱 + 創建**"。
    
    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |IPv4 位址空間|10.2.0.0/16|以 CIDR 標記法標記法表示語的虛擬網路位址範圍。 CIDR 範圍必須介於 /16 和 /24 之間|
    |子網路名稱|default|為虛擬網路中的預設子網選擇名稱。|
    |子網路位址範圍|10.2.0.0/24|採用 CIDR 標記法的子網路位址範圍。 它必須包含在虛擬網路的位址空間中。 無法編輯正在使用的子網的位址範圍。|

    ![在 Azure 門戶上為虛擬網路設置 IP 配置](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. 在 **"審閱 + 創建**"選項卡上，選擇 **"創建**"以部署虛擬網路。 部署完成後，導航到虛擬網路，並在 **"設置"** 下選擇 **"位址空間**"。 在"*添加其他位址範圍*"框中，插入`10.179.0.0/16`並選擇 **"保存**"。

    ![Azure 虛擬網路位址空間](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>建立 Azure Databricks 工作區

1. 從 Azure 入口網站功能表選取 [建立資源]****。 然後選擇 **"分析>資料塊**。

    ![在 Azure 門戶上創建 Azure 資料磚塊工作區](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. 在**Azure 資料磚塊服務**下，應用以下設置：

    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |工作區名稱|資料磚-快速啟動|為 Azure 資料塊工作區選擇名稱。|
    |訂用帳戶|\<您的訂用帳戶\>|選取您要使用的 Azure 訂用帳戶。|
    |資源群組|資料磚-快速啟動|選擇用於虛擬網路的相同資源組。|
    |Location|\<選取最接近使用者的區域\>|選擇與虛擬網路相同的位置。|
    |定價層|選擇 [標準] 或 [進階]。|有關定價層的詳細資訊，請參閱[資料磚定價頁](https://azure.microsoft.com/pricing/details/databricks/)。|

    ![創建 Azure 資料磚塊工作區基礎知識](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. 在 **"基礎知識"** 頁上輸入完設置後，選擇 **"下一步：網路>** 並應用以下設置：

    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |在虛擬網路 （VNet） 中部署 Azure 資料磚塊工作區|是|此設置允許您在虛擬網路中部署 Azure 資料磚塊工作區。|
    |虛擬網路|資料磚-快速啟動|選擇您在上一節中創建的虛擬網路。|
    |公共子網名稱|公共子網|使用預設的公共子網名稱。|
    |公共子網 CIDR 範圍|10.179.64.0/18|使用 CIDR 範圍，最多包括 /26。|
    |專用子網名稱|私子網|使用預設專用子網名稱。|
    |專用子網 CIDR 範圍|10.179.0.0/18|使用 CIDR 範圍，最多包括 /26。|

    ![在 Azure 門戶上將 VNet 資訊添加到 Azure 資料磚塊工作區](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. 部署完成後，導航到 Azure 資料磚塊資源。 請注意，虛擬網路對等互連已禁用。 另請注意概述頁中的資源組和託管資源組。 

    ![Azure 資料磚塊概述在 Azure 門戶中](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    託管資源組不可修改，並且不用於創建虛擬機器。 您只能在管理的資源組中創建虛擬機器。

    ![Azure 資料磚託管資源組](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    當工作區部署失敗時，工作區仍將以失敗狀態創建。 刪除失敗的工作區並創建解決部署錯誤的新工作區。 刪除失敗的工作區時，託管資源組和任何成功部署的資源也會被刪除。

## <a name="create-a-cluster"></a>建立叢集

> [!NOTE]
> 若要使用免費帳戶建立 Azure Databricks 叢集，在建立叢集之前，請先移至您的設定檔，並將訂用帳戶變更為**隨用隨付**。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。

1. 返回到 Azure 資料磚塊服務，並在 **"概述"** 頁上選擇 **"啟動工作區**"。

2. 選擇**群集** > **= 創建群集**。 然後創建群集名稱，如*資料磚塊-快速啟動群集*，並接受剩餘的預設設置。 選取 [建立叢集]****。

    ![創建 Azure 資料磚塊群集](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 群集運行後，返回到 Azure 門戶中的託管資源組。 請注意新的虛擬機器、磁片、IP 位址和網路介面。 在每個具有 IP 位址的公共和專用子網中創建網路介面。  

    ![群集創建後 Azure 資料磚託管資源組](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. 返回到 Azure 資料磚塊工作區並選擇您創建的群集。 然後導航到**Spark UI**頁上的 **"執行器**"選項卡。 請注意，驅動程式和執行器的位址在專用子網範圍內。 在此示例中，驅動程式為 10.179.0.6，執行器為 10.179.0.4 和 10.179.0.5。 您的 IP 位址可能不同。

    ![Azure 資料磚火花 UI 執行器](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>清除資源

在完成本文後，您可以終止叢集。 若要這樣做，請從 Azure Databricks 工作區的左窗格中選取 [叢集]****。 對於您想要終止的叢集，將游標移到 [動作]**** 資料行底下的省略符號上，然後選取 [終止]**** 圖示。 這將停止群集。

如果不手動終止群集，它將自動停止，前提是您在創建群集時選擇了"創建群集時在**\_\_非活動幾分鐘後終止**"核取方塊。 在這種情況下，叢集將會在停止運作達指定時間後自動停止。

如果不希望重用群集，可以刪除在 Azure 門戶中創建的資源組。

## <a name="next-steps"></a>後續步驟

在本文中，您將在 Azure 資料塊中創建了一個 Spark 群集，該群集已部署到虛擬網路。 進入下一篇文章，瞭解如何使用 Azure 資料塊筆記本中的 JDBC 在虛擬網路中查詢 SQL Server Linux Docker 容器。  

> [!div class="nextstepaction"]
>[從 Azure 資料磚塊筆記本查詢虛擬網路中的 SQL Server Linux Docker 容器](vnet-injection-sql-server.md)
