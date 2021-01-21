---
title: 瞭解所有企業感應器探索到的裝置
description: 使用內部部署管理主控台中的裝置清查，從連線的感應器取得裝置資訊的完整觀點。 使用匯入、匯出和篩選工具來管理此資訊。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9da5c8c89ee124e527584164b21b096ac815e5ca
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625459"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>調查裝置清查中的所有企業感應器偵測

您可以使用內部部署管理主控台中的 *裝置清查* ，從連線的感應器中查看裝置資訊。 這項功能可讓您全面瞭解所有的網路資訊。 使用匯入、匯出和篩選工具來管理此資訊。 連接的感應器版本的狀態資訊也會出現。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="裝置清查資料表的螢幕擷取畫面。":::

下表說明裝置清查中的資料表資料行。

| 參數 | 描述 |
|--|--|
| **未確認的警示** | 與此裝置相關聯的未處理警示數目。 |
| **業務單位** | 包含此裝置的業務單位。 |
| **區域** | 包含此裝置的區域。 |
| **站台** | 包含此裝置的網站。 |
| **區域** | 包含此裝置的區域。 |
| **設備** | 適用于 IoT 的 Azure Defender 感應器可保護此裝置。 |
| **名稱** | 此裝置的名稱，作為 IoT 的 Defender 探索到它。 |
| **型別** | 裝置的類型，例如 PLC 或 HMI。 |
| **廠商** | 裝置廠商的名稱，如 MAC 位址中所定義。 |
| **作業系統** | 裝置的作業系統。 |
| **韌體** | 裝置的固件。 |
| **IP 位址** | 裝置的 IP 位址。 |
| **VLAN** | 裝置的 VLAN。 |
| **MAC 位址** | 裝置的 MAC 位址。 |
| **通訊協定** | 裝置使用的通訊協定。 |
| **未確認的警示** | 與此裝置相關聯的未處理警示數目。 |
| **已獲授權** | 裝置的授權狀態：<br />- **True**：裝置已獲得授權。<br />- **False**：裝置尚未獲得授權。 |
| **稱為掃描器** | 此裝置是否會在網路中執行類似掃描的活動。 |
| **是否為程式設計裝置** | 這是否為程式設計裝置：<br />- **True**：裝置會針對 Plc、RTUs 和控制器執行程式設計活動，這些活動與工程工作站相關。<br />- **False**：裝置不是程式設計裝置。 |
| **群組** | 此裝置參與的群組。 |
| **最後一個活動** | 裝置所執行的最後一個活動。 |
| **Discovered** | 在網路中第一次看到此裝置時。 |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>將資料整合到企業裝置清查

資料整合功能可讓您使用其他企業資源的資訊，增強裝置清查中的資料。 這些來源包括 Cmdb、DNS、防火牆和 Web Api。

您可以使用此資訊來學習。 例如：

- 裝置採購日期和期限結束日期

- 負責每部裝置的使用者

- 裝置的開啟票證

- 上次升級固件的日期

- 允許存取網際網路的裝置

- 執行主動式防毒軟體應用程式的裝置

- 已登入裝置的使用者

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="裝置清查畫面上的資料表。":::

您可以透過下列任一方式來整合資料：

- 手動新增

- 執行 Defender for IoT 提供的自訂腳本

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="企業資料整合器的圖表。":::

您可以使用 Defender for IoT 技術支援來設定您的系統，以接收 Web API 查詢。

若要手動新增資料：

1. 在側邊功能表上，選取 [ **裝置清查** ]，然後選取 [] :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="編輯裝置的清查設定。":::

2. 在 [ **裝置清查設定** ] 對話方塊中，選取 [ **加入自訂資料行**]。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="將自訂資料行新增至您的清查。":::

3. 在 [ **加入自訂資料行** ] 對話方塊中，加入新的資料行名稱 (最多250個字元的 UTF) ，選取 [ **手動**]，然後選取 [ **儲存**]。 新的專案會出現在 [ **裝置清查設定** ] 對話方塊中。

4. 在 [ **裝置清查** ] 視窗的右上角，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: 並選取 [ **匯出所有裝置清查**]。 會產生 CSV 檔案。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="匯出的 CSV 檔案。":::

5. 手動將資訊新增至新的資料行，然後儲存檔案。

6. 在 [ **裝置清查** ] 視窗的右上角，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: ，選取 [匯 **入手動輸入資料行**]，然後流覽至 CSV 檔案。 新的資料會出現在 **裝置清查** 資料表中。

若要整合其他企業實體的資料：

1. 在 [ **裝置清查** ] 視窗的右上角，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: 並選取 [ **匯出所有裝置清查**]。

2. 在 [ **裝置清查設定** ] 對話方塊中，選取 [ **加入自訂資料行**]。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="將自訂資料行新增至您的清查。":::

3. 在 [ **加入自訂資料行** ] 對話方塊中，加入新的資料行名稱 (最多250個字元的 UTF) ，然後選取 [ **自動**]。 [ **上傳腳本** ] 和 [ **測試腳本** ] 選項隨即出現。

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="自動新增自訂資料行。":::

4. 上傳並測試您從 [Microsoft 支援服務](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)收到的腳本。

## <a name="retrieve-information-from-the-device-inventory"></a>從裝置清查取出資訊

