---
title: 同步處理多個 Azure Kinect DK 裝置
description: 本文將探討多重裝置同步處理的優點，以及如何設定要同步處理的裝置。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azure，kinect，規格，硬體，深色，功能，深度，色彩，RGB，IMU，陣列，深度，多，同步處理
ms.openlocfilehash: e22f42a69e7d9b8283ec2f2da478dde0c27ce4ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276540"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>同步處理多個 Azure Kinect DK 裝置

每個 Azure Kinect DK 裝置都包含 3.5 mm 同步處理埠（同步處理和**同步**處理），您可以用來將多個裝置連結**在**一起。 連接裝置之後，您的軟體可以協調它們之間的觸發程式時間。 

本文說明如何連接和同步處理裝置。

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>使用多個 Azure Kinect DK 裝置的優點

使用多個 Azure Kinect DK 裝置的原因有很多，包括下列各項：

- 填入遮蔽。 雖然 Azure Kinect DK 的資料轉換會產生單一影像，但這兩個相機（深度和 RGB）實際上是相隔一小的距離。 位移可能會導致遮蔽。 當前景物件針對裝置上的兩個相機之一封鎖部分背景物件的視圖時，就會發生遮蔽。 在產生的色彩影像中，前景物件似乎會在背景物件上轉換陰影。  
   例如，在下圖中，左側相機會看到灰色圖元「P2」。 不過，白色前景物件會封鎖右端相機 IR 橫樑。 右側攝影機沒有 "P2" 的資料。  
   ![遮蔽](./media/occlusion.png)  
   其他已同步處理的裝置可以提供 pixels occluded 資料。
- 以三個維度掃描物件。
- 將有效的畫面播放速率增加為大於每秒30個畫面格的值（FPS）。
- 捕捉相同場景的多個4K 色彩影像，全都在曝光中心的100毫秒內對齊 &mu; 。
- 增加空間內的相機涵蓋範圍。

## <a name="plan-your-multi-device-configuration"></a>規劃多重裝置設定

開始之前，請確定您已複習[Azure Kinect DK 硬體規格](hardware-specification.md)和[Azure Kinect DK 深度攝影機](depth-camera.md)。

### <a name="select-a-device-configuration"></a>選取裝置設定

您可以使用下列其中一種方法來進行裝置設定：

- **菊輪鍊**設定。 同步處理一個主要裝置和最多八個從屬裝置。  
   ![此圖顯示如何在菊輪鍊設定中連接 Azure Kinect DK 裝置。](./media/multicam-sync-daisychain.png)
