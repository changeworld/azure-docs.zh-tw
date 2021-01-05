---
title: 在內部部署管理主控台上使用警示
description: 使用內部部署管理主控台來取得網路中最近威脅的企業觀點，並進一步瞭解感應器使用者處理這些威脅的方式。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: f52e308199ddb8bc21809009ad71918d077ac5b2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839094"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>在內部部署管理主控台上使用警示 

您可以從管理主控台的 [ **警示** ] 視窗中執行下列動作：

- 使用警示篩選

- 使用警示計數器

- 查看警示資訊

- 管理警示事件

- 建立警示排除規則

- 從外部系統觸發警示排除規則

- 使用警示群組加速事件工作流程

## <a name="view-alerts-in-the-on-premises-management-console"></a>在內部部署管理主控台中查看警示

內部部署管理主控台會匯總來自所有已連線感應器的警示。 這可讓您深入瞭解網路中最近的威脅，並協助您進一步瞭解感應器使用者處理這些威脅的方式。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="警示視窗的螢幕擷取畫面。":::

### <a name="work-with-alert-filters"></a>使用警示篩選

[ **警示** ] 視窗會顯示連線到內部部署管理主控台的感應器所產生的警示。 您可以查看已連線感應器的所有警示，或提出從特定的傳送警示：

- 網站

- 區域

- 裝置

- Sensor

選取 [ **清除篩選** ] 以查看所有警示。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="選取 [清除篩選] 按鈕，以清除您的篩選。":::

### <a name="work-with-alert-counters"></a>使用警示計數器

警示計數器會依嚴重性和已認可狀態提供警示的細目。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="警示計數器會顯示您有多少警示。":::

警示計數器中會出現下列嚴重性層級：

- **重大**：表示應立即處理的惡意攻擊。

- **主要**：表示安全性威脅，這是很重要的解決。

- **次要**：表示來自可能包含安全性威脅之基準行為的一些偏差。

- **警告**：指出來自基準行為但沒有安全性威脅的部分偏差。

嚴重性層級是預先定義的。

您可以調整計數器，以根據已認可和未確認的警示來提供數位。 已在 Defender 針對 IoT 感應器觸發未認可的警示，但尚未在感應器上檢查操作員。

選取 [ **顯示認可的警示** ] 選項時，所有認可的警示都會出現在 [ **警示** ] 視窗中。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="查看已認可的警示。":::

### <a name="view-alert-information"></a>查看警示資訊

警示會顯示下列資訊：

- 警示事件的摘要。

- 警示嚴重性。

- 感應器中偵測到警示的連結。

- 警示 UUID。 UUID 包含與感應器上偵測到的警示事件相關聯的警示識別碼，並以連字號分隔，然後再接著唯一的系統識別碼。

**內部部署管理主控台警示 UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="裝置已連線但未獲授權。":::

**感應器警示識別碼**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="感應器警示識別碼。":::

使用 Uuid 可確保在內部部署管理主控台中顯示的每個警示都可供搜尋，並可由唯一的數位識別。 這是必要的，因為從多個感應器產生的警示可能會產生相同的警示識別碼。

> [!NOTE]
> 根據預設，在定義轉送規則時，會在下列夥伴系統中顯示 Uuid： ArcSight、syslog 伺服器、QRadar、Sentinel 和 NetWitness。 不需要進行任何設定。

若要查看警示資訊：

- 從警示清單中選取警示。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="警示資訊的螢幕擷取畫面。":::

若要在感應器中查看警示：

- 從警示中選取 [ **開啟感應器** ]。

若要在區域對應中查看裝置：

- 若要查看裝置對應，並將焦點放在警示裝置和所有連線的裝置，請選取 [ **顯示裝置**]。

## <a name="manage-alert-events"></a>管理警示事件

您可以管理組織感應器偵測到的警示事件，如下所示：

- 學習或認可警示事件。 選取 [ **學習 & 認可** ] 以瞭解所有可授權的警示事件，以及確認目前未認可的所有警示事件。

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="選取 [學習 & 認可] 以瞭解全部。":::

- 靜音和取消靜音警示事件。

## <a name="create-alert-exclusion-rules"></a>建立警示排除規則

指示 Defender 讓 IoT 根據下列內容略過警示觸發程式：

- 時區和時間週期

- 裝置位址 (IP、MAC、子網) 

- 警示名稱

- 特定感應器

