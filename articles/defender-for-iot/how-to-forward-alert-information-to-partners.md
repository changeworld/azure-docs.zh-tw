---
title: 轉寄警示資訊
description: 您可以使用轉送規則，將警示資訊傳送給夥伴系統。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0b71f7ca3f812de1514612f8b0dd5915f3f81bc4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839378"
---
# <a name="forward-alert-information"></a>轉寄警示資訊

您可以將警示資訊傳送給與適用于 IoT 的 Azure Defender、syslog 伺服器、電子郵件地址等整合的合作夥伴。 使用轉送規則可讓您快速將警示資訊傳遞給安全性專案關係人。  

Syslog 和其他預設轉送動作會隨著您的系統一起傳遞。 當您與夥伴廠商（例如 Microsoft Azure Sentinel、ServiceNow 或 Splunk）整合時，更多的轉送動作可能會變成可用。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="警示資訊。":::

Defender for IoT 系統管理員有權使用轉送規則。

## <a name="about-forwarded-alert-information"></a>關於轉送的警示資訊

警示提供廣泛的安全性和操作事件的相關資訊。 例如：

  - 警示的日期和時間

  - 偵測到事件的引擎

  - 警示標題和描述性訊息

  - 警示嚴重性

  - 來源和目的地名稱和 IP 位址

  - 偵測到可疑的流量

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="偵測到位址掃描。":::

建立轉送規則時，會將相關資訊傳送給夥伴系統。

## <a name="create-forwarding-rules"></a>建立轉送規則

若要建立新的轉送規則：

1. 選取側邊功能表上的 [ **轉送** ]。

   ：： image type = "content" source = "媒體/使用方法-使用-警示-感應器/create-forwarding-rule-screen.png" alt-text = "建立轉送規則圖示。"：：：

2. 選取 [ **建立轉送規則**]。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="建立新的轉送規則。":::

3. 輸入轉送規則的名稱。

### <a name="forwarding-rule-criteria"></a>轉送規則準則 

定義觸發轉送規則所依據的準則。 使用轉送規則準則有助於找出並管理從感應器傳送到外部系統的資訊量。 可用選項如下：

通訊 **協定**：只有在偵測到的流量是在特定的通訊協定上執行時，才會觸發轉送規則。 從下拉式清單中選取所需的通訊協定，或選擇 [全部]。

**引擎**：選取所需的引擎或選擇 [全部]。 將會傳送所選引擎的警示。

**嚴重性層** 級：這是在嚴重性層級方面轉寄的最小事件。 例如，如果您選取 [ **次要**]、[次要警示]，以及高於此嚴重性等級的所有警示，將會轉送。 層級是預先定義的。

### <a name="forwarding-rule-actions"></a>轉送規則動作

轉送規則動作會指示感應器將警示資訊轉寄給夥伴廠商或伺服器。 您可以為每個轉送規則建立多個動作。

除了系統提供的轉送動作之外，當您與夥伴廠商整合時，其他動作可能會變成可用。 

#### <a name="email-address-action"></a>電子郵件地址動作

傳送包含警示資訊的電子郵件。 您可以為每個規則輸入一個電子郵件地址。

若要定義轉送規則的電子郵件：

1. 輸入單一電子郵件地址。 如果需要傳送多個郵件，請建立另一個動作。

2. 在 SIEM 上輸入警示偵測之時間戳記的時區。

3. 選取 [提交]  。

#### <a name="syslog-server-actions"></a>Syslog 伺服器動作

下列為支援的格式：

- 簡訊

- CEF 訊息

- LEEF 訊息

- 物件訊息

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="建立轉送規則動作。":::

輸入下列參數：

- Syslog 主機名稱和埠。

- 通訊協定 TCP 和 UDP。

- SIEM 上的警示偵測時間戳記的時區。

- CEF 伺服器的 TLS 加密憑證檔案和金鑰檔 (選擇性) 。
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="設定您的轉送規則加密。":::

| Syslog 文字訊息輸出欄位 | 描述 |
|--|--|
| 日期和時間 | Syslog 伺服器電腦收到資訊的日期和時間。 |
| 優先順序 | 使用者。警示 |
| Hostname (主機名稱) | 感應器 IP 位址 |
| 通訊協定 | TCP 或 UDP |
| 訊息 | 感應器：感應器名稱。<br /> 警示：警示的標題。<br /> 類型：警示的類型。 可能是 **通訊協定違規**、 **原則違規**、 **惡意** 代碼、 **異常** 或 **操作**。<br /> 嚴重性：警示的嚴重性。 可以是 **警告**、 **次要**、 **主要** 或 **嚴重**。<br /> 來源：來源裝置名稱。<br /> 來源 IP：來源裝置 IP 位址。<br /> 目的地：目的地裝置名稱。<br /> 目的地 IP：目的地裝置的 IP 位址。<br /> Message：警示的訊息。<br /> 警示群組：與警示相關聯的警示群組。 |


