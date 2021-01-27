---
title: 建立資料採礦報告
description: 在各種層級（例如通訊協定、固件版本或程式設計命令）中，產生網路裝置的完整和詳細資訊。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/20/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 56751adedfc01b3927acc65f47910c3b1281f09c
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811619"
---
# <a name="sensor-data-mining-queries"></a>感應器資料採礦查詢

## <a name="about-sensor-data-mining-queries"></a>關於感應器資料採礦查詢

資料採礦工具可讓您在各種層級產生網路裝置的完整且細微的資訊。 例如，您可以根據下列程式建立查詢：

- 時間週期

- 連線到網際網路

- 連接埠

- 通訊協定

- 固件版本

- 程式設計命令

- 裝置非活動狀態

您可以根據篩選準則來微調報表。 例如，您可以查詢已更新固件的特定子網。

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="使用中裝置的清單。":::

您可以使用各種工具來管理查詢。 例如，您可以匯出和儲存。

> [!NOTE]
> 系統管理員和安全性分析師可以存取資料採礦選項。

### <a name="dynamic-updates"></a>動態更新

您建立的資料採礦查詢會在每次開啟時動態更新。 例如：

- 如果您在6月1日在裝置上建立適用于固件版本的報告，並在6月10日再次開啟報表，則會以6月10日正確的資訊更新此報表。

- 如果您建立報告來偵測6月1日過去30天內探索到的新裝置，並于6月30日開啟報表，則會顯示過去30天的結果。

### <a name="data-mining-use-cases"></a>資料採礦使用案例

您可以使用查詢來處理各種安全性小組的廣泛安全性需求：

- **SOC 事件回應**：即時產生報告，以協助處理立即事件回應。 例如，針對可能需要修補的裝置清單產生報告。

- **辯論**：根據調查報表的歷程記錄資料產生報表。

- **IT 網路完整性**：產生可協助改善整體網路安全性的報表。 例如，產生一份報告，其中列出具有弱式驗證認證的裝置。

- **可見度**：產生涵蓋所有查詢專案的報表，以查看您網路的所有基準參數。

## <a name="data-mining-storage"></a>資料採礦儲存體

除了刪除裝置時以外，會持續儲存和儲存資料採礦資訊。 您可以匯出資料採礦結果，並將其儲存在外部，並儲存在安全的伺服器上。 此外，感應器會執行自動每日備份，以確保系統持續性和資料保留。


## <a name="predefined-data-mining-queries"></a>預先定義的資料採礦查詢

提供下列預先定義的查詢。 這些查詢會即時產生。

- **Cve**：過去24小時內偵測到具有已知弱點的裝置清單。

- **排除的 cve**：以手動方式排除的所有 cve 清單。 若要在 VA 報告和攻擊媒介中達到更精確的結果，您可以藉由包含和排除 Cve 來手動自訂 CVE 清單。

- **網際網路活動**：連線至網際網路的裝置。

- 非使用中的 **裝置**：過去七天未進行通訊的裝置。

- 使用中 **裝置**：過去24小時內的作用中網路裝置。

- **遠端存取**：透過遠端會話通訊協定進行通訊的裝置。

- 程式 **設計命令**：傳送產業程式設計的裝置。

您可以從 [ **報表** ] 畫面自動存取這些報表，其中 RO 使用者和其他使用者可以加以查看。 RO 使用者無法存取資料採礦報表。

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="資料採礦畫面。":::

## <a name="create-a-data-mining-query"></a>建立資料採礦查詢

若要建立資料採礦報表：

1. 從側邊功能表選取 [ **資料採礦** ]。 預先定義的建議報表會自動出現。

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="從側邊窗格選取資料採礦。":::

2. 選取 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::。

