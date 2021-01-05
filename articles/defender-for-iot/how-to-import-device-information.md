---
title: 匯入裝置資訊
description: 適用于 IoT 的 Defender 感應器會監視及分析鏡像流量。 在這些情況下，您可能會想要匯入資料，以豐富已偵測到之裝置的資訊。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0a443ba7b41615fc62882bd8de6bf077c6b5a137
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838955"
---
# <a name="import-device-information-to-a-sensor"></a>將裝置資訊匯入感應器

適用于 IoT 的 Azure Defender 感應器會監視及分析鏡像流量。 在某些情況下，由於組織特定的網路設定原則，某些資訊可能無法傳輸。

在這些情況下，您可能會想要匯入資料，以豐富已偵測到之裝置的資訊。 有兩個選項可用來將資訊匯入至感應器：

- **從地圖匯入**：將裝置名稱、類型、群組或 Purdue 圖層更新為對應。

- **從** 匯入設定匯入：匯入裝置作業系統、IP 位址、修補程式等級或授權狀態。

## <a name="import-from-the-map"></a>從地圖匯入

本節說明如何將裝置名稱、類型、群組或 Purdue 圖層匯入至裝置對應。 您可以從地圖進行此作業。

以下是匯入需求：

- **名稱**：最多可有30個字元。

- **輸入** 或 **Purdue 圖層**：使用出現在 [ **裝置屬性** ] 對話方塊中的選項。  (在裝置上按一下滑鼠右鍵，然後選取 [ **視圖屬性**]。 ) 

- **裝置群組**：建立最多30個字元的新群組。 

> [!NOTE]
> 若要避免衝突，請不要將您從某個感應器匯出的資料匯入至另一個感應器。

若要匯入：

1. 在側邊功能表上，選取 [ **裝置**]。

2. 在 [ **裝置** ] 視窗的右上角，選取 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="裝置視窗的螢幕擷取畫面。":::

3. 選取 [ **匯出裝置**]。 匯出的檔案中會出現廣泛的資訊範圍。 此資訊包含裝置使用的通訊協定和裝置授權狀態。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="匯出檔案中的資訊。":::

4. 在 CSV 檔案中，只變更 [裝置名稱]、[類型]、[群組] 和 [Purdue] 圖層。 然後儲存檔案。 

   使用匯出檔案中顯示的大小寫標準。 例如，針對 Purdue 圖層，請使用所有的第一個字母大小寫。

5. 從 **裝置** 視窗的 [匯 **入/匯出**] 下拉式功能表中，選取 [匯 **入裝置**]。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="透過裝置視窗匯入裝置。":::

6. 選取 [匯 **入裝置** ]，然後選取您要匯入的 CSV 檔案。 匯入狀態訊息會顯示在畫面上，直到 [匯 **入裝置** ] 對話方塊關閉為止。

## <a name="import-from-import-settings"></a>從匯入設定匯入

本節說明如何將裝置 IP 位址、OS、修補程式等級或授權狀態匯入至裝置對應。 您可以從 [匯 **入設定** ] 對話方塊進行此作業。

匯入 IP 位址、OS 和修補程式等級：