當您希望 Defender for IoT 忽略將觸發警示的活動時，請建立警示排除規則。

例如，如果您知道特定感應器監視的所有裝置都會透過維護程式兩天進行，您可以定義排除規則，指示 Defender 在預先定義的期間內，將此感應器偵測到的警示抑制。

### <a name="alert-exclusion-logic"></a>警示排除邏輯

警示規則邏輯為 `AND` 基礎。 這表示只有在符合所有規則條件時，才會觸發警示。

如果未定義規則條件，條件將會包含所有選項。 例如，如果您未在規則中包含感應器名稱，則會套用至所有感應器。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="[建立排除規則] 視圖的螢幕擷取畫面。":::

規則摘要會顯示在 [ **排除規則** ] 視窗中。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="排除規則摘要視圖的螢幕擷取畫面。":::

除了使用排除規則之外，您還可以藉由將警示靜音來隱藏警示。

### <a name="create-exclusion-rules"></a>建立排除規則

若要建立排除規則：

1. 從內部部署管理主控台的左窗格中，選取 [ **警示排除**]。 選取視窗右上角的 [新增] **圖示，** 以定義新的排除規則 :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: 。 [ **建立排除規則** ] 對話方塊隨即開啟。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="在這裡填入資訊來建立警示排除。":::

2. 在 [ **名稱** ] 欄位中輸入規則名稱。 名稱不能包含引號 (`"`) 。

3. 在 [ **依時區/期間** ] 區段中，輸入特定時區內的時間週期。 當針對某個時區的特定時段建立排除規則時，請使用這項功能，但應在其他時區同時執行。 例如，您可能需要在 8:00 AM 和 10:00 AM 的三個不同時區之間套用排除規則。 在此情況下，請建立三個使用相同時間週期和相關時區的不同排除規則。

4. 選取 [新增]。 在排除期間內，未在連線的感應器上建立警示。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="依時間週期視圖的螢幕擷取畫面。":::

5. 在 [ **依裝置位址** ] 區段中，定義：

  - 您要排除的裝置 IP 位址、MAC 位址或子網位址。

  - 已排除裝置、來源和目的地的流量方向。

6. 選取 [新增]。

7. 在 [ **依警示標題** ] 區段中，開始輸入警示標題。 從下拉式清單中，選取要排除的警示標題或標題。

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="依警示標題視圖顯示的螢幕擷取畫面。":::

8. 選取 [新增]。

9. 在 [ **依感應器名稱** ] 區段中，開始輸入感應器名稱。 從下拉式清單中，選取您要排除的感應器或感應器。

10. 選取 [新增]。

11. 選取 [儲存]  。 新的規則會出現在規則清單中。

您可以藉由靜音或建立警示排除規則來隱藏警示。 本節說明這兩項功能的可能使用案例。

- **排除規則**。 寫入排除規則的時機：

  - 您事先知道要從資料庫中排除事件。 例如，您知道在特定感應器偵測到的案例將會觸發不相關的警示。 例如，您將對特定網站上的組織 Plc 執行維護工作，並想要隱藏與此網站 Plc 相關的警示。

  - 您希望 Defender for IoT 略過特定時間範圍內的事件， () 的系統維護工作。

  - 您想要忽略特定子網中的事件。

  - 您想要使用一個規則來控制從數個感應器產生的警示事件。

  - 您不想要在事件記錄檔中追蹤警示排除事件。

- **靜音**。 在下列情況時將警示靜音：

  - 不會規劃需要靜音的專案。 您事先不知道什麼是不相關的事件。

  - 您想要從 [ **警示** ] 視窗隱藏警示，但仍想要在事件記錄檔中追蹤。

  - 您想要忽略特定通道上的事件。

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>從外部系統觸發警示排除規則

從外部系統觸發警示排除規則。 例如，管理來自企業票證系統的排除規則，或管理網路維護程式的系統。

定義要套用規則的感應器、引擎、開始時間和結束時間。 如需詳細資訊，請參閱 [Defender For IOT API 感應器和管理主控台 api](references-work-with-defender-for-iot-apis.md)。

您使用 API 建立的規則會在 [ **排除規則** ] 視窗中顯示為 RO。

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="[編輯排除規則] 視圖的螢幕擷取畫面。":::

## <a name="see-also"></a>請參閱

[在感應器上使用警示](how-to-work-with-alerts-on-your-sensor.md)
