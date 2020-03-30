---
title: 從合作夥伴獲取感應器資料
description: 本文介紹如何從合作夥伴獲取感應器資料。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 916c828365c8f9f50f408bd6c51182bb6e89605f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384189"
---
# <a name="get-sensor-data-from-sensor-partners"></a>從感應器合作夥伴獲取感應器資料

Azure FarmBeats 可説明您將來自 IoT 設備和感應器的流式處理資料引入資料集。 目前，支援以下感應器設備合作夥伴。

  ![農場節拍合作夥伴](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

將設備資料與 Azure FarmBeats 集成可説明您從伺服器場中部署的 IoT 感應器獲取地面資料到資料中心。 資料一旦可用，可以通過 FarmBeats 加速器進行視覺化。 這些資料可用於資料融合和機器學習/人工智慧 （ML/AI） 模型構建，使用 FarmBeats。

要啟動感應器資料流程，請確保：

-  您在 Azure 應用商店中安裝了伺服器場節拍。
-  您決定在伺服器場上安裝的感應器和設備。
-  如果您計畫使用土壤水分感應器，請使用 FarmBeats 土壤水分感應器放置圖，獲取有關感應器數量以及感應器的確切位置的建議。 有關詳細資訊，請參閱[生成地圖](generate-maps-in-azure-farmbeats.md)。
- 您可以在伺服器場中從設備合作夥伴購買和部署設備或感應器。 確保您可以通過設備合作夥伴的解決方案訪問感應器資料。

## <a name="enable-device-integration-with-farmbeats"></a>啟用與伺服器場節拍的設備集成

開始感應器資料流程後，可以開始將資料輸入 FarmBeats 系統的過程。 向設備供應商提供以下資訊，以啟用與 FarmBeats 的集成：

 - API 端點
 - 租用戶識別碼
 - 用戶端識別碼
 - 用戶端密碼
 - 事件Hub連接字串

可以通過以下步驟生成上述資訊：（請注意，這些步驟需要在 Azure 上完成，因此您需要訪問部署伺服器場節拍的 Azure 訂閱）

1. 登入 https://portal.azure.com/。

2. **如果您在 FarmBeats 版本 1.2.7 或更高版本上，請跳過步驟 2a、2b 和 2c，然後轉到步驟 3。** 您可以通過按一下 FarmBeats UI 右上角的"設置"圖示來檢查伺服器場節拍版本。

2a. 轉到 Azure 活動目錄 ->應用註冊

2b. 按一下作為伺服器場節拍部署的一部分創建的應用註冊。 它將具有與伺服器場Beats資料中心相同的名稱。

2c. 按一下"公開 API" ->按一下"添加用戶端應用程式"並輸入**04b07795-8ddb-461a-bbee-02f9e1bf7b46**並選中"授權範圍"。 這將授予 Azure CLI（雲外殼）以執行以下步驟的存取權限。

3. 開啟 Cloud Shell。 此選項在 Azure 門戶右上角的工具列上可用。

    ![Azure 門戶工具列](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

5. 確保環境設置為**PowerShell**。 預設情況下，它設置為 Bash。

    ![PowerShell 工具列設置](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

6. 轉到主目錄。

   ```azurepowershell-interactive 

    cd  

    ```

7. 執行下列命令。 這將將腳本下載到您的主目錄。

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1 

    ```

8. 執行下列指令碼。 該腳本要求從 Azure 活動目錄 -> 概述頁獲取的租戶 ID。

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

9. 按照螢幕上的說明捕獲**API 終結點**、**租戶 ID、****用戶端 ID、****用戶端金鑰**和**事件Hub連接字串**的值。

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>使用生成的憑據集成設備資料

現在，您從上一節生成了以下資訊。
 - API 端點
 - 事件Hub連接字串
 - 用戶端識別碼
 - 用戶端密碼
 - 租用戶識別碼
 
您需要向設備合作夥伴提供此功能以連結 FarmBeats。 轉到設備合作夥伴門戶執行相同的操作。 例如，如果您使用的是大衛斯儀器、太溶或佩斯爾儀器（Metos.at）的設備，請轉到如下所述的相應頁面：

[大衛斯儀器](https://weatherlink.github.io/azure-farmbeats/setup)

[太溶](https://app.teralytic.com/)

[佩斯爾儀器](https://ng.fieldclimate.com/user-api-services)

 設備提供程式確認集成成功。 確認後，您可以查看 Azure FarmBeats 上的所有設備和感應器。

## <a name="view-devices-and-sensors"></a>查看設備和感應器

使用以下部分查看伺服器場的設備和感應器。

### <a name="view-devices"></a>檢視裝置

目前，FarmBeats 支援以下設備：

- **節點**：一個或多個感應器連接到的設備。
- **閘道**：一個或多個節點連接到的設備。

請遵循下列步驟。

1. 在主頁上，從功能表中選擇 **"設備**"。
  "**設備"** 頁顯示裝置類型、型號、狀態、放置在其中的伺服器場以及中繼資料的最後更新日期。 預設情況下，伺服器場列設置為*Null*。 您可以選擇將設備分配給伺服器場。 有關詳細資訊，請參閱[分配設備](#assign-devices)。
2. 選擇設備以查看連接到設備的裝置屬性、遙測和子設備。

    ![[裝置] 頁面](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>查看感應器

請遵循下列步驟。

1. 在主頁上，從功能表中選擇 **"感應器**"。
  "**感應器"** 頁顯示有關感應器類型、所連接的伺服器場、父設備、埠名稱、埠類型和上次更新狀態的詳細資訊。
2. 選擇感應器以查看感應器屬性、活動警報和感應器的遙測資料。

    ![感應器頁面](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>分配設備  

感應器資料流程入後，您可以將其分配給部署感應器的伺服器場。

1. 在主頁上，從功能表中選擇 **"農場**"。 將顯示 **"伺服器場**"清單頁。
2. 選擇要為其分配設備的伺服器場，然後選擇 **"添加設備**"。
3. 將顯示"**添加設備"** 視窗。 選擇要分配給伺服器場的設備。

    ![添加設備視窗](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 選擇 **"添加設備**"。 或者，轉到 **"設備"** 功能表，選擇要分配給伺服器場的設備，然後選擇 **"關聯設備**"。
5. 在 **"關聯設備"** 視窗中，從下拉清單中選擇伺服器場，然後選擇"**全部應用**"將伺服器場關聯到所有選定的設備。

    ![關聯設備視窗](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 要將每個設備關聯到不同的伺服器場，請選擇"**分配給伺服器場"** 列中的下拉箭頭，然後為每個設備行選擇一個伺服器場。
7. 選擇 **"分配"** 以完成設備分配。

### <a name="visualize-sensor-data"></a>將感應器資料視覺化

請遵循下列步驟。

1. 在主頁上，從功能表中選擇 **"伺服器場**"以查看 **"伺服器場"** 頁。
2. 選擇要查看感應器資料的**伺服器場**。
3. 在 **"伺服器場"** 儀表板上，您可以查看遙測資料。 您可以查看即時遙測或使用**自訂範圍**查看特定日期範圍。

    ![伺服器場儀表板](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>刪除感應器

請遵循下列步驟。

1. 在主頁上，從功能表中選擇 **"感應器**"以查看 **"感應器"** 頁面。
2. 選擇要刪除的設備，並在確認視窗中選擇 **"刪除**"。

    ![刪除按鈕](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

確認訊息顯示感應器已成功刪除。

## <a name="delete-devices"></a>刪除裝置

請遵循下列步驟。

1. 在主頁上，從功能表中選擇 **"設備**"以查看 **"設備"** 頁。
2. 選擇要刪除的設備，並在確認視窗中選擇 **"刪除**"。

    ![刪除按鈕](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>後續步驟

現在，感應器資料會流入 Azure FarmBeats 實例。 現在，瞭解如何為伺服器場[生成地圖](generate-maps-in-azure-farmbeats.md#generate-maps)。
