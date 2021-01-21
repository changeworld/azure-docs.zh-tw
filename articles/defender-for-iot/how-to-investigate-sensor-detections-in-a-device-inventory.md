---
title: 深入瞭解特定感應器探索到的裝置
description: 裝置清查會顯示感應器偵測到的廣泛裝置屬性範圍。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c868685ad943c1ab9ab263a164111e46294c042
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625426"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>調查裝置清查中的感應器偵測

裝置清查會顯示感應器偵測到的廣泛裝置屬性範圍。 您可以使用下列選項：

 - 輕鬆篩選資訊。

 - 將資訊匯出至 CSV 檔案。

 - 匯入 Windows 登錄詳細資料。

 - 建立要在裝置對應中顯示的群組。

## <a name="view-device-attributes-in-the-device-inventory"></a>查看裝置清查中的裝置屬性

下列屬性會出現在裝置清查資料表中。

| 參數 | 描述 |
|--|--|
| 名稱 | 當感應器發現裝置時，該裝置的名稱。 |
| 類型 | 裝置的類型。 |
| 廠商 | 裝置廠商的名稱，如 MAC 位址中所定義。 |
| 作業系統 | 裝置的作業系統。 |
| 韌體 | 裝置的固件。 |
| IP 位址 | 裝置的 IP 位址。 |
| VLAN | 裝置的 VLAN。 如需指示感應器探索 Vlan 的詳細資訊，請參閱 [定義 vlan 名稱](how-to-manage-the-on-premises-management-console.md#define-vlan-names)。 (how to--------------------------settings) 。 |
| MAC 位址 | 裝置的 MAC 位址。 |
| 通訊協定 | 裝置使用的通訊協定。 |
| 未確認的警示 | 與此裝置相關聯的未認可警示數目。 |
| 已獲授權 | 由使用者定義的授權狀態：<br />- **True**：裝置已獲得授權。<br />- **False**：裝置尚未獲得授權。 |
| 稱為掃描器 | 由使用者定義為掃描裝置。 |
| 是否為程式設計裝置 | 由使用者定義為授權的程式設計裝置。 <br />- **True**：裝置會針對 Plc、RTUs 和控制器執行程式設計活動，這些活動與工程工作站相關。 <br />- **False**：裝置不是程式設計裝置。 |
| 群組 | 此裝置參與的群組。 |
| 最後一個活動 | 裝置所執行的最後一個活動。 |
| Discovered | 在網路中第一次看到此裝置時。 |

若要查看裝置清查：

1. 在左窗格中，選取 [ **裝置**]。 [ **裝置** ] 窗格隨即在右側開啟。

2. 在 [ **裝置** ] 窗格中，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: 。

若要隱藏和顯示資料行，請自訂裝置清查表格：

1. 在裝置清查的右上方功能表上，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: 。

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="裝置清查設定畫面。":::

2. 在 [ **裝置清查設定** ] 視窗中，選取您要在 [裝置清查] 資料表中顯示的資料行。

3. 使用箭號來變更資料表中資料行的位置。

4. 選取 [儲存]。 [ **裝置清查設定** ] 視窗隨即關閉，而新的設定會出現在資料表中。

### <a name="create-temporary-device-inventory-filters"></a>建立暫存裝置清查篩選器

您可以設定篩選器，以定義資料表所顯示的資訊。 例如，您可以決定只想要查看 PLC 裝置的資訊。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="裝置學習。":::

當您離開清查時，不會儲存篩選。

### <a name="save-device-inventory-filters"></a>儲存裝置清查篩選

您可以儲存篩選或您所需的篩選器組合，並在裝置清查中重新套用它們。 根據特定裝置類型建立更廣泛的篩選，或根據特定的類型和特定的通訊協定來建立更窄的篩選。

您儲存的篩選也會另存為裝置對應群組。 這項功能可在地圖上查看網路裝置時，提供額外的細微性層級。

若要建立篩選：

1. 在您想要篩選的資料行中，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: 。

2. 在 [ **篩選器** ] 對話方塊中，選取篩選類型：

   - **Equals**：根據您要篩選資料行的確切值。 例如，如果您根據 **Equals** 和來篩選 [通訊協定] 資料行 `value=ICMP` ，此資料行將會顯示只使用 ICMP 通訊協定的裝置。

   - **包含**：在資料行中的其他值之間包含的值。 例如，如果您根據 [ **包含** ] 和來篩選 [通訊協定] 資料行 `value=ICMP` ，此資料行會顯示使用 ICMP 通訊協定的裝置，作為裝置使用的通訊協定清單的一部分。

3. 若要根據字母順序來組織資料行資訊，請選取 [] :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: 。 選取和箭號來排列 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: 順序 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 。

4. 若要儲存新的篩選，請定義篩選準則，然後選取 [ **另存** 新檔]。

5. 若要變更篩選定義，請變更定義，然後選取 [ **儲存變更**]。

若要查看篩選：

- 開啟左窗格並查看您已儲存的篩選：

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="從左側窗格中查看篩選。":::

### <a name="view-filtered-information-as-a-map-group"></a>將篩選的資訊視為地圖群組

當您切換至地圖視圖時，會反白顯示並篩選已篩選的裝置。 您所儲存的篩選群組會顯示在 [ **裝置清查篩選** ] 群組底下的側邊功能表中。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="在地圖視圖中查看篩選準則。":::

## <a name="learn-windows-registry-details"></a>瞭解 Windows 登錄詳細資料

除了學習 OT 裝置之外，您還可以探索 IT 裝置，包括 Microsoft Windows 工作站和伺服器。 這些裝置也會顯示在 [裝置清查] 中。 瞭解裝置之後，您可以使用詳細的 Windows 資訊擴充裝置清查，例如：

- 已安裝 Windows 版本

- 安裝的應用程式

- 修補程式層級資訊

- 開啟連接埠

- 更健全的作業系統版本資訊

有兩個選項可供您用來取得此資訊：

- 使用已排程 WMI 掃描的主動式輪詢。 

- 在裝置上散發和執行腳本來進行本機調查。 使用本機腳本會略過在端點上執行 WMI 輪詢的風險。 它也適用于具有瀑布和單向元素的管制網路。

本文說明如何使用腳本在本機調查 Windows endpoint registry。 此資訊將用來產生警示、通知、資料採礦報告、風險評定和攻擊向量報表。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="資料採礦螢幕擷取畫面。":::

您可以調查下列 Windows 作業系統：

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>開始之前

若要使用腳本，您必須符合下列需求：

- 需要有系統管理員許可權，才能在裝置上執行腳本。

- 感應器應已瞭解 Windows 裝置。 這表示如果裝置已經存在，腳本將會取出其資訊。

- 感應器正在監視 Windows 電腦所連接的網路。

### <a name="acquire-the-script"></a>取得腳本

若要接收腳本，請 [與客戶支援人員聯繫](mailto:support.microsoft.com)。

### <a name="deploy-the-script"></a>部署腳本

您可以部署腳本一次，或使用標準的自動化部署方法和工具來排程進行中的查詢。

### <a name="about-the-script"></a>關於腳本

- 腳本會以公用程式的形式執行，而不是安裝的程式。 執行腳本並不會影響端點。

- 腳本所產生的檔案會保留在本機磁片磁碟機上，直到您將其刪除為止。

- 腳本所產生的檔案會放在彼此的旁邊。 請勿分隔它們。

- 如果您在相同的位置再次執行腳本，則會覆寫這些檔案。

執行指令碼：  

1. 將腳本複製到本機磁片磁碟機，並將它解壓縮。 下列檔案會出現：

    - start.bat

    - settings.js開啟

    - 資料 bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="檔案總管中的檔案。":::

2. 執行檔案 `run.bat` 。

3. 探查登錄之後，會出現具有登錄資訊的 CX 快照集檔案。

4. 檔案名會指出快照集的系統名稱和日期和時間。 範例檔案名為 `CX-snaphot_SystemName_Month_Year_Time` 。

### <a name="import-device-details"></a>匯入裝置詳細資料

每個端點上所學到的資訊都應該匯入感應器。

從查詢產生的檔案可以放在一個可從感應器存取的資料夾中。 使用標準、自動化的方法和工具，將每個 Windows 端點的檔案移到您要匯入感應器的位置。

請勿更新檔案名。

若要匯入：

1. 選取 [匯 **入 Windows** 設定] 對話方塊中的 [匯 **入設定**]。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="匯入您的 Windows 設定。":::

2. 選取 [ **新增**]，然後選取 [所有檔案] (Ctrl + A) 。

3. 選取 [關閉]。 匯入裝置登錄資訊。 如果上傳其中一個檔案時發生問題，您將會收到檔案上傳失敗的通知。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="已新增檔案上傳成功。":::

## <a name="export-device-inventory-information"></a>匯出裝置清查資訊

您可以將裝置清查資訊匯出到 Excel 檔案。 匯入的資訊會覆寫目前的資訊。

匯出 CSV 檔案：

- 在裝置清查的右上方功能表上，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: 。 會產生並下載 CSV 報表。

## <a name="see-also"></a>另請參閱

[調查裝置清查中的所有企業感應器偵測](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[使用網站地圖視圖](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
