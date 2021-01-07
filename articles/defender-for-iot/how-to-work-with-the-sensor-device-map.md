---
title: 使用感應器裝置對應
description: 裝置對應提供偵測到的網路裝置的圖形標記法。 使用對應來分析和管理裝置資訊、網路配量和產生報告。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: fd1721060bdc4b18f324a94f7c367bacde6ed4e8
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976753"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>調查裝置對應中的感應器偵測

裝置對應提供偵測到的網路裝置的圖形標記法。 使用對應來：

  - 取得、分析及管理裝置資訊。

  - 分析網路配量，例如特定的感興趣群組或 Purdue 圖層。

  - 產生報告，例如匯出裝置詳細資料和摘要。

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="裝置對應的螢幕擷取畫面。":::

若要存取對應：

  - 從主控台的主畫面選取 [ **裝置對應** ]。

## <a name="map-search-and-layout-tools"></a>地圖搜尋和版面組態工具

下列工具是用來在地圖中工作。

您的使用者角色會決定哪些工具可在 [裝置對應] 視窗中使用。 如需使用者角色的詳細資訊，請參閱 [建立及管理使用者](how-to-create-and-manage-users.md) 。

| 符號 | 描述 |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| 依 IP 位址或 MAC 位址搜尋特定裝置。 在文字方塊中，輸入 IP 位址或 MAC 位址。 地圖會顯示您在與裝置連線的裝置上搜尋的裝置。 |
| 群組醒目提示和篩選 <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="群組反白顯示和篩選的螢幕擷取畫面。"::: | 根據預設和自訂裝置群組篩選或反白顯示地圖。 |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | 它會折迭顯示，以在裝置上啟用焦點視圖，並將 IT 裝置群組在一起。  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | 在地圖中維護目前的裝置相片順序。 例如，如果您將裝置拖曳至地圖上的新位置，則當您離開地圖時，裝置將會保留在這些位置中。 |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | 全螢幕 |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -查看為此裝置識別的 Purdue 層，包括自動、流程式控制制、監督和企業 <br /> -查看裝置之間的連線。|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | 在廣播與多播之間顯示或隱藏。 |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | 根據上一次與其他裝置通訊的時間來篩選地圖上的裝置。 |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="通知" border="false"::: | 查看裝置的通知。 例如，如果使用現有 MAC 位址偵測到裝置的新 IP |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="匯出" border="false"::: | 匯出/匯入裝置資訊。 |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | 查看所選裝置的基本裝置屬性。 |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="放大" border="false"::: 或 :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="縮小" border="false"::: | 放大或縮小地圖中的裝置。 |

## <a name="view-ot-elements-only"></a>僅查看 OT 元素

根據預設，系統會自動依子網匯總 IT 裝置，讓地圖視圖專注于 OT 和 ICS 網路。 IT 網路元素的呈現會折迭為最小值，以減少地圖上呈現的裝置總數，並清楚說明和 ICS 網路元素。

每個子網都會以單一實體的形式呈現在裝置對應上，包括互動式折迭和擴充功能，可查看 IT 子網的詳細資料和上一頁。

下圖顯示已折迭的 IT 子網，其中包含27個 IT 網路元素。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="折迭的 IT 子網與27個 IT 網路元素":::

若要啟用 IT 網路折迭功能：

- 在 [系統設定] 視窗中，確定已啟用 [IT 網路功能]。

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="系統設定視窗":::

若要展開 IT 子網：

1. 若要區分 IT 和 OT 網路，請從 [系統設定] 畫面選取 [ **子網**]。

   > [!NOTE]
   > 建議您以有意義的名稱來命名每個子網，讓使用者可以輕鬆地識別這些子網，以便區分其和網路。

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="子網設定":::

2. 在 [編輯子網設定] 視窗中，清除您要定義為 IT 子網之每個子網的 [ICS 子網]。 IT 子網會在裝置對應中顯示折迭，並以 ICS 裝置的通知（例如控制器或 PLC）在 IT 網路中顯示。

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="編輯子網設定":::