1. 從說明[中心](https://cyberx-labs.zendesk.com/hc/en-us)下載[assets_info_2 2.8 和 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data)檔案，然後輸入資訊，如下所示：

   - **Ip 位址**：輸入裝置 ip 位址。

   - **作業系統**：從下拉式清單中選取。

   - **上次更新**：使用 yyyy-mm-dd 格式。

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="選項畫面。":::

2. 在側邊功能表上，選取 [匯 **入設定**]。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="匯入您的設定。":::

3. 若要上傳所需的設定，請在 [ **裝置資訊** ] 區段中，選取 [ **新增** ] 並上傳您準備的 CSV 檔案。

若要匯入授權狀態：

1. 從 Defender for IoT 說明中心下載並儲存 [authorized_assets.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 檔案。 確認您已將檔案儲存為 CSV。

2. 輸入下列資訊：

   - **Ip 位址**：裝置 ip 位址。

   - **名稱**：授權的裝置名稱。 請確定名稱是正確的。 在匯入清單中提供給裝置的名稱，會覆寫裝置對應中顯示的名稱。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="具有匯入裝置清單的 Excel 檔案。":::

3. 在側邊功能表上，選取 [匯 **入設定**]。

4. 在 [ **授權的裝置** ] 區段中，選取 [ **新增** ]，然後上傳您儲存的 CSV 檔案。

匯入資訊時，您會收到未授權裝置的相關警示，指出未出現在此清單中的所有裝置。

## <a name="import-device-information-to-the-sensor"></a>將裝置資訊匯入感應器

感應器會監視並分析鏡像流量。 在某些情況下，由於組織特定的網路設定原則，某些資訊可能無法傳輸。

在這些情況下，您可能會想要匯入資料，以在已偵測到的裝置上擴充裝置資訊。 有兩個選項可用來將資訊匯入至感應器：

- **從地圖匯入**：將裝置名稱、類型、群組或 Purdue 圖層更新為對應。

- **從** 匯入設定匯入：匯入裝置作業系統、IP 位址、修補程式等級或授權狀態。

### <a name="import-from-the-map"></a>從地圖匯入

本節說明如何將裝置名稱、類型、群組或 Purdue 圖層匯入至裝置對應。 您可以從地圖進行此作業。

以下是匯入需求：

- **名稱**：最多可有30個字元。

- **輸入** 或 **Purdue 圖層**：使用出現在 [ **裝置屬性** ] 對話方塊中的選項。  (在裝置上按一下滑鼠右鍵，然後選取 [ **視圖屬性**]。 ) 

- **裝置群組**：建立最多30個字元的新群組。 

> [!NOTE]
> 若要避免衝突，請不要將您從某個感應器匯出的資料匯入至另一個感應器。

若要匯入：

1. 在側邊功能表上，選取 [ **裝置**]。

2. 在 [ **裝置** ] 視窗的右上角，選取 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="要從中挑選裝置的視窗。":::

3. 選取 [ **匯出裝置**]。 匯出的檔案中會出現廣泛的資訊範圍。 範例包括裝置使用的通訊協定和裝置授權狀態。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="匯出檔案中的資訊。":::

4. 在 CSV 檔案中，只變更裝置名稱、類型、群組和 Purdue 圖層。 然後儲存檔案。 

   使用匯出檔案中顯示的大小寫標準。 例如，針對 Purdue 圖層，請使用所有的第一個字母大小寫。

5. 從 **裝置** 視窗的 [匯 **入/匯出**] 下拉式功能表中，選取 [匯 **入裝置**]。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="匯入您的裝置。":::

6. 選取 [匯 **入裝置** ]，然後選取您要匯入的 CSV 檔案。 匯入狀態訊息會顯示在畫面上，直到 [匯 **入裝置** ] 對話方塊關閉為止。

### <a name="import-from-import-settings"></a>從匯入設定匯入 

本節說明如何將裝置 IP 位址、OS、修補程式等級或授權狀態匯入至裝置對應。 您可以從 [匯 **入設定** ] 對話方塊進行此作業。

匯入 IP 位址、OS 和修補程式等級：

1. 從說明[中心](https://cyberx-labs.zendesk.com/hc/en-us)下載[assets_info_2 2.8 和 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data)檔案，然後輸入資訊，如下所示：

   - **Ip 位址**：裝置 ip 位址。

   - **作業系統**：從下拉式清單中選取。

   - **上次更新**：使用 yyyy-mm-dd 格式。

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="畫面上的內容。":::

2. 在側邊功能表上，選取 [匯 **入設定**]。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="填寫 [匯入設定] 畫面。":::

3. 若要上傳所需的設定，請在 [ **裝置資訊** ] 區段中，選取 [ **新增** ]，然後上傳您已備妥的 CSV 檔案。

若要匯入授權狀態：

1. 從 Defender for IoT 說明中心下載並儲存 [authorized_assets.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 檔案。 確認您已將檔案儲存為 CSV。

2. 輸入下列資訊：

   - **Ip 位址**：裝置 ip 位址。

   - **名稱**：授權的裝置名稱。 請確認名稱正確無誤。 在匯入清單中提供給裝置的名稱，會覆寫裝置對應中顯示的名稱。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="匯入至裝置對應的清單。":::

3. 在側邊功能表上，選取 [匯 **入設定**]。

4. 在 [ **授權的裝置** ] 區段中，選取 [ **新增** ]，然後上傳您儲存的 CSV 檔案。

匯入資訊時，您會收到未授權裝置的相關警示，指出未出現在此清單中的所有裝置。

## <a name="see-also"></a>請參閱

[控制監視的流量](how-to-control-what-traffic-is-monitored.md)

[調查裝置清查中的感應器偵測](how-to-investigate-sensor-detections-in-a-device-inventory.md)