| Syslog 物件輸出 | 描述 |
|--|--|
| 日期及時間 |   Syslog 伺服器電腦收到資訊的日期和時間。 |  
| 優先順序 |    使用者。警示 | 
| Hostname (主機名稱) |    感應器 IP | 
| 訊息 | 感應器名稱：設備的名稱。 <br /> 警示時間：偵測到警示的時間：可能會因 syslog 伺服器電腦的時間而異，並取決於轉送規則的時區設定。 <br /> 警示標題：警示的標題。 <br /> 警示訊息：警示的訊息。 <br /> 警示嚴重性：警示的嚴重性：「 **警告**」、「 **次要**」、「 **主要**」或「 **重大**」。 <br /> 警示類型： **通訊協定違規**、 **原則違規**、 **惡意** 代碼、 **異常** 或 **操作**。 <br /> 通訊協定：警示的通訊協定。  <br /> **Source_MAC**：來源裝置的 IP 位址、名稱、廠商或 OS。 <br /> Destination_MAC：目的地的 IP 位址、名稱、廠商或 OS。 如果遺失資料，則值將為 **N/A**。 <br /> alert_group：與警示相關聯的警示群組。 |


| Syslog CEF 輸出格式 | 描述 |
|--|--|
| 日期和時間 | Syslog 伺服器電腦收到資訊的日期和時間。 |
| 優先順序 | 使用者。警示 | 
| Hostname (主機名稱) | 感應器 IP 位址 |
| 訊息 | CEF：0 <br />適用於 IoT 的 Azure Defender <br />感應器名稱：感應器設備的名稱。 <br />感應器版本 <br />警示標題：警示的標題。 <br />msg：警示的訊息。 <br />通訊協定：警示的通訊協定。 <br />嚴重性：「 **警告**」、「 **次要**」、「 **主要**」或「 **重大**」。 <br />類型： **通訊協定違規**、 **原則違規**、 **惡意** 代碼、 **異常** 或 **操作**。 <br /> 開始：偵測到警示的時間。 <br />可能會因 syslog 伺服器電腦的時間而異，並取決於轉送規則的時區設定。 <br />src_ip：來源裝置的 IP 位址。  <br />dst_ip：目的地裝置的 IP 位址。<br />貓：與警示相關聯的警示群組。  |

| Syslog LEEF 輸出格式 | 描述 |
|--|--|
| 日期和時間 |   Syslog 伺服器電腦收到資訊的日期和時間。 |  
| 優先順序 |    使用者。警示 | 
| Hostname (主機名稱) |    感應器 IP |
| 訊息 | 感應器名稱： Azure Defender for IoT 設備的名稱。 <br />LEEF：1。0 <br />適用於 IoT 的 Azure Defender <br />Sensor  <br />感應器版本 <br />適用于 IoT 的 Azure Defender 警示 <br />標題：警示的標題。 <br />msg：警示的訊息。 <br />通訊協定：警示的通訊協定。<br />嚴重性：「 **警告**」、「 **次要**」、「 **主要**」或「 **重大**」。 <br />類型：警示的類型： **通訊協定違規**、 **原則違規**、 **惡意** 代碼、 **異常** 或 **操作**。 <br />啟動：警示的時間。請注意，它可能會與 syslog 伺服器電腦的時間不同。  (這取決於時區設定。 )  <br />src_ip：來源裝置的 IP 位址。<br />dst_ip：目的地裝置的 IP 位址。 <br />貓：與警示相關聯的警示群組。 |

輸入所有資訊之後，請選取 [ **提交**]。

#### <a name="netwitness-action"></a>NetWitness 動作

將警示資訊傳送至 NetWitness 伺服器。

若要定義 NetWitness 轉送參數：

1. 輸入 NetWitness **主機名稱** 和 **埠** 資訊。

2. 在 SIEM 上輸入警示偵測之時間戳記的時區。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="將時區新增至轉送規則。":::

3. 選取 [提交]  。

#### <a name="integrated-vendor-actions"></a>整合式廠商動作

您可能已整合系統與安全性、裝置管理或其他產業廠商。 這些整合可讓您：

  - 傳送警示資訊。

  - 傳送裝置清查資訊。

  - 與廠商端防火牆通訊。

整合有助於橋接先前孤立的安全性解決方案、增強裝置可見度，以及加速整個系統的回應，以更快速地降低風險。

您可以使用 [動作] 區段來輸入認證，以及與整合式廠商進行通訊所需的其他資訊。

如需有關為整合設定轉送規則的詳細資訊，請參閱相關的夥伴整合文章。

### <a name="test-forwarding-rules"></a>測試轉送規則

測試在轉送規則中所定義的感應器與夥伴伺服器之間的連接：

1. 從 [ **轉送規則** ] 對話方塊中選取規則。

2. 選取 [ **更多** ] 方塊。

3. 選取 [ **傳送測試訊息**]。

4. 移至您的夥伴系統，確認已收到感應器傳送的資訊。

### <a name="edit-and-delete-forwarding-rules"></a>編輯和刪除轉送規則 

若要編輯轉送規則：

- 在 [**轉送規則**] 畫面上，選取 [**更多**] 下拉式功能表下的 [**編輯**]。 進行所需的變更，然後選取 [ **提交**]。

若要移除轉送規則：

- 在 [**轉送規則**] 畫面上，選取 [**更多**] 下拉式功能表下的 [**移除**]。 在 [ **警告** ] 對話方塊中，選取 **[確定]**。

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>轉送規則和警示排除規則

系統管理員可能已定義警示排除規則。 這些規則會指示感應器忽略以各種參數為基礎的警示事件，以協助系統管理員更細微地控制警示觸發。 這些參數可能包括裝置位址、警示名稱或特定的感應器。

這表示，您定義的轉送規則可能會根據您的系統管理員所建立的排除規則而予以忽略。 排除規則是在內部部署管理主控台中定義。

## <a name="see-also"></a>請參閱

[加速警示工作流程](how-to-accelerate-alert-incident-response.md)