3. 若要在地圖上展開 IT 網路，請在 [裝置] 視窗中，以滑鼠右鍵按一下它，然後選取 [ **展開網路**]。

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="展開您的網路視圖。":::

4. 確認方塊隨即出現，通知您無法重做版面配置變更。

5. 選取 [確定]。 IT 子網元素會顯示在地圖上。

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="確定":::

折迭 IT 子網：

1.  從左窗格中選取 [ **裝置**]。

2. 在 [裝置] 視窗中，選取折迭圖示。 紅色數位表示目前在地圖上顯示的已擴充的 IT 子網數目。

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="[裝置] 視窗":::

3. 選取您要折迭的子網 (s) 或選取 [ **全部** 折迭]。 選取的子網會在地圖上顯示為折迭狀態。

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="全部摺疊":::

折迭圖示會更新為已擴充的 IT 子網的更新數目。

## <a name="view-or-highlight-device-groups"></a>查看或醒目提示裝置群組

您可以根據裝置群組自訂地圖顯示。 例如，與特定 VLAN 或子網相關聯的裝置群組。 預先定義的群組可供使用，而且可以建立自訂群組。

查看群組依據：

  - 醒目提示 **：** 以藍色醒目提示屬於特定群組的裝置。

  - **篩選：** 只會在地圖上顯示屬於特定群組的裝置。

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="埠的標準視圖":::

可用的預先定義群組如下：

| 群組名稱 | 描述 |
|--|--|
| **已知的應用程式或非標準埠 (預設)** | 使用保留埠的裝置，例如 TCP。 使用非標準埠或未獲指派別名之埠的裝置。 |
| **(預設) 的通訊協定** | 處理 OT 流量的裝置。 |
| **授權 (預設)** | 在學習過程中或正式新增至網路的網路中探索到的裝置 |
| **裝置清查篩選** | 裝置會根據 [裝置清查] 資料表中的篩選器儲存來分組。 |
| **輪詢間隔** | 依輪詢間隔分組的裝置。 輪詢間隔會根據迴圈通道或期間自動產生。 例如，15.0 秒、3.0 秒、1.5 秒或任何間隔。 查看此資訊可協助您瞭解系統是否正在輪詢太快或太慢。 |
| **程式設計** | 工程工作站和程式控制控制器 |
| **子網路** | 屬於特定子網的裝置。 |
| **VLAN** | 與特定 VLAN ID 相關聯的裝置。 |
| **子網之間的連接** | 與跨子網連線建立關聯的裝置。 |
| **釘選的警示** | 使用者已釘選警示的裝置。 |
| **攻擊向量模擬** | 攻擊向量報告中偵測到易受攻擊的裝置。 若要在地圖上查看這些裝置，請在產生攻擊向量時選取 [ **在裝置對應上顯示** ] 核取方塊。 :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="新增攻擊向量模擬"::: |
| **上次出現時間** | 裝置依上次看見的時間範圍分組，例如：一小時、六小時、一天、七天。 |
| **不在 Active Directory** | 未與 Active Directory 通訊的所有非 PLC 裝置。 |

若要反白顯示或篩選裝置：

1. 從側邊功能表選取 [ **裝置對應** ]。

2. 選取篩選圖示。 :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="功能表":::

3. 從 [群組] 窗格中，選取您要醒目提示或篩選裝置的群組。

4. 選取 [ **反白顯示** ] 或 [ **篩選**]。

## <a name="define-custom-groups"></a>定義自訂群組

除了可供您查看預先定義的群組之外，您還可以定義自訂群組。 這些群組會顯示在裝置對應、裝置清查和資料採礦報告中。

> [!NOTE]
> 您也可以從裝置清查建立群組。

建立群組：

1. 從側邊功能表選取 [ **裝置** ]。 裝置地圖隨即顯示。

2. 選取 [ :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="群組設定"::: ] 以顯示 [群組] 設定。

3. 選取 [ :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="群組"::: ] 以建立新的自訂群組。

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="建立自訂群組畫面":::

4. 新增群組的名稱，最多可使用30個字元。

