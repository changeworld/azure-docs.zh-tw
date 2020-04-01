---
title: 從合作夥伴取得感應器資料
description: 本文介紹如何從合作夥伴獲取感測器數據。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 113ab07af8ada16c0779da510c5f5b1f1f5a290b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398224"
---
# <a name="get-sensor-data-from-sensor-partners"></a>從感測器合作夥伴取得感應器資料

Azure FarmBeats 可説明您將來自 IoT 設備和感測器的流式處理數據引入資料集。 目前,支援以下感測器設備合作夥伴。

  ![農場節拍合作夥伴](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

將設備資料與 Azure FarmBeats 整合可説明您從伺服器場中部署的 IoT 感測器獲取地面數據到資料中心。 數據一旦可用,可以通過 FarmBeats 加速器進行可視化。 這些數據可用於數據融合和機器學習/人工智慧 (ML/AI) 模型構建,使用 FarmBeats。

要啟動偵測器資料流程,請確保:

-  您在 Azure 應用商店中安裝了伺服器場節拍。
-  您決定在伺服器場上安裝的感測器和設備。
-  如果您計劃使用土壤水分感測器,請使用 FarmBeats 土壤水分感測器放置圖,獲取有關感測器數量以及感測器的確切位置的建議。 有關詳細資訊,請參閱[產生地圖](generate-maps-in-azure-farmbeats.md)。
- 您可以在伺服器場中從設備合作夥伴購買和部署設備或感測器。 確保您可以通過設備合作夥伴的解決方案訪問感測器數據。

## <a name="enable-device-integration-with-farmbeats"></a>開啟與伺服器場節拍的裝置整合

開始感測器數據流后,可以開始將數據輸入 FarmBeats 系統的過程。 提供使用者資訊提供以下資訊,以開啟與 FarmBeats 的整合:

 - API 端點
 - 租用戶識別碼
 - 用戶端識別碼
 - 用戶端密碼
 - 事件Hub連接字串

按照以下步驟生成上述資訊:

> [!NOTE]
> 這些步驟需要在 Azure 上完成才能訪問部署伺服器場節拍的 Azure 訂閱。

1. 登入 https://portal.azure.com/。

2. **如果您在 FarmBeats 版本 1.2.7 或更高版本中,請跳過步驟 a、b 和 c,然後轉到步驟 3。** 您可以通過選擇 FarmBeats UI 右上角的 **「設定」** 圖示來檢查伺服器場節拍版本。

      a.  跳到**Azure 的目錄** > **應用程式**

      b. 選擇此伺服器場節拍部署的一部分建立**的應用程式**。 它將具有與伺服器場Beats資料庫相同的名稱。

      c. 選擇 **「公開 API>** 選擇 **」新增用戶端應用程式**「並輸入**04b07795-8ddb-461a-bbee-02f9e1bf7b46**並選擇此**授權範圍**。 這將授予 Azure CLI(雲外殼)以執行以下步驟的訪問許可權:

3. 開啟 Cloud Shell。 此選項在 Azure 門戶右上角的工具列上可用。

    ![Azure 門戶工具列](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 確保環境設定為**PowerShell**。 默認情況下,它設置為 Bash。

    ![PowerShell 工具列設定](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 轉到主目錄。

    ```azurepowershell-interactive 
    cd  
    ```

6. 執行下列命令。 這將將腳本下載到您的主目錄。

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. 執行下列指令碼。 該腳本要求提供租戶 ID,可以從**Azure 活動目錄** > **概述**頁獲取該 ID。

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. 按照螢幕上的說明擷取**API 終結點**、**租戶 ID、****用戶端 ID、****用戶端金鑰**和**事件Hub連接字串**的值。

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>使用產生的認證整合裝置資料

現在,您從上一節生成了以下資訊。
 - API 端點
 - 事件Hub連接字串
 - 用戶端識別碼
 - 用戶端密碼
 - 租用戶識別碼

您需要向設備合作夥伴提供此功能以連結 FarmBeats。 轉到設備合作夥伴門戶執行相同的操作。 例如,如果您使用的是戴維斯儀器、太溶或佩斯爾儀器(Metos.at)的設備,則按照以下說明訪問相應的頁面:

1. [大衛斯儀器](https://weatherlink.github.io/azure-farmbeats/setup)

2. [太溶](https://app.teralytic.com/)

3. [佩斯爾儀器](https://ng.fieldclimate.com/user-api-services)

設備提供程式確認集成成功。 確認後,您可以查看 Azure FarmBeats 上的所有設備和感測器。

## <a name="view-devices-and-sensors"></a>檢視裝置和感應器

使用以下部分查看伺服器場的設備和感測器。

### <a name="view-devices"></a>檢視裝置

目前,FarmBeats 支援以下設備:

- **節點**:一個或多個感測器連接到的設備。
- **閘道**:一個或多個節點連接到的設備。

請遵循下列步驟：

1. 在主頁上,從功能表中選擇 **「設備**」。。
  **裝置「** 頁顯示裝置類型、型號、狀態、放置在其中的伺服器場以及元數據的最後更新日期。 預設情況下,伺服器列設定為*NULL*。 您可以選擇將裝置分配給伺服器場。 有關詳細資訊,請參閱[分配裝置](#assign-devices)。
2. 選擇設備以查看連接到設備的設備屬性、遙測和子設備。

    ![[裝置] 頁面](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>檢視感應器

請遵循下列步驟：

1. 在主頁上,從功能表中選擇 **「感測器**」。。
  "**感應器「** 頁顯示有關感應器類型、連接的伺服器場、父設備、埠名稱、埠型態和上次更新狀態的詳細資訊。
2. 選擇感測器以查看感測器屬性、活動警報和感測器的遙測數據。

    ![感應器頁面](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>配置裝置  

感測器數據流入後,您可以將其分配給部署感測器的伺服器場。

1. 在主頁上,從菜單中選擇 **「農場**」。。 將顯示 **「伺服器場**」列表頁。
2. 選擇要為其分配設備的伺服器場,然後選擇 **「添加設備**」 。
3. 將顯示「**添加設備」** 視窗。 選擇要分配給伺服器場的設備。

    ![新增裝置視窗](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 選擇 **「添加設備**」。 或者,轉到 **「設備」** 選單,選擇要分配給伺服器場的設備,然後選擇 **「關聯裝置**」。。
5. 在 **「關聯裝置」** 視窗中,從下拉清單中選擇伺服器場,然後選擇「**全部應用**」將伺服器場關聯到所有選定的裝置。

    ![關聯裝置視窗](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 要將每個設備關聯到不同的伺服器場,請選擇「**分配給伺服器場」** 列中的下拉箭頭,然後為每個設備行選擇一個伺服器場。

7. 選擇 **「分配」** 以完成裝置分配。

### <a name="visualize-sensor-data"></a>將感應器資料視覺化

請遵循下列步驟：

1. 在主頁上,從選單中選擇 **「伺服器場**」以查看 **「伺服器場」** 頁。
2. 選擇要檢視器資料的**伺服器場**。
3. 在 **「伺服器場」** 儀表板上,您可以查看遙測數據。 您可以查看即時遙測或使用**自定義範圍**查看特定日期範圍。

    ![伺服器場儀表板](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>移除感應器

請遵循下列步驟：

1. 在主頁上,從選單中選擇 **「感測器**」以查看 **「感測器」** 頁面。
2. 選擇要刪除的裝置,並在確認視窗中選擇 **「刪除**」 。

    ![刪除按鈕](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

確認消息顯示感測器已成功刪除。

## <a name="delete-devices"></a>刪除裝置

請遵循下列步驟：

1. 在主頁上,從選單中選擇 **「設備**」以查看 **「設備」** 頁。
2. 選擇要刪除的裝置,並在確認視窗中選擇 **「刪除**」 。

    ![刪除按鈕](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>後續步驟

現在,感測器數據會流入 Azure FarmBeats 實例。 現在,瞭解如何為伺服器場[產生地圖](generate-maps-in-azure-farmbeats.md#generate-maps)。
