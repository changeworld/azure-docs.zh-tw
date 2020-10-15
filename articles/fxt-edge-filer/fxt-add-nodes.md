---
title: 教學課程：將節點新增至 Azure FXT Edge Filer 叢集
description: 了解如何將叢集節點新增至 Azure FXT Edge Filer 儲存體快取，並啟用高可用性 (HA) 功能。
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 5b62927930212fc7e59fc4329a29ceecbe2815e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185327"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>教學課程：將叢集節點新增至 Azure FXT Edge Filer 叢集

建立新的 Azure FXT Edge Filer 叢集只需要一個節點。 進行其他設定之前，您應該新增至少兩個其他節點，並啟用高可用性。 

此教學課程說明如何新增叢集節點，並啟用高可用性 (HA) 功能。 

在本教學課程中，您將了解： 

> [!div class="checklist"]
> * 如何將節點新增至 FXT 叢集
> * 如何啟用 HA

此教學課程中的步驟需要大約 45 分鐘才能完成。

開始此教學課程之前，請將您要新增之節點的電源開啟，並[設定初始密碼](fxt-node-password.md)。 

## <a name="1-load-the-cluster-nodes-page"></a>1.載入 [叢集節點] 頁面

在網頁瀏覽器中開啟叢集的控制台，並以系統管理員身分登入。 (詳細指示可在概觀文章的[開啟設定頁面](fxt-cluster-create.md#open-the-settings-pages)下找到。)

控制台會在開啟時顯示 [儀表板]  索引標籤。 

![控制台儀表板 (第一個索引標籤)](media/fxt-cluster-config/dashboard-1-node.png)

此影像新建立的叢集儀表板，該叢集中具有單一節點。

## <a name="2-locate-the-node-to-add"></a>2.找出要新增的節點

若要新增節點，請按一下 [設定]  索引標籤，然後在 [叢集]  區段中，選擇 [FXT 節點]  頁面。

![控制台 [設定] 索引標籤 (第二個索引標籤)，其已載入 [叢集] > [FXT 節點]](media/fxt-cluster-config/settings-fxt-nodes.png)

[FXT 節點 - 未加入]  清單會顯示所有未指派的 FXT 節點 (大部分資料中心只有幾個)。 尋找您想要新增至叢集的 FXT 節點。

> [!Tip] 
> 如果在 [未加入]  清單上找不到想要的節點，請檢查它是否符合下列需求：
> 
> * 已開啟電源，並已[設定根密碼](fxt-node-password.md)。
> * 已連線到您可以存取的網路。 如果您使用 VLAN，它必須位在與叢集相同的 VLAN 上。
> * 可以使用 Bonjour 通訊協定偵測它。 
>
>   有些防火牆設定會封鎖 Bonjour 所使用的 TCP/UDP 連接埠，導致 FXT 作業系統無法自動偵測節點。
> 
> 如果您想要新增的節點不在清單中，請嘗試下列解決方式： 
> 
> * 按一下 [手動探索]  按鈕，依 IP 位址來尋找。
> 
> * 手動指派臨時 IP 位址。 這很罕見，但如果您使用標記的 VLAN，而且節點不在正確的網路上，或網路不允許自我指派的 IP 位址時，就可能會需要這樣做。 依照此舊版文件中的指示來[手動設定靜態 IP 位址](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html)。

節點名稱、IP 位址、軟體版本以及資格狀態會顯示在清單中。 一般來說，[狀態]  欄會顯示 [想要加入]，或描述讓節點失去資格無法加入叢集的系統或硬體問題。

[動作]  欄有按鈕，可讓您將節點新增至叢集，或更新其軟體。 更新按鈕會自動安裝符合叢集中現有節點的軟體版本。

叢集中的所有節點都必須使用相同版本的作業系統，但在新增節點之前，您不需要更新軟體。 按一下 [允許加入]  按鈕之後，叢集加入程序會自動檢查並安裝與叢集上的版本相符的 OS 軟體。

若要深入了解此頁面上的選項，請參閱《叢集設定指南》中的[叢集   > FXT 節點  ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html)。

## <a name="3-click-the-allow-to-join-button"></a>3.按一下 [允許加入] 按鈕 

在您想要新增之節點的 [動作]  功能表中，按一下 [允許加入]  按鈕。

按一下該按鈕之後，隨著節點的軟體更新以準備新增至叢集，其狀態可能會變更。 

下面的影像顯示正在加入叢集的節點 (很可能正在先取得 OS 更新，然後才新增)。 在 [動作]  欄中，正在新增至叢集的節點不會顯示任何按鈕。

![節點表的一列，其中顯示節點名稱、IP 位址、軟體版本、「允許加入」訊息，以及空白的最後一欄](media/fxt-cluster-config/node-join-in-process.png)

幾分鐘後，新的節點應會顯示在 [FXT 節點]  設定頁面頂端的叢集節點清單中。 

重複此程序，以將其他節點新增至叢集。 您不需要等到某個完成加入叢集，就可以開始新增另一個節點。

## <a name="enable-high-availability"></a>啟用高可用性

在將第二個節點新增至叢集之後，您可能會在控制台的儀表板上看到未設定高可用性功能的警告訊息。 

高可用性 (HA) 可在其中一個節點故障時，讓叢集節點相互彌補。 預設不會啟用 HA。

![儀表板索引標籤，其中 [條件] 表格中有「叢集有一個以上的節點，但 HA 未啟用...」的訊息](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> 在您的叢集中有至少三個節點之前，請勿啟用 HA。

請依照此程序來開啟 HA： 

1. 在 [設定]  索引標籤的 [叢集]  區段中，載入 [高可用性]  頁面。

   ![HA 設定頁面 ([叢集] > [高可用性])。 [啟用 HA] 核取方塊位於頂端，而提交按鈕位於底部。](media/fxt-cluster-config/enable-ha.png)

2. 按一下標示為 [啟用 HA]  的方塊，然後按一下 [提交]  按鈕。 

[儀表板]  上會出現警示，以確認已啟用 HA。

![顯示「HA 目前已全部設定完成」訊息的儀表板表格](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>後續步驟

在叢集中新增所有節點之後，可透過設定叢集的長期儲存體，繼續進行設定。

> [!div class="nextstepaction"]
> [新增後端儲存體並設定虛擬命名空間](fxt-add-storage.md)