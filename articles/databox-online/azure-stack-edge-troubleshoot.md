---
title: 使用 Azure 入口網站 Azure Stack Edge Pro 進行疑難排解 |Microsoft Docs
description: 瞭解如何針對您 Azure Stack Edge Pro 上的問題進行疑難排解。 您可以執行診斷、收集支援的資訊，以及使用記錄來進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 01/21/2021
ms.author: alkohli
ms.openlocfilehash: 09325289dd5cb83bfc26dd26b266d50b622fe098
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763078"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-issues"></a>針對您的 Azure Stack Edge Pro 問題進行疑難排解

本文說明如何針對 Azure Stack Edge Pro 上的問題進行疑難排解。 

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 執行診斷
> * 收集支援套件
> * 使用記錄進行疑難排解
> * 針對 IoT Edge 錯誤進行疑難排解

## <a name="run-diagnostics"></a>執行診斷

若要診斷任何裝置錯誤並進行疑難排解，您可以執行診斷測試。 在您裝置的本機 Web UI 中執行下列步驟，以執行診斷測試。

1. 在本機 Web UI 中，移至 [疑難排解] > [診斷測試]。 選取您要執行的測試，然後選取 [ **執行測試**]。 這會執行測試，以診斷您的網路、裝置、Web Proxy、時間或雲端設定的任何可能問題。 您會收到裝置正在執行測試的通知。

    ![選取測試](media/azure-stack-edge-troubleshoot/run-diag-1.png)

2. 測試完成後會顯示結果。

    ![檢閱測試結果](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    如果測試失敗，則會顯示建議動作的 URL。 選取 URL 以查看建議的動作。

    ![檢閱失敗測試的警告](media/azure-stack-edge-troubleshoot/run-diag-3.png)

## <a name="collect-support-package"></a>收集支援套件

記錄套件包含有助於 Microsoft 支援小組疑難排解任何裝置問題的所有相關記錄。 您可以透過本機 Web UI 產生記錄套件。

執行下列步驟來收集支援套件。

1. 在本機 Web UI 中，移至 [疑難排解] > [支援]。 選取 [ **建立支援套件**]。 系統會開始收集支援套件。 套件收集可能需要幾分鐘的時間。

    ![按一下 [新增使用者]](media/azure-stack-edge-troubleshoot/collect-logs-1.png)

2. 建立支援封裝之後，請選取 [ **下載支援套件**]。 壓縮的套件會下載至您所選的路徑。 您可以將此套件解壓縮並檢視系統記錄檔。

    ![按一下 [新增使用者 2]](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>使用記錄進行疑難排解

在上傳和重新整理程序期間發生的所有錯誤都會包含在各自的錯誤檔案中。

1. 若要查看錯誤檔案，請移至您的共用，然後加以選取以查看內容。 

      ![連接到並檢視共用內容](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. 選取 _Microsoft Azure Stack Edge Pro 資料夾_。 這個資料夾有兩個子資料夾：

    * 具有上傳錯誤記錄檔的 [上傳] 資料夾。
    * 在重新整理期間發生錯誤的 [重新整理] 資料夾。

    以下是可供重新整理的範例記錄檔。

    ```xml
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ```

3. 當您在此檔案中看到錯誤 (在範例) 中反白顯示時，請記下錯誤碼，在此案例中為16001。 針對下列錯誤參考，查閱此錯誤碼的說明。

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="troubleshoot-iot-edge-errors"></a>針對 IoT Edge 錯誤進行疑難排解

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="next-steps"></a>後續步驟

* 深入了解[此版本中的已知問題](data-box-gateway-release-notes.md)。
