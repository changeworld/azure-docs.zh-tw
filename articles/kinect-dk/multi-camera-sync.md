---
title: 同步處理多個 Azure Kinect DK 裝置
description: 本文將探討多重裝置同步處理的優點，以及如何設定要同步處理的裝置。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azure、kinect、規格、硬體、深色、功能、深度、色彩、RGB、IMU、陣列、深度、多重、同步處理
ms.openlocfilehash: 7c79101de5e5455ae2ff9fd8b5d8369a3832631c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361155"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>同步處理多個 Azure Kinect DK 裝置

每個 Azure Kinect DK 裝置都包含 3.5 mm 同步處理埠 (**同步** 處理，並將您可以用來連結多個裝置的) **同步** 處理。 連接裝置之後，您的軟體可以協調它們之間的觸發時間。 

本文說明如何連線和同步處理裝置。

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>使用多個 Azure Kinect DK 裝置的優點

使用多個 Azure Kinect DK 裝置有許多原因，包括下列各項：

- 填寫遮蔽。 雖然 Azure Kinect DK 的資料轉換會產生單一影像，但 (深度和 RGB) 的兩張相機其實遠距離較小。 位移可讓遮蔽的可能性。 當前景物件針對裝置上的兩個相機之一封鎖背景物件部分的視圖時，就會發生遮蔽。 在產生的色彩影像中，前景物件似乎會在背景物件上轉換陰影。  
   例如，在下圖中，左側相機會看到灰色圖元 "P2"。 不過，白色前景物件會封鎖右手邊的相機 IR 橫樑。 右側相機沒有 "P2" 的資料。  
   ![圖所示，兩個相機會以封鎖的方式出現在相同的位置。](./media/occlusion.png)  
   其他已同步處理的裝置可以提供 pixels occluded 的資料。
- 掃描三個維度中的物件。
- 將有效的畫面播放速率增加到大於每秒30個畫面格 (FPS) 的值。
- 捕捉相同場景的多個4K 色彩影像，全部對齊100微秒內的 (&mu;) 公開的中心。
- 增加空間內的相機涵蓋範圍。

## <a name="plan-your-multi-device-configuration"></a>規劃您的多重裝置設定

開始之前，請確定您已複習 [Azure Kinect DK 硬體規格](hardware-specification.md) 和 [Azure Kinect DK 深度攝影機](depth-camera.md)。

### <a name="select-a-device-configuration"></a>選取裝置設定

您可以使用下列其中一種方法來進行裝置設定：

- **菊輪鍊**設定。 同步處理一部主要裝置和最多八個從屬裝置。  
   ![此圖顯示如何連接菊輪鍊設定中 Azure Kinect DK 裝置。](./media/multicam-sync-daisychain.png)