您可以取得受管理的感應器偵測到的廣泛裝置資訊範圍，並將該資訊與合作夥伴系統整合。 例如，您可以取出感應器、區域、網站識別碼、IP 位址、MAC 位址、固件、通訊協定和廠商資訊。 您根據下列內容取得的篩選資訊：

- 授權和未經授權的裝置。

- 與特定網站相關聯的裝置。

- 與特定區域相關聯的裝置。

- 與特定感應器相關聯的裝置。

使用 Defender for IoT API 命令來取得和整合此資訊。 如需詳細資訊，請參閱 [Defender For IOT API 感應器和管理主控台 api](references-work-with-defender-for-iot-apis.md)。

## <a name="filter-the-device-inventory"></a>篩選裝置清查

您可以篩選裝置清查以顯示感興趣的資料行。 例如，您可以查看 PLC 裝置資訊。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="裝置清查的螢幕擷取畫面。":::

當您離開視窗時，就會清除篩選。

若要多次使用相同的篩選準則，您可以儲存篩選或您所需的篩選器組合。 您可以開啟左窗格，並查看您已儲存的篩選器：

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="裝置清查畫面。":::

若要篩選裝置清查：

1. 在您想要篩選的資料行中，選取 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: 。

2. 在 [ **篩選器** ] 對話方塊中，選取篩選類型：

   - **Equals**：根據您要篩選資料行的確切值。 例如，如果您根據 **Equals** 和來篩選 [通訊協定] 資料行 `value=ICMP` ，此資料行將會顯示只使用 ICMP 通訊協定的裝置。

   - **包含**：在資料行中的其他值之間包含的值。 例如，如果您根據 [ **包含** ] 和來篩選 [通訊協定] 資料行 `value=ICMP` ，此資料行會顯示使用 ICMP 通訊協定的裝置，作為裝置使用的通訊協定清單的一部分。

3. 若要根據字母順序來組織資料行資訊，請選取 [] :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: 。 選取和箭號來排列 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: 順序 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 。

4. 若要儲存新的篩選，請定義篩選準則，然後選取 [ **另存** 新檔]。

5. 若要變更篩選定義，請變更定義，然後選取 [ **儲存變更**]。

## <a name="view-device-information-per-zone"></a>查看每個區域的裝置資訊

您可以瞭解有關區域中裝置的下列資訊。

### <a name="view-a-device-map"></a>查看裝置對應

若要查看所選區域的感應器裝置對應：

- 在 [ **網站管理** ] 視窗中，從包含區功能變數名稱稱的列選取 [ **View Zone Map** ]。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="預設營業單位的預設區域。":::

[ **裝置對應** ] 視窗隨即出現。 它會顯示與所選區域相關的所有網路元素，包括感應器、與其連接的裝置，以及其他資訊。

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="區域地圖的螢幕擷取畫面。":::

下列工具可讓您從地圖中查看裝置和裝置資訊。 如需這些功能的詳細資訊，請參閱 *適用于 IoT 的 Defender 平臺使用者指南*。

- **地圖縮放視圖**：簡化的視圖、連接視圖和詳細的觀點。 顯示的地圖視圖會依地圖的縮放層級而有所不同。 您可以藉由調整縮放層級，在地圖視圖之間切換。

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **地圖搜尋和版面組態工具**：用來顯示不同網路區段、裝置、裝置群組或圖層的工具。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="搜尋和版面組態工具視圖的螢幕擷取畫面。":::

- **裝置上的標籤和指標：** 例如，在 IT 網路的子網中分組的裝置數目。 在此範例中為8。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="標籤和指標的螢幕擷取畫面。":::

- **查看裝置屬性**：例如，監視裝置的感應器和基本裝置屬性。 以滑鼠右鍵按一下裝置以查看裝置屬性。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="裝置屬性視圖的螢幕擷取畫面。":::

- **與裝置相關聯的警示：** 以滑鼠右鍵按一下裝置以查看相關的警示。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="顯示警示視圖的螢幕擷取畫面。":::

### <a name="view-alerts-associated-with-a-zone"></a>查看與區域相關聯的警示

若要查看與特定區域相關聯的警示：

- 選取 [ **區域** ] 視窗中的 [警示] 圖示。 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="具有範例的預設商務單位視圖。":::

如需詳細資訊，請參閱 [總覽：使用警示](how-to-work-with-alerts-on-premises-management-console.md)。

### <a name="view-the-device-inventory-of-a-zone"></a>查看區域的裝置清查

若要查看與特定區域相關聯的裝置清查：

- 從 [**區域**] 視窗中選取 [**查看裝置清查**]。

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="裝置清查畫面隨即出現。":::

如需詳細資訊，請參閱 [調查裝置清查中的所有企業感應器](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)偵測。

### <a name="view-additional-zone-information"></a>查看其他區域資訊

以下是可用的其他區域資訊：

- **區域詳細資料**：查看與區域相關聯的裝置、警示和感應器數目。

- **感應器詳細資料**：查看指派給區域之每個感應器的名稱、IP 位址和版本。

- **線上狀態**：如果感應器已中斷連線，請從感應器進行連接。 請參閱將 [感應器連線至內部部署管理主控台](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)。 

- **更新進度**：如果已連線的感應器正在升級，則會出現升級狀態。 在升級期間，內部部署管理主控台不會接收來自感應器的裝置資訊。

## <a name="see-also"></a>另請參閱

[調查裝置清查中的感應器偵測](how-to-investigate-sensor-detections-in-a-device-inventory.md)