- **星星**設定。 同步處理一個主要裝置和最多兩個從屬裝置。  
   ![此圖顯示如何在星形設定中設定多個 Azure 深色裝置。](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>使用外部同步觸發程式

在這兩種設定中，主要裝置會提供從屬裝置的觸發信號。 不過，您可以使用自訂外部來源來進行同步處理觸發程式。 例如，您可以使用此選項，將影像捕捉與其他設備同步處理。 在菊輪鍊設定或星型設定中，外部觸發程式來源會連線到主要裝置。

您的外部觸發程式來源必須以與主要裝置相同的方式運作。 它必須提供具有下列特性的同步信號：

- 活躍高
- 脈衝寬度：大於 8 &mu; 秒
- 5V TTL/CMOS
- 駕駛容量上限：不小於 8 milliamps （mA）
- 頻率支援：剛好 30 FPS、15 FPS 和 5 FPS （彩色相機主要 VSYNC 信號的頻率）

觸發程式來源必須使用 3.5-mm 音訊纜線，將信號傳遞至主要裝置**在埠中的同步**處理。 您可以使用身歷聲或 mono 纜線。 Azure Kinect DK 將音訊纜線連接器的所有卷起袖子和環形，加總起來。 如下圖所示，裝置只會接收來自連接器提示的同步信號。

![外部觸發程式信號的纜線設定](./media/resources/camera-trigger-signal.jpg)

如需有關如何使用外部設備的詳細資訊，請參閱搭配[使用 Azure Kinect 錄製程式與外部同步處理的裝置](record-external-synchronized-units.md)

### <a name="plan-your-camera-settings-and-software-configuration"></a>規劃您的相機設定和軟體配置

如需有關如何設定軟體以控制相機並使用影像資料的詳細資訊，請參閱[Azure Kinect 感應器 SDK](about-sensor-sdk.md)。

本節討論影響已同步處理之裝置（但不是單一裝置）的幾個因素。 您的軟體應該考慮這些因素。

#### <a name="exposure-considerations"></a>曝光考慮
如果您想要控制每個裝置的精確時間，建議您使用手動曝光設定。 在 [自動曝光] 設定下，每個彩色相機都可以動態變更實際的曝光。 因為公開會影響時間，所以這類變更很快就會將攝影機推播不同步。

在映射捕獲迴圈中，避免重複設定相同的曝光設定。 只在需要時呼叫 API 一次。

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>避免多個深度攝影機之間的干擾

當多個深度相機是影像重迭的欄位時，每張攝影機必須影像自己的關聯鐳射。 為避免雷射彼此干擾，相機捕捉應彼此之間的位移，160μs 或更多。

針對每個深度相機捕捉，鐳射會開啟九次，每次僅限 125 &mu; 秒。 然後，鐳射會 &mu; 在 14505 s 或 23905 s 閒置 &mu; ，視作業模式而定。 這個行為表示位移計算的起點是 125 &mu; s。

此外，相機時鐘與裝置固件時鐘之間的差異會增加160秒的最小位移 &mu; 。 若要計算更精確的設定位移，請注意您所使用的深度模式，並參考[深度感應器原始計時表](hardware-specification.md#depth-sensor-raw-timing)。 藉由使用此資料表中的資料，您可以使用下列方程式來計算最小位移（每張攝影機的曝光時間）：

> *曝光時間*= （*IR 脈衝* &times; *脈衝寬度*） + （*閒置期間* &times; *閒置時間*）

當您使用160秒的位移時 &mu; ，可以設定最多9個額外的深度相機，讓每個鐳射在其他雷射閒置時開啟。

在您的軟體中，使用 ```depth_delay_off_color_usec``` 或 ```subordinate_delay_off_master_usec``` 來確定每個 IR 鐳射會在其本身的 160 s 視窗中引發， &mu; 或具有不同的視圖欄位。

## <a name="prepare-your-devices-and-other-hardware"></a>準備您的裝置和其他硬體

除了多部 Azure Kinect DK 裝置，您可能必須取得額外的主機電腦和其他硬體，才能支援您想要建立的設定。 在您開始設定之前，請使用本節中的資訊來確定所有裝置和硬體都已就緒。

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK 裝置

針對您想要同步處理的每個 Azure Kinect DK 裝置，執行下列動作：

- 請確定裝置上已安裝最新的固件。 如需如何更新裝置的詳細資訊，請移至[更新 Azure Kinect DK 固件](update-device-firmware.md)。 
- 移除裝置封面以顯示同步埠。
- 請記下每個裝置的序號。 您稍後會在安裝程式中使用此數位。

### <a name="host-computers"></a>主機電腦

一般來說，每個 Azure Kinect DK 都會使用自己的主機電腦。 您可以使用專用的主機控制器，視您使用裝置的方式，以及透過 USB 連線傳送的資料量而定。 

請確定每部主機電腦上已安裝 Azure Kinect 感應器 SDK。 如需有關如何安裝感應器 SDK 的詳細資訊，請移至[快速入門：設定您的 Azure Kinect DK](set-up-azure-kinect-dk.md)。 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux 電腦： Ubuntu 上的 USB 記憶體

根據預設，以 Linux 為基礎的主機電腦只會配置 USB 控制器 16 MB 的核心記憶體來處理 USB 傳輸。 此數量通常足以支援單一 Azure Kinect DK。 不過，若要支援多個裝置，USB 控制器必須有更多的記憶體。 若要增加記憶體，請遵循下列步驟：

1. 編輯/**等/預設/grub**。
1. 尋找下列程式碼行：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   使用下面這一行來取代它：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > 這些命令會將 USB 記憶體設定為 32 MB。 這是預設值兩倍的範例設定。 您可以設定更大的值，適合您的解決方案。
1. 執行**sudo update-grub**。
1. 重新啟動電腦。

### <a name="cables"></a>纜線

若要將裝置連線到另一部電腦，並將它們連接到主機電腦，您必須使用 3.5-mm 男性-雌雄纜線（也稱為 3.5-mm 音訊纜線）。 纜線長度應小於10個計量，而且可以是身歷聲或 mono。

您必須擁有的纜線數目取決於您所使用的裝置數目，以及特定的裝置設定。 Azure Kinect DK 方塊不包含纜線。 您必須分別購買。

如果您在星星設定中連接裝置，則也必須有一個耳機分隔器。

## <a name="connect-your-devices"></a>連線您的裝置

**在菊輪鍊設定中連接 Azure Kinect DK 裝置**

1. 將每個 Azure Kinect DK 連接到電源。
1. 將每部裝置連線到其主機電腦。 
1. 選取一個裝置作為主要裝置，並將 3.5-mm 音訊纜線插入其 [**同步輸出**] 埠。
1. 將纜線的另一端插入第一個從屬裝置的 [**同步**處理] 埠。
1. 若要連接另一部裝置，請將另一個纜線插入第一個從屬裝置的 [**同步**處理] 埠，然後進入下一個裝置的 [**同步**處理] 埠。
1. 重複上一個步驟，直到所有裝置都連線為止。 最後一部裝置應該只有一條纜線連接。 其**同步輸出**埠應該是空的。

**以星星設定連接 Azure Kinect DK 裝置**

1. 將每個 Azure Kinect DK 連接到電源。
1. 將每部裝置連線到其主機電腦。 
1. 選取一個裝置作為主要裝置，並將耳機分隔器的單一端點插入其 [**同步輸出**] 埠。
1. 將 3.5-mm 音訊纜線連接到耳機分隔器的「分割」端點。
1. 將每個纜線的另一端插入其中一個從屬裝置的 [**同步**處理] 埠。

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>確認裝置已連線且正在進行通訊

若要確認裝置已正確連線，請使用[Azure Kinect Viewer](azure-kinect-viewer.md)。 視需要重複此程式，以測試每個次級裝置與主要裝置的組合

> [!IMPORTANT]  
> 在此程式中，您必須知道每個 Azure Kinect DK 的序號。

1. 開啟兩個 Azure Kinect Viewer 實例。
1. 在 [**開啟裝置**] 下，選取您要測試的次級裝置序號。  
   ![開啟裝置](./media/open-devices.png)
   > [!IMPORTANT]  
   > 若要取得所有裝置之間的精確影像捕捉對齊，您必須最後啟動主要裝置。  
1. 在 [**外部同步**] 底下，選取 [ **Sub**]。  
   ![附屬相機啟動](./media/sub-device-start.png)
1.  選取 [開始]。  
    > [!NOTE]  
    > 因為這是從屬裝置，所以 Azure Kinect Viewer 在裝置啟動後不會顯示影像。 在次級裝置收到來自主要裝置的同步信號之前，不會顯示任何影像。
1. 次級裝置啟動之後，請使用 Azure Kinect Viewer 的其他實例來開啟主要裝置。
1. 在 [**外部同步**] 底下，選取 [ **Master**]。
1. 選取 [開始]。

當主要 Azure Kinect 裝置啟動時，Azure Kinect 檢視器的兩個實例應該會顯示影像。

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>以同步處理的集合形式校準裝置

確認裝置正確通訊之後，您就可以將它們校準，以在單一網域中產生映射。

在單一裝置中，深度和 RGB 相機會進行工廠校正，以搭配使用。 不過，當有多個裝置必須搭配使用時，必須加以校正，才能判斷如何將影像從拍攝相機的所在網域轉換成您想要用來處理影像的相機網域。

交叉校準裝置有多個選項。 Microsoft 提供[GitHub 綠色螢幕程式碼範例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)，其使用 OpenCV 方法。 此程式碼範例的讀我檔案會提供更多詳細資料和校準裝置的指示。

如需有關校正的詳細資訊，請參閱[使用 Azure Kinect 校正功能](use-calibration-functions.md)。

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