- **星星**設定。 同步處理一部主要裝置和最多兩個次級裝置。  
   ![此圖顯示如何設定星狀設定中的多個 Azure 深色裝置。](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>使用外部同步觸發程式

在這兩種設定中，主要裝置會為次級裝置提供觸發信號。 不過，您可以使用自訂的外部來源進行同步處理觸發程式。 例如，您可以使用此選項來同步處理影像捕捉與其他設備。 在菊輪鍊設定或星星設定中，外部觸發程式來源會連接到主要裝置。

外部觸發程式來源的運作方式必須與主要裝置相同。 它必須傳遞具有下列特性的同步信號：

- 主動高
- 脈衝寬度：大於 8 &mu; 秒
- 5V TTL/CMOS
- 驅動容量上限： milliamps (mA) 不小於8個
- 頻率支援：大約 30 FPS、15 FPS 和 5 FPS (彩色攝影機主機 VSYNC 信號的頻率) 

觸發程式來源必須使用 3.5-mm 音訊纜線，將信號傳遞至主要裝置 **同步** 處理的埠。 您可以使用身歷聲或 mono 纜線。 Azure Kinect DK 將音訊纜線連接器的所有卷起袖子和環形合在一起，並將其加總。 如下圖所示，裝置只會收到來自連接器提示的同步信號。

![外部觸發程式信號的纜線設定](./media/resources/camera-trigger-signal.jpg)

如需如何使用外部設備的詳細資訊，請參閱 [使用 Azure Kinect 錄製器搭配外部同步處理的裝置](record-external-synchronized-units.md)

### <a name="plan-your-camera-settings-and-software-configuration"></a>規劃您的相機設定和軟體設定

如需有關如何設定軟體以控制攝影機和使用影像資料的詳細資訊，請參閱 [Azure Kinect 感應器 SDK](about-sensor-sdk.md)。

本節將討論幾個影響同步裝置的因素， (但不) 單一裝置。 您的軟體應考慮這些因素。

#### <a name="exposure-considerations"></a>公開考慮
如果您想要控制每個裝置的精確時間，建議您使用手動曝光設定。 在自動曝光設定下，每個彩色相機都可以動態變更實際的曝光。 因為曝光會影響時間，所以這類變更會快速地將攝影機推入不同步。

在映射捕獲迴圈中，請避免重複設定相同的曝光設定。 只在需要時呼叫 API 一次。

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>避免多個深度相機之間的干擾

當多個深度攝影機重製作重迭顯示欄位的影像時，每張攝影機都必須影像自己的關聯鐳射。 為了防止雷射互相干擾，相機捕捉應該會由160μs 或更多的方式彼此位移。

針對每個深度相機捕捉，鐳射會開啟九次，而且每次只會在 125 &mu; 秒使用。 然後，會根據作業模式，讓鐳射閒置 14505 &mu; s 或 23905 &mu; s。 此行為表示位移計算的起點是 125 &mu; s。

此外，相機時鐘和裝置固件時鐘之間的差異會增加到160秒的最小位移 &mu; 。 若要計算設定的更精確位移，請注意您所使用的深度模式，並參考 [深度感應器原始時間資料表](hardware-specification.md#depth-sensor-raw-timing)。 藉由使用此資料表中的資料，您可以使用下列方程式來計算每個相機) 的曝光時間最小位移 (：

> *曝光時間* = (*IR 脈衝* &times; *脈衝寬度*) + (*閒置期間* &times; *閒置時間*) 

當您使用160秒的位移時 &mu; ，最多可以設定9個額外的深度相機，讓每個鐳射在其他雷射閒置時開啟。

在您的軟體中，請使用 ```depth_delay_off_color_usec``` 或， ```subordinate_delay_off_master_usec``` 以確定每個 IR 鐳射都在其本身的 160 s 視窗中引發， &mu; 或有不同的視圖欄位。

## <a name="prepare-your-devices-and-other-hardware"></a>準備您的裝置和其他硬體

除了多部 Azure Kinect DK 裝置之外，您可能必須取得額外的主機電腦和其他硬體，才能支援您要建立的設定。 使用本節中的資訊，確保所有裝置和硬體都已就緒，然後再開始設定。

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK 裝置

針對您要同步處理的每個 Azure Kinect DK 裝置，請執行下列動作：

- 請確定裝置上已安裝最新的固件。 如需如何更新裝置的詳細資訊，請移至 [更新 Azure Kinect DK 固件](update-device-firmware.md)。 
- 移除裝置封面以顯示同步埠。
- 請記下每個裝置的序號。 您稍後會在安裝程式中使用此號碼。

### <a name="host-computers"></a>主機電腦

一般而言，每個 Azure Kinect DK 都會使用自己的主機電腦。 您可以根據裝置的使用方式，以及透過 USB 連線傳輸的資料量，來使用專用主機控制器。 

請確定每部主機電腦上都已安裝 Azure Kinect 感應器 SDK。 如需有關如何安裝感應器 SDK 的詳細資訊，請移至 [快速入門：設定您的 Azure Kinect DK](set-up-azure-kinect-dk.md)。 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux 電腦： Ubuntu 上的 USB 記憶體

根據預設，以 Linux 為基礎的主機電腦會配置 USB 控制器，但只有 16 MB 的核心記憶體可處理 USB 傳輸。 此數量通常足以支援單一 Azure Kinect DK。 不過，為了支援多個裝置，USB 控制器必須有更多的記憶體。 若要增加記憶體，請遵循下列步驟：

1. 編輯/**etc/default/grub**。
1. 尋找下列程式碼行：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   使用這一行來取代它：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > 這些命令會將 USB 記憶體設定為 32 MB。 這是預設值兩倍的範例設定。 您可以為您的方案設定更大的值。
1. 執行 **sudo update-grub**。
1. 將電腦重新開機。

### <a name="cables"></a>纜線

若要將裝置連線到另一部裝置，以及連接到主機電腦，您必須使用 3.5-mm 男性至男性纜線 (也稱為 3.5-mm 音訊纜線) 。 纜線的長度應該小於10個計量，而且可以是身歷聲或 mono。

您必須擁有的纜線數目取決於您使用的裝置數目，以及特定的裝置設定。 Azure Kinect DK box 不包含纜線。 您必須另外購買。

如果您在星星設定中連接裝置，您也必須有一個耳機分隔器。

## <a name="connect-your-devices"></a>連線您的裝置

**連接菊輪鍊設定中的 Azure Kinect DK 裝置**

1. 將每個 Azure Kinect DK 連接到電源。
1. 將每部裝置連接到其主機電腦。 
1. 選取一部裝置作為主要裝置，然後將 3.5-mm 音訊纜線插入其 **同步輸出** 埠。
1. 將纜線的另一端插入第一個從屬裝置的「同步處理」埠 **中** 。
1. 若要連線到另一個裝置，請將另一個纜線插入第一個從屬裝置的「 **同步輸出** 」埠，然後進入下一個裝置的「 **同步** 處理」埠。
1. 重複前一個步驟，直到所有裝置都連線為止。 最後一個裝置應該只有一個纜線連接。 其 **同步輸出** 埠應為空白。

**連接星星設定中的 Azure Kinect DK 裝置**

1. 將每個 Azure Kinect DK 連接到電源。
1. 將每部裝置連接到其主機電腦。 
1. 選取一部要作為主要裝置的裝置，並將耳機分隔器的單一一端插入其 **同步輸出** 埠。
1. 將 3.5-mm 音訊纜線連接到耳機分隔器的「分割」端點。
1. 將每個纜線的另一端插入其中一個從屬裝置的 **同步處理中** 。

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>確認裝置已連線且正在進行通訊

若要確認裝置是否已正確連線，請使用 [Azure Kinect 檢視器](azure-kinect-viewer.md)。 視需要重複此程式，以搭配主要裝置來測試每個次級裝置

> [!IMPORTANT]  
> 針對此程式，您必須知道每個 Azure Kinect DK 的序號。

1. 開啟兩個 Azure Kinect 檢視器實例。
1. 在 [ **開啟裝置**] 下，選取您要測試的次級裝置序號。  
   ![開啟裝置](./media/open-devices.png)
   > [!IMPORTANT]  
   > 若要取得所有裝置之間的精確影像捕捉對齊，您必須先啟動主要裝置。  
1. 在 [ **外部同步**] 底下，選取 [ **Sub**]。  
   ![附屬相機開始](./media/sub-device-start.png)
1.  選取 [開始]。  
    > [!NOTE]  
    > 因為這是從屬裝置，所以在裝置啟動後，Azure Kinect 檢視器不會顯示影像。 在從屬裝置收到來自主要裝置的同步信號之前，不會顯示任何影像。
1. 在次級裝置啟動之後，請使用 Azure Kinect 檢視器的另一個實例來開啟主要裝置。
1. 在 [ **外部同步**] 底下，選取 [ **Master**]。
1. 選取 [開始]。

當主要 Azure Kinect 裝置啟動時，Azure Kinect 檢視器的兩個實例都應該會顯示影像。

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>將裝置校準為同步處理的集合

在確認裝置是否正確通訊之後，您就可以將它們校正以在單一網域中產生映射。

在單一裝置中，深度和 RGB 相機會以原廠校正，以搭配使用。 不過，當多個裝置必須一起運作時，必須先校正它們，才能判斷如何將影像從相機的網域轉換成您想要用來處理影像的相機網域。

交叉校準裝置有多種選項。 Microsoft 提供 [GitHub 綠螢幕程式碼範例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)，其使用 OpenCV 方法。 此程式碼範例的讀我檔案會提供更多詳細資料，以及用於校正裝置的指示。

如需有關校正的詳細資訊，請參閱 [使用 Azure Kinect 校正功能](use-calibration-functions.md)。

## <a name="next-steps"></a>後續步驟

設定同步處理的裝置之後，您也可以瞭解如何使用
> [!div class="nextstepaction"]
> [Azure Kinect 感應器 SDK 記錄和播放 API](record-playback-api.md)

## <a name="related-topics"></a>相關主題

- [關於 Azure Kinect 感應器 SDK](about-sensor-sdk.md)
- [Azure Kinect DK 硬體規格](hardware-specification.md) 
- [快速入門：設定您的 Azure Kinect DK](set-up-azure-kinect-dk.md) 
- [更新 Azure Kinect DK 固件](update-device-firmware.md) 
- [重設 Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Azure Kinect 檢視器](azure-kinect-viewer.md) 
