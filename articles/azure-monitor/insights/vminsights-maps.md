---
title: 使用 Azure 監視器查看 VM 的應用依賴項
description: 映射是 VM Azure 監視器的一項功能。 它會自動探索 Windows 和 Linux 系統上的應用程式元件，並映射服務之間的通信。 本文詳細介紹了如何在各種方案中使用 Map 要素。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283849"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>使用 VM Azure 監視器的映射功能瞭解應用程式元件
在 VM 的 Azure 監視器中，您可以查看在 Azure 或環境中運行的 Windows 和 Linux 虛擬機器 （VM） 上發現的應用程式元件。 您可以通過兩種方式觀察 VM。 直接從 VM 查看地圖，或從 Azure 監視器查看地圖以查看跨 VM 組的元件。 本文將説明您瞭解這兩種查看方法以及如何使用地圖功能。 

如需設定適用於 VM 的 Azure 監視器相關資訊，請參閱[啟用適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。

## <a name="sign-in-to-azure"></a>登入 Azure
登錄到 Azure[門戶](https://portal.azure.com)。

## <a name="introduction-to-the-map-experience"></a>地圖體驗簡介
在深入瞭解地圖體驗之前，您應該瞭解它如何呈現和視覺化資訊。 無論是直接從 VM 還是從 Azure 監視器選擇地圖要素，地圖要素都提供了一致的體驗。 唯一的區別是，與 Azure 監視器不同，一個地圖顯示多層應用程式或群集的所有成員。

Map 功能通過發現具有以下功能的正在運行的進程來視覺化 VM 依賴項： 

- 伺服器之間的活動網路連接。
- 入站和出站連接延遲。
- 在指定時間範圍內跨任何 TCP 連接的體系結構的埠。  
 
展開 VM 以顯示流程詳細資訊，僅顯示與 VM 通信的進程。 用戶端組顯示連接到 VM 的前端用戶端的計數。 伺服器埠組顯示 VM 連接到的後端伺服器的計數。 展開伺服器埠組以查看通過該埠連接的伺服器的詳細清單。  

選擇 VM 時，右側的 **"屬性**"窗格將顯示 VM 的屬性。 屬性包括作業系統報告的系統資訊、Azure VM 的屬性以及匯總已發現連接的環圈圖。 

!["屬性"窗格](./media/vminsights-maps/properties-pane-01.png)

在窗格的右側，選擇 **"日誌事件"** 以顯示 VM 已發送到 Azure 監視器的資料清單。 此資料可用於查詢。  選擇任何記錄類型以打開 **"日誌"** 頁，您可以在其中看到該記錄類型的結果。 您還會看到針對 VM 篩選的預配置查詢。  

![日誌事件窗格](./media/vminsights-maps/properties-pane-logs-01.png)

關閉 **"日誌"** 頁並返回到"**屬性"** 窗格。 在此處，選擇 **"警報"** 以查看 VM 運行狀況標準警報。 "地圖"功能與 Azure 警報集成，以在選定時間範圍內顯示所選伺服器的警報。 伺服器將顯示當前警報的圖示，電腦**警報**窗格列出警報。 

!["警報"窗格](./media/vminsights-maps/properties-pane-alerts-01.png)

要使 Map 要素顯示相關警報，請創建適用于特定電腦的警報規則：

- 包括按電腦對警報進行分組的子句（例如，**按電腦間隔 1 分鐘**）。
- 警報基於指標。

有關 Azure 警報和創建警報規則的詳細資訊，請參閱[Azure 監視器 中的統一警報](../../azure-monitor/platform/alerts-overview.md)。

在右上角，"**圖例"** 選項描述地圖上的符號和角色。 要仔細查看地圖並將其移動，請使用右下角的縮放控制項。 您可以設置縮放級別，並將地圖與頁面大小擬合。  

## <a name="connection-metrics"></a>連接計量
"**連接**"窗格在 TCP 埠上顯示來自 VM 的選定連接的標準指標。 計量包括回應時間、每分鐘的要求、流量輸送量及連結。  

!["連接"窗格上的網路連接圖表](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>失敗的連線
地圖顯示進程和電腦的失敗連接。 虛線表示用戶端系統無法訪問進程或埠。 對於使用依賴項代理的系統，代理報告失敗的連接嘗試。 Map 功能通過觀察無法建立連接的 TCP 通訊端來監視進程。 此故障可能是由於防火牆、用戶端或伺服器中的配置錯誤或遠端服務不可用造成的。

![地圖上的連接失敗](./media/vminsights-maps/map-group-failed-connection-01.png)

瞭解失敗的連接可以説明您疑難排解、驗證遷移、分析安全性並瞭解服務的整體體系結構。 失敗的連接有時是無害的，但它們通常指向問題。 連接可能會失敗，例如，當容錯移轉環境突然變得無法訪問時，或者兩個應用程式層在雲遷移後無法相互通信時。

### <a name="client-groups"></a>用戶端群組
在地圖上，用戶端組表示連接到映射電腦的用戶端電腦。 單個用戶端組表示單個進程或電腦的用戶端。

![地圖上的用戶端組](./media/vminsights-maps/map-group-client-groups-01.png)

要查看用戶端組中系統的受監視用戶端和 IP 位址，請選擇該組。 組的內容如下所示。  

![用戶端組地圖上的 IP 位址清單](./media/vminsights-maps/map-group-client-group-iplist-01.png)

如果組包括受監視和未受監視的用戶端，則可以選擇組環圈圖的相應部分來篩選用戶端。

### <a name="server-port-groups"></a>伺服器埠組
伺服器埠組表示具有來自映射電腦的入站連接的伺服器上的埠。 該組包含伺服器埠和與該埠具有連接的伺服器數的計數。 選擇組以查看各個伺服器和連接。 

![地圖上的伺服器埠組](./media/vminsights-maps/map-group-server-port-groups-01.png)  

如果組包括受監視和未受監視的伺服器，則可以選擇組環圈圖的相應部分來篩選伺服器。

## <a name="view-a-map-from-a-vm"></a>從 VM 查看地圖 

要直接從 VM 訪問 VM 的 Azure 監視器，：

1. 在 Azure 入口網站中，選取 [虛擬機器]****。 
2. 從清單中選擇 VM。 在 **"監視**"部分中，選擇 **"見解**"。  
3. 選取 [對應]**** 索引標籤。

地圖通過發現正在運行的進程組和在指定時間範圍內具有活動網路連接的進程組和進程來視覺化 VM 的依賴關係。  

根據預設，對應會顯示過去 30 分鐘。 如果要查看依賴項過去的樣子，可以查詢最多一小時的歷史時間範圍。 要執行查詢，請使用左上角的**TimeRange**選擇器。 例如，在事件期間執行查詢或在更改之前查看狀態。  

![直接 VM 對應概觀](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>從虛擬機器比例集中查看地圖

要直接從虛擬機器規模集中訪問 VM 的 Azure 監視器，可以執行：

1. 在 Azure 門戶中，選擇**虛擬機器縮放集**。
2. 從清單中選擇 VM。 然後在 **"監視**"部分中，選擇 **"見解**"。  
3. 選取 [對應]**** 索引標籤。

地圖將比例集中的所有實例視覺化為組節點以及組的依賴項。 展開的節點列出比例集中的實例。 您可以一次滾動流覽這些實例 10。 

要載入特定實例的地圖，請先在地圖上選擇該實例。 然後選擇右側的**省略號**按鈕 （...），然後選擇 **"載入伺服器映射**"。 在顯示的地圖中，您將看到在指定時間範圍內具有活動網路連接的進程組和進程。 

根據預設，對應會顯示過去 30 分鐘。 如果要查看依賴項過去的樣子，可以查詢最多一小時的歷史時間範圍。 要執行查詢，請使用**時間範圍**選擇器。 例如，在事件期間執行查詢或在更改之前查看狀態。

![直接 VM 對應概觀](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>您還可以從虛擬機器縮放集的**實例**視圖訪問特定實例的映射。 在 **"設置"** 部分中，**轉到實例見解** > **Insights**。

## <a name="view-a-map-from-azure-monitor"></a>從 Azure 監視器查看地圖

在 Azure 監視器中，Map 功能提供 VM 及其依賴項的全域視圖。 要訪問 Azure 監視器中的地圖功能，可以執行以下服務：

1. 在 Azure 門戶中，選擇 **"監視器**"。 
2. 在 **"見解"** 部分中，選擇**虛擬機器**。
3. 選取 [對應]**** 索引標籤。

   ![多個 VM 的 Azure 監視器概覽映射](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

使用頁面頂部的**工作區**選擇器選擇工作區。 如果您有多個日誌分析工作區，請選擇使用解決方案啟用的工作區，該工作區的 VM 會向其報告。 

**組**選擇器返回與所選工作區相關的電腦的訂閱、資源組、[電腦群組](../../azure-monitor/platform/computer-groups.md)和虛擬機器縮放集。 您的選擇僅適用于"地圖"功能，不會延續到"性能"或"運行狀況"。

根據預設，對應會顯示過去 30 分鐘。 如果要查看依賴項過去的樣子，可以查詢最多一小時的歷史時間範圍。 要執行查詢，請使用**時間範圍**選擇器。 例如，在事件期間執行查詢或在更改之前查看狀態。  

## <a name="next-steps"></a>後續步驟

要識別瓶頸、檢查性能並瞭解 VM 的總體利用率，請參閱[查看 VM Azure 監視器的性能狀態](vminsights-performance.md)。 
