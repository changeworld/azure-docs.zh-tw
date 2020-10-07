---
title: 快速入門 - 將 Azure Kinect 感應器串流錄製到檔案
description: 在本快速入門中，您將了解如何將來自感應器 SDK 的資料流錄製到檔案。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azure, kinect, 錄製, 播放, 讀取器, matroska, mkv, 串流, 景深, rgb, 相機, 色彩, imu, 音訊, 感應器
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277773"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>快速入門：將 Azure Kinect 感應器串流錄製到檔案

本快速入門提供如何使用 [Azure Kinect 錄製器](azure-kinect-recorder.md)工具將來自感應器 SDK 的資料流錄製到檔案的相關資訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

本快速入門假設：

- 您已將 Azure Kinect DK 連線到主機電腦，並已正確供電。
- 您已完成硬體的[設定](set-up-azure-kinect-dk.md)。

## <a name="create-recording"></a>建立錄製內容

1. 開啟命令提示字元，並提供 [Azure Kinect 錄製器](azure-kinect-recorder.md)的路徑 (位於已安裝的工具目錄中，名稱為 `k4arecorder.exe`)。 例如： `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe` 。
2. 錄製 5 秒。

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. 根據預設，錄製器會使用 NFOV 未分類收納景深模式，並以 30 fps 輸出 1080p RGB，包括 IMU 資料。 如需錄製選項和秘訣的完整概觀，請參閱 [Azure Kinect 錄製器](azure-kinect-recorder.md)。

## <a name="play-back-recording"></a>播放錄製內容

您可以使用 [Azure Kinect 檢視器](azure-kinect-viewer.md)來播放錄製內容。

1. 啟動 [`k4aviewer.exe`](azure-kinect-viewer.md)
2. 展開 [開啟錄製內容] 索引標籤，然後開啟您的錄製內容。

## <a name="next-steps"></a>後續步驟

您已了解如何將感應器串流錄製到檔案，接下來可以開始

> [!div class="nextstepaction"]
> [建置第一個 Azure Kinect 應用程式](build-first-app.md)
