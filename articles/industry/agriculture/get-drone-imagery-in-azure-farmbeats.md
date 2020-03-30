---
title: 取得無人機影像
description: 本文介紹如何從合作夥伴處獲取無人機圖像。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132040"
---
# <a name="get-drone-imagery-from-drone-partners"></a>從無人機合作夥伴處獲取無人機圖像

本文介紹如何將無人機影像合作夥伴的正交鑲嵌資料引入 Azure FarmBeats Datahub。 正形馬賽克是一種航空插圖或圖像，從無人機收集的資料中進行幾何校正和縫合。

目前，支援以下影像合作夥伴。

  ![FarmBeats 無人機影像合作夥伴](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

將無人機影像資料與 Azure FarmBeats 集成可説明您從伺服器場上執行的無人機飛行中的正射馬賽克資料獲取到資料庫。 資料可用後，您可以在 FarmBeats 加速器中查看資料。 這些資料可用於資料融合、人工智慧和機器學習模型構建。

## <a name="before-you-begin"></a>開始之前

  - 確保安裝了 Azure 伺服器場節拍。 有關如何安裝伺服器場節拍的資訊，請參閱[安裝 Azure FarmBeats](install-azure-farmbeats.md)。
  - 確保您擁有希望在 FarmBeats 系統中定義無人機圖像的伺服器場。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>實現無人機影像集成與 FarmBeats

向設備供應商提供以下資訊，以便與 FarmBeats 集成：
 - API 端點
 - 租用戶識別碼
 - 用戶端識別碼
 - 用戶端密碼

請遵循下列步驟。

1. 下載此[腳本](https://aka.ms/farmbeatspartnerscript)，並將其提取到本地磁碟機。 ZIP 檔案內有兩個檔。
2. 登入 [Azure 入口網站](https://portal.azure.com/)並開啟 Azure Cloud Shell。 此選項在門戶右上角的工具列上可用。

    ![在門戶的右上角欄上打開 Azure 雲外殼](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 確保環境設置為**PowerShell**。

    ![電源外殼設置](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 上傳從雲殼實例中步驟 1 下載的兩個檔。

    ![上傳檔案](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 轉到上載檔的目錄。 預設情況下，它們在使用者名下上載到主目錄。
6. 執行下列指令碼：

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. 按照螢幕上的說明捕獲 API 終結點、租戶 ID、用戶端 ID、用戶端金鑰和事件Hub連接字串的值。

    將所需的憑據輸入合作夥伴的無人機軟體系統後，可以從 FarmBeats 系統導入所有伺服器場。 然後，您可以使用伺服器場詳細資訊來執行飛行路徑規劃和無人機圖像收集。

    在無人機供應商的軟體處理原始圖像後，無人機軟體系統將拼接的正交馬賽克和其他經過處理的圖像上傳到資料hub中。

## <a name="view-drone-imagery"></a>查看無人機圖像

將資料發送到 FarmBeats 資料庫後，可以使用 FarmBeats Datahub API 查詢場景存儲。

或者，您可以在 **"農場詳細資訊**"頁上查看最新的無人機圖像。 要查看圖像，請按照以下步驟操作。

1. 選擇將影像上載到的伺服器場。 將顯示 **"伺服器場**詳細資訊"頁。
2. 向下滾動到最新的**精度地圖**部分。
3. 在 **"無人機影像"** 部分查看圖像。

    ![無人機影像部分](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>下載無人機圖像

選擇無人機影像部分時，將打開一個快顯視窗，顯示無人機正射馬賽克的高解析度圖像。

![高解析度正射馬賽克](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>查看所有無人機地圖

無人機供應商上傳的檔和圖像將顯示在 **"地圖"** 部分。 選擇 **"地圖**"部分，按 **"伺服器場"** 篩選，然後選擇要查看和下載的相應檔。

  ![地圖部分](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>後續步驟

瞭解如何使用 FarmBeats Datahub [API](rest-api-in-azure-farmbeats.md)獲取無人機圖像。
