---
title: Azure Kinect 內文追蹤 SDK 下載
description: 瞭解如何在 Windows 或 Linux 上下載每個版本的 Azure Kinect 感應器 SDK。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure，kinect，sdk，下載更新，最新，可用，安裝，主體，追蹤
ms.openlocfilehash: 0ac0598d893617f341b9e1fd4d45c0c3e3f3c619
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359590"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>下載 Azure Kinect 內文追蹤 SDK

本檔提供安裝每個 Azure Kinect 主體追蹤 SDK 版本的連結。

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect 內文追蹤 SDK 內容

- 使用 Azure Kinect DK 建立主體追蹤應用程式的標頭和程式庫。
- 使用 Azure Kinect DK 之主體追蹤應用程式所需的可轉散發 Dll。
- 範例主體追蹤應用程式。

## <a name="windows-download-links"></a>Windows 下載連結

版本       | 下載
--------------|----------
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Linux 安裝指示

目前，唯一支援的發行版本為 Ubuntu 18.04。 若要要求支援其他發行版本，請參閱[此頁面](https://aka.ms/azurekinectfeedback)。

首先，您必須遵循[這裡](/windows-server/administration/linux-package-repository-for-microsoft-software)的指示，設定 [Microsoft 的套件存放庫](https://packages.microsoft.com/)。

`libk4abt<major>.<minor>-dev` 套件包含要針對 `libk4abt` 建置的標頭和 CMake 檔案。
`libk4abt<major>.<minor>`封裝包含執行相依于和範例檢視器之可執行檔所需的共用物件 `libk4abt` 。

基本教學課程需要 `libk4abt<major>.<minor>-dev` 套件。 若要安裝它，請執行

`sudo apt install libk4abt1.0-dev`

如果命令成功，SDK 就可供使用。

> [!NOTE]
> 安裝 SDK 時，請記住您安裝的路徑。 例如「C:\Program Files\Azure Kinect 內文追蹤 SDK 1.0.0」。 您會在此路徑中的文章中找到參考的範例。
> 本文追蹤範例位於 Azure Kinect 範例存放庫中的內文 [追蹤](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) 範例資料夾中。 您會在這裡找到文章所參考的範例。

## <a name="change-log"></a>變更記錄

### <a name="v101"></a>v. 1.0。1
* [Bug 修正]修正當 SDK 從 Windows build 19025 或更新版本上的路徑載入 onnxruntime.dll 時，SDK 損毀的問題： [連結](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>1.0.0 版
* 功能將 c # 包裝函式新增至 msi 安裝程式。
* [Bug 修正]修正無法正確偵測到頭部旋轉的問題： [連結](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Bug 修正]修正在 Linux 機器上，CPU 使用量最高可達100% 的問題： [連結](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* 示例將兩個範例新增至範例存放庫。 範例1示範如何將主體追蹤結果從深度空間轉換成色彩空間 [連結](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample);範例2示範如何偵測樓層平面 [連結](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

### <a name="v095"></a>v 0.9。5
* 功能C # 支援。 C # 包裝函式會封裝在 nuget 套件中。
* 功能多重追蹤器支援。 允許建立多個追蹤器。 現在使用者可以建立多個追蹤器，以追蹤來自不同 Azure Kinect 裝置的主體。
* 功能CPU 模式的多重執行緒支援。 在 CPU 模式上執行時，將會使用所有核心來最大化速度。
* 功能加入 `gpu_device_id` 至 `k4abt_tracker_configuration_t` 結構。 允許使用者指定不是預設的 GPU 裝置，以執行主體追蹤演算法。
* [Bug 修正/中斷變更]修正聯合名稱中的打字問題。 將聯合名稱從變更 `K4ABT_JOINT_SPINE_NAVAL` 為 `K4ABT_JOINT_SPINE_NAVEL` 。

### <a name="v094"></a>v 0.9。4
* 功能新增手接點支援。 SDK 將為每個手提供三個額外接點的資訊：手、HANDTIP、THUMB。
* 功能為每個偵測到的接點新增預測信賴等級。
* 功能新增 CPU 模式支援。 藉由變更 `cpu_only_mode` 中的值 `k4abt_tracker_configuration_t` ，現在 SDK 可以在 CPU 模式下執行，而不需要使用者擁有強大的圖形配接器。

### <a name="v093"></a>v 0.9。3
* 功能將新的 DNN 模型發佈 dnn_model_2_0 onnx，這可大幅改善主體追蹤的穩定性。
* 功能預設停用時態性平滑。 追蹤的接點將會更具回應性。
* 功能改善主體索引對應的精確度。
* [Bug 修正]修正感應器方向設定不正確 bug。
* [Bug 修正]將 body_index_map 類型從 K4A_IMAGE_FORMAT_CUSTOM 變更為 K4A_IMAGE_FORMAT_CUSTOM8。
* [已知問題]兩個接近的主體可能會合並到單一實例區段。

### <a name="v092"></a>v 0.9。2
* [重大變更]更新以相依于最新的 Azure Kinect 感應器 SDK 1.2.0。
* [API 變更] `k4abt_tracker_create` 函數將開始進行 `k4abt_tracker_configuration_t` 輸入。 
* [API 變更]將 `k4abt_frame_get_timestamp_usec` API 變更為 `k4abt_frame_get_device_timestamp_usec` 更明確且一致的感應器 SDK 1.2.0。
* 功能可讓使用者在建立追蹤器時指定感應器掛接方向，以在不同的角度裝載時達到更精確的主體追蹤結果。
* 功能提供新的 API， `k4abt_tracker_set_temporal_smoothing` 以變更使用者想要套用的時態性平滑處理量。
* 功能新增 c + + 包裝函式 k4abt. .hpp。
* 功能新增版本定義標頭 k4abtversion .h。
* [Bug 修正]修正導致極高 CPU 使用率的 bug。
* [Bug 修正]修正記錄器損毀錯誤。

### <a name="v091"></a>v 0.9。1
* [Bug 修正]修正終結追蹤器時的記憶體遺漏
* [Bug 修正]更好的遺漏相依性錯誤訊息
* [Bug 修正]建立第二個追蹤器實例時失敗，而不會損毀
* [Bug 修正]記錄器環境變數現在可以正常運作
* Linux 支援

### <a name="v090"></a>v 0.9。0

* [重大變更]從 CUDA 10.1) 將 SDK 相依性降級至 CUDA 10.0 (。 ONNX runtime 正式支援最多 CUDA 10.0。
* [重大變更]切換至 ONNX 執行時間，而不是 Tensorflow 執行時間。 減少第一個畫面格啟動時間和記憶體使用量。 它也會減少 SDK 二進位檔大小。
* [API 變更]已重新命名 `k4abt_tracker_queue_capture()` 為 `k4abt_tracker_enqueue_capture()`
* [API 變更]分解 `k4abt_frame_get_body()` 成兩個不同的函數： `k4abt_frame_get_body_skeleton()` 和 `k4abt_frame_get_body_id()` 。 現在您可以查詢主體識別碼，而不需要一律複製整個基本架構結構。
* [API 變更]新增函式  `k4abt_frame_get_timestamp_usec()` ，以簡化使用者查詢主體框架時間戳記的步驟。
* 進一步改善主體追蹤演算法精確度和追蹤可靠性

## <a name="next-steps"></a>後續步驟

- [Azure Kinect DK 總覽](about-azure-kinect-dk.md)

- [設定 Azure Kinect DK](set-up-azure-kinect-dk.md)

- [設定 Azure Kinect 主體追蹤](body-sdk-setup.md)