3. 選取 [ **新增報表** ] 並定義報表。

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="填寫此畫面來建立新的報表。":::

   可用的參數如下：

   - 提供報表名稱和描述。

   - 針對 [類別]，請選取下列其中一項：

      - **類別 (所有)** ，以查看您網路中所有裝置的所有報告結果。

      - 選擇標準類別的 [**一般**]。

   - 選取您感興趣的特定報表參數。

   - 依) 選擇排序次序 (**順序** 。 根據活動或類別來排序結果。

   - 選取 [ **儲存至報表頁面** ]，將報表結果儲存為報表，以便從 **報表** 頁面存取。 這可讓 RO 使用者執行您所建立的報表。

   - 選取 [ **篩選] (新增)** 來新增更多篩選。 支援萬用字元要求。

   - 在裝置對應) 中指定裝置群組 (定義。

   - 指定 IP 位址。

   - 指定埠。

   - 指定 MAC 位址。

4. 選取 [儲存]。 在 [ **資料採礦** ] 頁面上開啟的報表結果。

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="在資料採礦頁面上顯示的報告結果。":::

### <a name="manage-data-mining-reports"></a>管理資料-挖掘報表

下表說明資料採礦的管理選項：

| 圖示影像 | 描述 |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | 編輯報表參數。 |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | 匯出為 PDF。 |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |匯出為 CSV。 |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | 顯示其他資訊，例如上次修改日期。 您可以使用此功能來建立查詢結果快照集。 例如，您可能需要這麼做，才能進一步調查小組領導人或 SOC 分析師。 |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | 顯示在 [ **報表] 頁面上** ，或在 [ **報表** ] 頁面上隱藏。 :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="隱藏或顯示您的報表。"::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | 刪除報表。 |

#### <a name="create-customized-directories"></a>建立自訂目錄 

您可以藉由建立類別目錄來組織資料採礦查詢的廣泛資訊。 例如，您可以建立通訊協定或位置的目錄。

若要建立新的目錄：

1. 選取 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: 以新增目錄。

2. 選取 [ **新增目錄** ] 以顯示新的目錄表單。

3. 命名新的目錄。

4. 將必要的報表拖曳至新的目錄。 您可以隨時將報表拖曳回主視圖。

5. 以滑鼠右鍵按一下新目錄以開啟、編輯或刪除它。

#### <a name="create-snapshots-of-report-results"></a>建立報表結果的快照集

您可能需要儲存某些查詢結果，以進行進一步的調查。 例如，您可能需要與不同的安全性小組共用結果。

快照集會儲存在報表結果中，而不會產生動態查詢。

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="快照。":::

若要建立快照集：

1. 開啟所需的報表。

2. 選取資訊圖示 :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: 。

3. 選取 [ **取得新** 的]。

4. 輸入快照集的名稱，然後選取 [ **儲存**]。

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="拍攝快照集。":::

#### <a name="customize-the-cve-list"></a>自訂 CVE 清單

您可以手動自訂 CVE 清單，如下所示：

  - 包含/排除 Cve

  - 變更 CVE 分數

若要在 CVE 報告中執行手動變更：

1.  從側邊功能表選取 [ **資料採礦**]。

2. 選取 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: [ **資料採礦** ] 視窗的左上角。 然後選取 [ **新增報表**]。

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="建立新的報表。":::

3. 從左窗格中，選取下列其中一個選項：

   - **已知弱點**：同時選取兩個選項，並在報表的兩個數據表中顯示結果，一個資料表具有 cve，另一個則包含排除的 cve。

   - **Cve**：選取此選項可顯示所有 cve 的清單。

   - **排除的 cve**：選取此選項可顯示所有排除的 cve 清單。

4. 填寫 **名稱** 和 **描述** 資訊，然後選取 [ **儲存**]。 新的報表就會出現在 [ **資料採礦** ] 視窗中。

5. 若要排除 Cve，請開啟 Cve 的資料採礦報表。 所有 Cve 的清單隨即出現。

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="C E V E 報表。":::

6. 若要啟用選取清單中的專案，請選取 :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: 並選取您要自訂的 cve。 **作業** 列會出現在底部。

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="資料採礦作業列的螢幕擷取畫面。":::

7. 選取您要排除的 Cve，然後選取 [ **刪除記錄**]。 您所選取的 Cve 不會出現在 Cve 清單中，而且會在您產生時出現在排除的 Cve 清單中。

8. 若要在 Cve 清單中包含排除的 Cve，請為排除的 Cve 產生報表，並從該清單中刪除您想要在 Cve 清單中包含的專案。

9. 選取您要在其中變更分數的 Cve，然後選取 [ **更新 CVE 分數**]。

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="更新 CVE 分數。":::

10. 輸入新的分數，然後選取 **[確定]**。 更新的分數會顯示在您選取的 Cve 中。



## <a name="sensor-reports-based-on-data-mining"></a>根據資料採礦的感應器報表

從 [ **報表** ] 選項存取的一般報表是預先定義的資料採礦報表。 它們不是可在資料採礦中使用的動態查詢，而是資料採礦查詢結果的靜態標記法。

唯讀使用者無法使用資料採礦查詢結果。 如果系統管理員和安全性分析師想要唯讀使用者存取資料採礦查詢所產生的資訊，則應該將資訊儲存為報告。

報表會反映資料採礦查詢結果所產生的資訊。 這包括可在 [報表] 視圖中使用的預設資料採礦報表。 系統管理員和安全性分析師也可以產生自訂資料採礦查詢，並將其儲存為報表。 這些報告也可供 RO 使用者使用。

產生報告：

1. 選取側邊功能表上的 [ **報表** ]。

2. 選擇要顯示的必要報表。 選項可以是 **自訂** 或 **自動產生** 的報表，例如程式 **設計命令** 和 **遠端存取**。

3. 您可以從畫面右上角選取其中一個圖示來匯出報表：

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: 匯出至 PDF 檔案。

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: 匯出至 CSV 檔案。

> [!NOTE] 
> RO 使用者只能看到針對它們建立的報表。

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="選取要產生的報表。":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="已產生遠端存取報告。":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>以資料採礦報告為基礎的內部部署管理主控台報告

內部部署管理主控台可讓您針對已連線的每個感應器產生報告。 報表是以執行的感應器資料採礦查詢為基礎。

您可以產生下列報表：

- 使用中 **裝置 (過去24小時)**：顯示在24小時內顯示網路活動的裝置清單。

- **非使用中的裝置 (過去7天)**：顯示過去七天內沒有任何網路活動的裝置清單。

- 程式 **設計命令**：顯示過去24小時內傳送程式設計命令的裝置清單。

- **遠端存取**：顯示過去24小時記憶體取遠端來源的裝置清單。

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="報表檢視的螢幕擷取畫面。":::

當您從內部部署管理主控台選擇感應器時，該感應器上設定的所有自訂報表都會出現在報表清單中。 您可以針對每個感應器產生預設報表或在該感應器上設定的自訂報表。

產生報告：

1. 在左窗格中，選取 [ **報表**]。 [ **報表** ] 視窗隨即出現。

2. 從 [ **感應器** ] 下拉式清單中，選取要產生報告的感應器。

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="感應器視圖的螢幕擷取畫面。":::

3. 從右邊的下拉式清單中，選取您要產生的報告。

4. 若要建立報表結果的 PDF，請選取 [] :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: 。
