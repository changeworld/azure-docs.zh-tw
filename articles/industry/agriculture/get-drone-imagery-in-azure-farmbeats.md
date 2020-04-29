---
title: 取得無人機影像
description: 本文說明如何從合作夥伴取得無人機的影像。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77132040"
---
# <a name="get-drone-imagery-from-drone-partners"></a>從無人機合作夥伴取得無人機影像

本文說明如何將無人機影像合作夥伴的 orthomosaic 資料帶入 Azure FarmBeats Datahub。 Orthomosaic 是從無人機所收集的資料進行幾何校正和拼接的鳥瞰圖或影像。

目前支援下列影像合作夥伴。

  ![FarmBeats 無人機影像合作夥伴](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

將無人機影像資料與 Azure FarmBeats 整合，可協助您從在伺服器陣列上進行的無人機航班 orthomosaic 資料到 datahub。 資料可供使用之後，您可以在 FarmBeats 加速器中加以查看。 資料可以用於資料融合、人工智慧和機器學習模型建立。

## <a name="before-you-begin"></a>開始之前

  - 請確定您已安裝 Azure FarmBeats。 如需有關如何安裝 FarmBeats 的詳細資訊，請參閱[安裝 Azure FarmBeats](install-azure-farmbeats.md)。
  - 確定您有想要在 FarmBeats 系統中定義無人機影像的伺服器陣列。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>啟用無人機影像與 FarmBeats 的整合

將下列資訊提供給您的裝置提供者，以啟用與 FarmBeats 的整合：
 - API 端點
 - 租用戶識別碼
 - 用戶端識別碼
 - 用戶端密碼

依照下列步驟進行。

1. 下載此[腳本](https://aka.ms/farmbeatspartnerscript)，並將它解壓縮至您的本機磁片磁碟機。 Zip 檔案中有兩個檔案。
2. 登入 [Azure 入口網站](https://portal.azure.com/)並開啟 Azure Cloud Shell。 在入口網站右上角的工具列上，可以使用此選項。

    ![在入口網站的右上方列上開啟 Azure Cloud Shell](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 請確定環境已設定為**PowerShell**。

    ![PowerShell 設定](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 上傳您在 Cloud Shell 實例中步驟1下載的兩個檔案。

    ![上傳檔案](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 移至上傳檔案的目錄。 根據預設，它們會上傳至使用者名稱底下的主目錄。
6. 執行下列指令碼：

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. 依照畫面上的指示來捕獲 API 端點、租使用者識別碼、用戶端識別碼、用戶端密碼和 EventHub 連接字串的值。

    在您將所需的認證輸入夥伴的無人機軟體系統之後，就可以從 FarmBeats 系統匯入所有伺服器陣列。 接著，您可以使用伺服器陣列詳細資料來進行航班路徑規劃和無人機映射集合。

    當無人機提供者的軟體處理原始影像之後，無人機軟體系統會將拼接 orthomosaic 和其他處理過的影像上傳到 datahub。

## <a name="view-drone-imagery"></a>觀看無人機影像

將資料傳送至 FarmBeats datahub 之後，您就可以使用 FarmBeats Datahub Api 來查詢場景存放區。

或者，您可以在 [**伺服器陣列詳細資料**] 頁面上，查看最新的無人機影像。 若要查看影像，請遵循步驟。

1. 選取您的影像上傳到的伺服器陣列。 [**伺服器**陣列詳細資料] 頁面隨即出現。
2. 向下卷到最新的 [**精確度對應**] 區段。
3. 在 [**無人機影像**] 區段中，查看影像。

    ![無人機影像區段](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>下載無人機影像

當您選取 [無人機影像] 區段時，快顯視窗會隨即開啟，以顯示無人機 orthomosaic 的高解析度影像。

![高解析度 orthomosaic](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>查看所有無人機對應

無人機提供者所上傳的檔案和映射會顯示在 [**對應**] 區段中。 選取 [**對應**] 區段、[依**伺服器**陣列篩選]，然後選取要查看和下載的適當檔案。

  ![Maps 區段](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>後續步驟

瞭解如何使用 FarmBeats Datahub [api](rest-api-in-azure-farmbeats.md)來取得您的無人機影像。
