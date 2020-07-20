---
title: Azure Kinect 的已知問題和疑難排解
description: 瞭解搭配 Azure Kinect DK 使用感應器 SDK 時的一些已知問題和疑難排解秘訣。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: 疑難排解，更新，bug，kinect，意見反應，修復，記錄，秘訣
ms.openlocfilehash: 2db7b17ffc8b6595107dbc52fae719aa9f2d15db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276548"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Azure Kinect 的已知問題和疑難排解

此頁面包含搭配 Azure Kinect DK 使用感應器 SDK 時的已知問題和疑難排解秘訣。 另請參閱產品[支援頁面](https://aka.ms/kinectsupport)，以瞭解產品的硬體特定問題。

## <a name="known-issues"></a>已知問題

- ASMedia USB 主機控制器的相容性問題（例如 ASM1142 晶片）
  - 某些使用 Microsoft USB 驅動程式的情況可以解除封鎖
  - 許多電腦也有替代的主機控制器，而變更 USB3 埠可能會有説明

如需更多感應器 SDK 的相關問題，請檢查[GitHub 問題](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>收集記錄檔

K4A.dll 的記錄是透過環境變數來啟用。 根據預設，記錄會傳送到 stdout，而且只會產生錯誤和重要訊息。 這些設定可以改變，讓記錄移至檔案。 詳細資訊也可以視需要調整。 以下是適用于 Windows 的範例，可讓您記錄到名為 k4a 的檔案，並將會捕捉警告和較高層級的訊息。

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. 從命令提示字元執行案例（例如，啟動檢視器）
4. 流覽至 k4a .log 和 share file。

如需詳細資訊，請參閱下列從標頭檔剪輯：

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

主體追蹤 SDK K4ABT.dll 的記錄類似，不同之處在于使用者應該修改一組不同的環境變數名稱：

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>裝置不在裝置管理員中列舉

- 檢查裝置背後的狀態 LED，如果它閃爍琥珀色，您有 USB 連線問題，而且沒有足夠的耗電量。 電源供應器纜線應該插到提供的電源配接器。 當電源線的 USB 類型為 [已連線] 時，裝置所需的耗電量會比電腦 USB 埠所能提供的更多。 因此，請勿將其連線至電腦埠或 USB 集線器。
- 檢查是否已連接電源線，並使用 USB3 埠來取得資料。
- 請嘗試變更資料連線的 USB3 埠（建議使用靠近主機板的 USB 埠，例如，在電腦的背面）。
- 檢查您的纜線、損毀或較低品質的纜線可能會造成不可靠的列舉（裝置在 [裝置管理員] 中保持「閃爍」
- 如果您已連線到膝上型電腦並以電池執行，它可能會對埠的電源進行節流。
- 重新開機主機電腦。
- 如果問題持續發生，可能會有相容性問題。
- 如果在固件更新期間發生失敗，且裝置本身尚未復原，請執行[原廠重設](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)。

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect Viewer 無法開啟

- 請先檢查您的裝置是否在 Windows Device Manager 中列舉。

    ![Windows 裝置管理員中的 Azure Kinect 攝影機](./media/resources/viewer-fails.png)

- 使用裝置檢查是否有任何其他應用程式（例如，Windows 攝影機應用程式）。 一次只能有一個應用程式可以存取該裝置。
- 檢查 k4aviewer 的錯誤訊息。
- 開啟 Windows 攝影機應用程式，並檢查其運作方式。
- 電源週期裝置，等待串流會導致電源關閉，然後再使用裝置。
- 重新開機主機電腦。
- 請確定您在電腦上使用最新的圖形驅動程式。
- 如果您使用自己的 SDK 組建，請嘗試使用正式發行的版本（如果已解決此問題）。
- 如果問題持續發生，請[收集記錄](troubleshooting.md#collecting-logs)檔和檔案意見反應。

## <a name="cannot-find-microphone"></a>找不到麥克風

- 請先檢查 Device Manager 中列舉的麥克風陣列。
- 如果裝置已列舉且在 Windows 中正常運作，則問題可能是在 [固件更新] 視窗已將不同的容器識別碼指派給深度相機之後。
- 若要重設，請前往 Device Manager，以滑鼠右鍵按一下 [Azure Kinect 麥克風陣列]，然後選取 [卸載裝置]。 完成後，卸離並重新附加感應器。

    ![Azure Kinect Mic 陣列](./media/resources/mic-not-found.png)

- 之後，請重新開機 Azure Kinect Viewer，然後再試一次。

## <a name="device-firmware-update-issues"></a>裝置固件更新問題

- 如果更新後未回報正確的版本號碼，您可能需要為裝置進行電源迴圈。
- 如果固件更新中斷，可能會進入錯誤狀態且無法列舉。 卸離並重新連接裝置，並等候60秒，以查看是否可以復原。
如果不是，則會執行[原廠重設](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>影像品質問題

- 啟動[Azure Kinect viewer](azure-kinect-viewer.md) ，並檢查裝置的位置是否有干擾，或感應器遭到封鎖或鏡頭已變更。
- 如果在特定模式下發生問題，請嘗試不同的作業模式來縮小範圍。
- 若要與小組共用影像品質問題，您可以：

1) 在[Azure Kinect 檢視器](azure-kinect-viewer.md)上進行暫停查看，並拍照或
2) 使用[Azure Kinect](azure-kinect-recorder.md)錄製器進行記錄，例如`k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>不一致或非預期的裝置時間戳記

呼叫 ```k4a_device_set_color_control``` 會暫時引發裝置的時間變更，可能需要幾個捕捉才能穩定。 避免在影像捕捉迴圈中呼叫 API，以避免重設每個新影像的內部計時計算。 請改為在啟動相機之前呼叫 API，或只在需要變更影像捕捉迴圈內的值時才呼叫。 特別避免呼叫 ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` 。

## <a name="usb3-host-controller-compatibility"></a>USB3 主機控制器相容性

如果裝置未在 [裝置管理員] 下進行列舉，可能是因為它插入了不受支援的 USB3 控制器。 

針對 Windows 上的 Azure Kinect DK **，Intel**、**德克薩斯儀器（TI）** 和**Renesas**是*唯一支援的主機控制器*。 Windows 平臺上的 Azure Kinect SDK 依賴整合的容器識別碼，且必須跨越 USB 2.0 和3.0 裝置，才能讓 SDK 找到實際位於相同裝置上的深度、色彩和音訊裝置。 在 Linux 上，可能會支援更多主機控制器，因為該平臺所依賴的容器識別碼較少，而且有更多的裝置序號。 

當電腦上安裝了多個主機控制器時，USB 主機控制器的主題會變得更複雜。 當主機控制器混合時，使用者可能會遇到一些問題，其中有些埠正常工作，而其他埠則無法正常執行。 視埠如何連接到案例而定，您可能會看到所有的 front 埠都有 Azure Kinect 的問題

**Windows：** 若要找出您已開啟的主機控制器 Device Manager

1. 依類型的視圖 > 裝置 
2. 使用 Azure Kinect 連線的選取攝影機->Azure Kinect 4K 攝影機
3. 依連線視圖 > 裝置

![USB 埠疑難排解](./media/resources/usb-troubleshooting.png)

若要進一步瞭解電腦上連接的 USB 埠，請在將 Azure Kinect DK 連接到電腦上的不同 USB 埠時，針對每個 USB 埠重複這些步驟。

## <a name="depth-camera-auto-powers-down"></a>深度相機自動關閉電源

深度相機用來計算影像深度資料的鐳射具有有限的使用期限。 為了充分發揮雷射的生命週期，深度相機會偵測到沒有取用深度資料的時間。 當裝置正在串流處理數分鐘，但主機電腦並未讀取資料時的深度相機電源。 它也會影響多個裝置同步處理，其中從屬裝置會以深度相機正在串流的狀態啟動，而深度框架則會主動協助等待主要裝置開始同步處理捕獲。 若要在多重裝置捕捉案例中避免這個問題，請確定主要裝置會在第一個次要啟動的一分鐘內啟動。 

## <a name="using-body-tracking-sdk-with-unreal"></a>搭配 Unreal 使用本文追蹤 SDK

若要搭配使用本文追蹤 SDK 與 Unreal，請確定您已將加入 `<SDK Installation Path>\tools` 至環境變數， `PATH` 並將 `dnn_model_2_0.onnx` 和複製 `cudnn64_7.dll` 到 `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` 。

## <a name="next-steps"></a>後續步驟

[其他支援資訊](support.md)