5. 選取相關的裝置，如下所示：

   - 從這個功能表中選取裝置，方法是從清單中選取這些裝置， (在箭號按鈕) ，<br /> 或者， <br /> 
   - 從這個功能表中複製裝置，方法是從選取的群組中複製裝置 (選取箭號按鈕) 

6. 選取 [新增群組]  。

### <a name="add-devices-to-a-custom-group"></a>將裝置新增至自訂群組

您可以將裝置新增至自訂群組，或建立新的自訂群組和裝置。

1. 以滑鼠右鍵按一下地圖上的裝置 (s) 。

2. 選取 [ **加入群組**]。

3. 在 [群組] 欄位中輸入組名，然後選取 [+]。 新群組隨即出現。 如果群組已存在，則會將它新增至現有的自訂群組。

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="群組名稱":::

4. 重複步驟1-3，將裝置新增至群組。

## <a name="map-zoom-views"></a>地圖縮放視圖

使用地圖視圖有助於在分析大型網路時加速辯論。

可以顯示三個裝置詳細資料檢視：

  - [鳥瞰圖](#birds-eye-view)

  - [裝置類型和連接視圖](#device-type-and-connection-view)

  - [詳細檢視](#detailed-view)

### <a name="birds-eye-view"></a>鳥瞰圖

此視圖提供如下所示的裝置一覽：

  - 紅點表示具有警示 (的裝置) 

  - 加星號點表示標示為重要的裝置

  - 黑色點指出沒有警示的裝置

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="鳥視圖":::

### <a name="device-type-and-connection-view"></a>裝置類型和連接視圖 

此視圖顯示在地圖上以圖示表示的裝置，以反白顯示具有警示、裝置類型和連線裝置的裝置。

  - 具有警示的裝置會以紅色環形顯示

  - 沒有警示的裝置會以灰色環形顯示

  - 顯示為星號的裝置標示為重要

裝置類型圖示會顯示為已連線的裝置。

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="連接視圖":::

### <a name="detailed-view"></a>詳細檢視 

詳細的觀點顯示裝置和裝置標籤，以及下列資訊：

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="詳細檢視":::

### <a name="control-the-zoom-view"></a>控制縮放視圖

顯示的地圖視圖視地圖縮放層級而定。 您可以藉由變更縮放層級來切換地圖視圖。

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="控制縮放視圖":::

### <a name="enable-simplified-zoom-views"></a>啟用簡化的縮放視圖

如果系統管理員想要讓安全性分析師和 RO 使用者存取鳥和裝置，以及輸入連接視圖，則應該啟用簡化的視圖選項。

若要啟用簡化的地圖視圖：

  - 選取 [ **系統設定** ]，然後切換 [ **簡化的地圖視圖** ] 選項。

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="簡化地圖視圖":::

## <a name="learn-more-about-devices"></a>深入瞭解裝置

提供廣泛的工具，以深入瞭解裝置的裝置對應：

- [裝置標籤和指標](#device-labels-and-indicators)

- [裝置快速查看](#device-quick-views)

- [查看和管理裝置屬性](#view-and-manage-device-properties)

- [查看裝置類型](#view-device-types)

- [底板](#backplane-properties)

- [查看裝置事件的時間軸](#view-a-timeline-of-events-for-the-device)

- [分析程式設計詳細資料和變更](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>裝置標籤和指標

下列標籤和指標可能會顯示在地圖上的裝置上：

| 裝置標籤 | 描述 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP 主機名稱"::: | IP 位址主機名稱和 IP 位址，或子網位址 |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="警示數目"::: | 與裝置相關聯的警示數目 |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | 裝置類型圖示，例如儲存體、PLC 或 historian。 |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="已分組的裝置"::: | 在 IT 網路的子網中分組的裝置數目。 在此範例中為8。 |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="裝置學習期間"::: | 在學習期間偵測到且未獲授權為網路裝置的裝置。 |
| 實心線條 | 裝置之間的邏輯連接 |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="新增裝置"::: | 完成學習之後探索到的新裝置。 |

### <a name="device-quick-views"></a>裝置快速查看

從地圖存取裝置屬性和連接。

若要開啟 [快速屬性] 功能表：

  - 選取 [快速屬性] 功能表的 [ :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="快速屬性] 功能表":::。

#### <a name="quick-device-properties"></a>快速裝置屬性

選取裝置或多個裝置，並開啟 [快速屬性] 畫面來查看這些裝置的重點：

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="快速裝置屬性":::

#### <a name="quick-connection-properties"></a>快速連接屬性

在 [快速屬性] 畫面開啟時選取連接，以查看在此連線中使用的通訊協定，以及最後一次看到的時機：

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="快速連接屬性":::

## <a name="view-and-manage-device-properties"></a>查看和管理裝置屬性

您可以針對地圖上顯示的每個裝置來查看裝置內容。 例如，裝置名稱、類型或 OS，或固件或廠商。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="查看和管理裝置屬性":::

您可以手動更新下列資訊。 手動輸入的資訊將會覆寫 Defender 針對 IoT 所探索到的資訊。

  - 名稱

  - 類型

  - OS

  - Purdue 圖層

  - 描述

| Item | 描述 |
|--|--|
| 基本資訊 | 所需的基本資訊。 |
| 名稱 | 裝置名稱。 <br /> 根據預設，感應器會探索網路中定義的裝置名稱。 例如，在 DNS 伺服器中定義的名稱。 <br /> 如果未定義這類名稱，則裝置 IP 位址會出現在此欄位中。 <br /> 您可以手動變更裝置名稱。 為您的裝置提供有意義的名稱，以反映其功能。 |
| 類型 | 感應器偵測到的裝置類型。 <br /> 如需詳細資訊，請參閱 [視圖裝置類型](#view-device-types)。 |
| 廠商 | 裝置廠商。 |
| 作業系統 | 裝置作業系統。 |
| Purdue 圖層 | 此裝置的感應器所識別的 Purdue 層，包括： <br /> -自動 <br /> -流程式控制制 <br /> -監督 <br /> - Enterprise |
| 描述 | 任意文字欄位。 <br /> 新增裝置的詳細資訊。 |
| 屬性 | 在學習期間探索到的裝置，以及不屬於其他類別的任何其他資訊，都會出現在 [屬性] 區段中。 <br /> 這項資訊是 RO 的。 |
| 設定 | 您可以手動變更裝置設定，以防止誤報： <br /> - **授權裝置**：在學習期間，會將網路中探索到的所有裝置識別為授權的裝置。 在學習期間探索到裝置之後，預設會顯示為未授權的裝置。 您可以手動變更這個定義。 <br /> - **所謂的掃描器**：如果您知道此裝置是掃描器，而不需要對您發出警示，請啟用此選項。 <br /> - 程式 **設計裝置**：如果您知道此裝置稱為程式設計裝置，而且不需要對您發出警示，請啟用此選項。 |
| 自訂群組 | 裝置對應中，此裝置所參與的自訂群組。 |
| 州 | 裝置的安全性和授權狀態： <br /> -沒有 `Secured` 警示時的狀態 <br /> -當裝置出現警示時，就會顯示警示數目 <br /> - `Unauthorized` 在學習期間新增至網路的裝置會顯示其狀態。 您可以 `Authorized Device` 在 [設定] 中手動定義裝置 <br /> -如果此裝置的位址定義為動態位址， `DHCP` 則會新增至狀態。 |


| 網路 | 描述 |
|--|--|
| 介面 | 裝置介面。 RO 欄位。 |
| 通訊協定 | 裝置使用的通訊協定。 RO 欄位。 |
| 韌體 | 如果有可用的背板資訊，將不會顯示固件資訊。 |
| 位址 | 裝置 IP 位址。 |
| 序列 | 裝置序號。 |
| 模組位址 | 裝置型號和插槽號碼或識別碼。 |
| 型號 | 裝置型號。 |
| 韌體版本 | 固件版本號碼。 |

若要查看裝置資訊：

1. 從側邊功能表選取 [ **裝置** ]。

2. 以滑鼠右鍵按一下裝置，然後選取 [ **視圖屬性**]。 裝置屬性視窗隨即顯示。

3. 選取此視窗底部的 [必要警示]，以查看此裝置警示的詳細資訊。

### <a name="view-device-types"></a>查看裝置類型

裝置會在裝置探索過程中自動識別裝置類型。 您可以手動變更類型。

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="查看裝置類型":::

下表提供系統中的所有類型：

| 類別 | 裝置類型 |
|--|--|
| 積體電路 | 工程站 <br /> Plc <br />史學 家 <br />人機界面 <br />Ied <br />DC 控制器 <br />RTU <br />工業封裝系統 <br />產業規模 <br />產業機器人 <br />位置 <br />計量 <br />變數頻率磁片磁碟機  <br />機器人控制器 <br />伺服 <br />氣動裝置 <br />Marquee |
| IT | 網域控制站 <br />DB 伺服器 <br />工作站 <br />伺服器 <br />終端機工作站 <br />儲存體 <br />Smart Phone <br />Tablet <br />備份伺服器 |
| IoT | IP 攝影機 <br />印表機  <br />打孔時鐘 <br />ATM <br />智慧型電視 <br />遊戲主控台 <br />Dvr <br />大門主控台 <br />暖 通 空調 <br />控溫器 <br />引發警示 <br />智慧型燈光 <br />智慧型切換 <br />引發偵測器 <br />IP 電話 <br />鬧鐘系統 <br />鬧鐘警報器 <br />動作偵測 <br />電梯 <br />濕度感應器 <br />條碼掃描器 <br />不斷電供應系統 <br />人員計數器系統 <br />Intercom <br />大門 |
| 網路 | 無線存取點 <br />路由器 <br />參數 <br />防火牆 <br />VPN 閘道 <br />NTP 伺服器 <br />Wifi Pineapple <br />實體位置 <br />I/o 介面卡 <br /> 通訊協定轉換器 |

若要查看裝置資訊：

1.  從側邊功能表選取 [ **裝置** ]。

2. 以滑鼠右鍵按一下裝置，然後選取 [ **視圖屬性**]。 裝置屬性視窗隨即顯示。

3. 選取此視窗底部的 [必要警示]，以查看此裝置警示的詳細資訊。

### <a name="backplane-properties"></a>背板屬性

如果 PLC 包含多個模組，並將其分成多個機架和插槽，則這些特性在模組卡之間可能會有所不同。 例如，如果 IP 位址和 MAC 位址相同，則此固件可能會不同。

您可以使用後擋板選項，以一個具有各種不同定義的實體來檢查多個控制器/卡片和其嵌套裝置。 背板視圖中的每個位置都代表基礎裝置，也就是在其背後探索到的裝置。

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="背板屬性":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="背板裝置屬性":::

背板最多可包含30個控制器卡和最多30個機架單位。 多個層級中包含的裝置總數最多可達200部裝置。

當偵測到背板詳細資料時，會在裝置屬性視窗中顯示背板窗格。

每個位置都會顯示基礎裝置的數目，以及顯示模組類型的圖示。

| 圖示 | 模組類型 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="電源"::: | 電源供應器 |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="類比 i/o"::: | 類比 i/o |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="通訊配接器"::: | 通訊配接器 |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="數位 i/o"::: | 數位 i/o |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="人機界面"::: | 人機界面 |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="泛型"::: | 泛型 |

當您選取一個位置時，會出現位置詳細資料：

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="選取位置":::

若要查看位置後方的基礎裝置，請選取 [ **在地圖上顯示**]。 位置會顯示在裝置對應中，並與所有的基礎模組和裝置連線。

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="在地圖上查看":::

## <a name="view-a-timeline-of-events-for-the-device"></a>查看裝置事件的時間軸

查看與裝置相關聯之事件的時間軸。

若要查看時程表：

1. 以滑鼠右鍵按一下地圖上的裝置。

2. 選取 [ **顯示事件**]。 [事件時間軸] 視窗隨即開啟，其中包含針對所選裝置偵測到的事件相關資訊。

如需詳細資訊，請參閱 [事件時間軸](#event-timeline) 。

## <a name="analyze-programming-details-and-changes"></a>分析程式設計詳細資料和變更

藉由顯示網路裝置上所執行的程式設計事件並分析程式碼變更，來增強辯論。 此資訊可協助您探索可疑的程式設計活動，例如：

  - 人為錯誤：工程師正在程式設計錯誤的裝置。

  - 已損毀的程式設計自動化：由於自動化失敗，程式設計會錯誤地執行。

  - 駭客攻擊的系統：登入程式設計裝置的未經授權使用者。

您可以顯示程式設計裝置，並透過其他裝置在其上執行各種程式設計變更。

查看程式設計裝置已新增、變更、移除或未變更的程式碼。 根據檔案類型、日期或感興趣的時間，搜尋程式設計變更。

### <a name="when-to-review-programming-activity"></a>查看程式設計活動的時機 

您可能需要查看程式設計活動：

  - 查看有關未經授權程式設計的警示之後

  - 在對控制器進行計畫的更新之後

  - 當進程或電腦無法正常運作時 (查看誰執行了上次更新的時間，以及何時) 

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="程式設計變更記錄":::

其他選項可讓您：

  - 以星星標示感興趣的事件。

  - 下載具有目前程式碼的 * .txt 檔案。

### <a name="about-authorized-vs-unauthorized-programming-events"></a>關於已授權和未經授權的程式設計事件 

未被學習或手動定義為程式設計裝置的裝置會執行未授權的程式設計事件。 已解決或手動定義為程式設計裝置的裝置會執行已授權的程式設計事件。

程式設計分析視窗會顯示已授權和未經授權的程式設計事件。

### <a name="accessing-programming-details-and-changes"></a>存取程式設計詳細資料和變更

從下列程式存取程式設計分析視窗：

- [事件時間軸](#event-timeline)

- [未經授權的程式設計警示](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>事件時間軸

使用 [事件時間軸] 來顯示偵測到程式設計變更之事件的時間軸。

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="事件時間軸的視圖。":::

### <a name="unauthorized-programming-alerts"></a>未經授權的程式設計警示

當未經授權的程式設計裝置執行程式設計活動時，就會觸發警示。

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="未經授權的程式設計警示":::

> [!NOTE]
> 您也可以在裝置屬性視窗和裝置清查中查看基本程式設計資訊。 請參閱 [裝置程式設計資訊：其他位置](#device-programming-information-additional-locations) 以取得詳細資料。

### <a name="working-in-the-programming-timeline-window"></a>在程式設計時間軸視窗中工作

本節說明如何查看程式設計檔和比較版本。 搜尋傳送至程式設計裝置的特定檔案。 搜尋檔案的依據：

  - Date

  - 檔案類型

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="程式設計時間軸視窗":::

|程式設計時間軸類型 | 描述 |
|--|--|
| 設計裝置 | 提供程式設計的裝置詳細資料，包括主機名稱和檔案。 |
| 最近的活動 | 顯示感應器偵測到的50最近事件。 <br />若要反白顯示事件，請將滑鼠停留在其上方，然後按一下星號。 :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> 您可以查看最後的50事件。 |
| 檔案儲存體 | 顯示所選日期和程式設計裝置上的檔案大小所偵測到的檔案。 <br /> 依預設，每個裝置可顯示的檔案數目上限為300。 <br /> 根據預設，每個檔案的檔案大小上限為 15 MB。 |
| 檔案狀態 :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | 檔案卷標指出裝置上檔案的狀態，包括： <br /> **已新增**：檔案已新增至所選日期或時間的端點。 <br /> **已更新**：檔案已在選取的日期或時間更新。 <br /> **已刪除**：已移除此檔案。 <br /> **沒有標籤**：檔案未變更。   |
| 程式設計裝置 | 進行程式設計變更的裝置。 多個裝置可能會在一種程式設計的裝置上執行程式設計變更。 系統會顯示變更的主機名稱、日期或時間，以及已登入的使用者。 |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | 顯示目前安裝在設計裝置上的檔案。 |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | 下載所顯示程式碼的文字檔。 |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | 將目前的檔案與在選取的日期所偵測到的檔案進行比較。 |

### <a name="choose-a-file-to-review"></a>選擇要檢查的檔案

本節說明如何選擇要檢查的檔案。

若要選擇要檢查的檔案：

1. 從 **最近的事件** 窗格中選取事件

2. 選取 [檔案] 窗格中的檔案。 檔案會出現在目前的窗格中。

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="選取要使用的檔案。":::

### <a name="compare-files"></a>比較檔案

本節說明如何比較程式設計檔。

若要比較：

1. 從最近的事件窗格中選取事件。

2. 從 [檔案] 窗格中選取檔案。 檔案會出現在目前的窗格中。 您可以比較這個檔案與其他檔案。

3. 選取 [比較] 指標。

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="比較指標":::

   此視窗會顯示在程式設計裝置上偵測到所選取檔案的所有日期。 檔案可能已透過多個程式設計裝置在程式設計裝置上更新。

   偵測到的差異數目會出現在視窗的右上角。 您可能需要向下滾動以查看差異。

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="向下移動到您的選取範圍":::

   此數位是由連續的變更文字行來計算。 例如，如果變更了八個連續的程式程式碼 (刪除、更新或新增) 這將會計算為一項差異。

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="您的程式設計時間軸視圖。":::

4. 選取日期。 在選取的日期偵測到的檔案會出現在視窗中。

5. 從 [最近使用的事件/檔案] 窗格選取的檔案一律會出現在右側。

### <a name="device-programming-information-additional-locations"></a>裝置程式設計資訊：其他位置

除了在程式設計時間軸中審核詳細資料，您還可以存取裝置屬性視窗和裝置清查中的程式設計資訊。

| 裝置類型 | 描述 |
|--|--|
| 裝置屬性 | [裝置屬性] 視窗提供在 device\. 上偵測到的最後一個程式設計事件的相關資訊。 :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="您裝置的屬性"::: |
| 裝置清查 | 裝置清查指出裝置是否為程式設計 device\。 :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="裝置清查"::: |

## <a name="manage-device-information-from-the-map"></a>從地圖管理裝置資訊

感應器不會直接在網路上更新或影響裝置。 此處所做的變更只會影響分析裝置的方式。

### <a name="delete-devices"></a>刪除裝置

如果學到的資訊無關，您可能會想要刪除裝置。 例如，

  - 工程工作站上的夥伴承包商會連接來執行設定更新。 工作完成後，就不應再監視裝置。

  - 由於網路中的變更，某些裝置已不再連接。

如果您未刪除裝置，感應器會繼續監視。 60天后，將會出現通知，建議您將其刪除。

如果有其他裝置嘗試存取裝置，您可能會收到警示，指出裝置沒有回應。 在此情況下，您的網路可能設定錯誤。

裝置將會從裝置對應、裝置清查和資料採礦報表中移除。 其他資訊，例如：儲存在 widget 中的資訊將保持不變。

裝置必須處於作用中狀態至少10分鐘，才能將其刪除。

若要從裝置對應刪除裝置：

1. 從側邊功能表選取 [ **裝置** ]。

2. 在裝置上按一下滑鼠右鍵，然後選取 [ **刪除**]。

### <a name="merge-devices"></a>合併裝置

在某些情況下，您可能需要合併裝置。 如果感應器發現不同的網路實體是唯一的裝置，則可能需要此項。 例如，

  - 有四張網路卡的 PLC

  - 具有 WIFI 和實體卡片的膝上型電腦

合併時，您要指示感應器將兩個裝置的裝置屬性合併成一個裝置。 當您這樣做時，裝置屬性視窗和感應器報告將會以新的裝置屬性詳細資料進行更新。

例如，如果您將兩個裝置與 IP 位址合併，則這兩個 IP 位址會在裝置屬性視窗中顯示為個別的介面。 您只能合併已授權的裝置。

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="裝置屬性視窗":::

事件時間軸會顯示 merge 事件。

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="包含合併事件的事件時間軸。":::

您無法復原裝置合併。 如果您誤合併兩個裝置，請刪除裝置，並等候感應器重新探索兩者。

若要合併裝置：

1. 選取兩個裝置，然後以滑鼠右鍵按一下其中一部裝置。

2. 選取 [ **合併** ] 以合併裝置。 最多可能需要2分鐘的時間才能完成合併。

3. 在 [設定合併裝置屬性] 對話方塊中，選擇裝置名稱。

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="屬性對話方塊":::

4. 選取 [儲存]。

### <a name="authorize-and-unauthorize-devices"></a>授權和未經授權裝置

在學習期間，會將網路中探索到的所有裝置識別為授權的裝置。 **授權** 的標籤不會出現在裝置對應中的這些裝置上。

在學習期間探索到裝置之後，它會顯示為未授權的裝置。 除了在地圖中看到未授權的裝置，您也可以在裝置清查中看到這些裝置。

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="裝置清查":::

**與未經授權的新裝置**

在學習期間之後偵測到的新裝置會顯示 `New` 並加上 `Unauthorized` 標籤。

如果您在地圖上移動裝置，或手動變更裝置屬性，則 `New` 會從裝置圖示中移除標籤。

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>未授權的裝置-攻擊媒介和風險評量報告

系統會將未經授權的裝置計算包含在風險評估報告和攻擊媒介報告中。

- **攻擊向量報表：** 標示為未經授權的裝置會在攻擊向量中解析為可能對網路造成威脅的可疑 rogue 裝置。

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="檢視您的攻擊向量報表":::

- **風險評量報告：** 標示為未授權的裝置包括：

  - 在風險評量報告中識別

若要手動授權或未經授權裝置：

1. 以滑鼠右鍵按一下地圖上的裝置，然後選取 [**未經授權**]

### <a name="mark-devices-as-important"></a>將裝置標示為重要

您可以將重要的網路裝置標示為重要，例如商務關鍵性伺服器。 這些裝置在地圖上以星號標示。 星號會根據地圖的縮放層級而有所不同。

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>重要裝置-攻擊媒介和風險評量報告

產生風險評定報告和攻擊向量報表時，會計算重要的裝置。

  - 標示為「重要」的攻擊向量報表裝置會在攻擊向量中解析為攻擊目標。 

  - 風險評量報告：在風險評估報告中提供安全性分數時，會計算標示為「重要」的裝置。

## <a name="generate-activity-reports-from-the-map"></a>從地圖產生活動報告

在1、6、12或24小時內產生所選裝置的活動報告。 可用資訊如下：

  - 類別：根據流量案例的基本偵測資訊。

  - 來源和目的地裝置

  - Data：脫離的其他資訊。

  - 最後一次看到的時間和日期。

您可以將報表儲存為 Microsoft Excel 或 Word 檔案。

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="[最近的活動] ":::

若要產生裝置的活動報告：

1. 以滑鼠右鍵按一下地圖上的裝置。

2. 選取活動報表。

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="查看活動的報表。":::

## <a name="generate-attack-vector-reports-from-the-map"></a>從地圖產生攻擊向量報表

模擬攻擊向量報表，以瞭解您所選取之地圖上的裝置是否為易受攻擊的目標。

攻擊向量報表提供可攻擊之裝置的弱點鏈的圖形標記法。 這些弱點可讓攻擊者存取重要的網路裝置。 攻擊向量模擬器會即時計算攻擊媒介，並分析每個特定目標的所有攻擊媒介。

若要在攻擊向量報告中查看裝置：

1. 以滑鼠右鍵按一下地圖上的裝置。

2. 選取 [ **模擬攻擊媒介**]。 [攻擊向量] 對話方塊隨即開啟，其中包含您選取作為攻擊目標的裝置。

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="新增攻擊向量模擬":::

3. 將其餘的參數加入至對話方塊，然後選取 [ **新增模擬**]。

## <a name="export-device-information-from-the-map"></a>從地圖匯出裝置資訊

從地圖匯出下列裝置資訊。

  -  (Microsoft Excel) 的裝置詳細資料

  -  (Microsoft Excel) 的裝置摘要

  - 具有群組 (Microsoft Word) 的 word 檔案

若要匯出：

1. 從地圖中選取 [匯出] 圖示。

1. 選取 [匯出] 選項。

## <a name="see-also"></a>請參閱

[調查裝置清查中的感應器偵測](how-to-investigate-sensor-detections-in-a-device-inventory.md)
