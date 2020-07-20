---
title: 快速入門 - 設定 Azure Kinect DK
description: 本快速入門將提供如何設定 Azure Kinect DK 硬體的指示
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, 開發工具組, azure dk, 設定, 硬體, 快速, usb, 電源, 檢視器, 感應器, 串流, 設定, SDK, 韌體
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211273"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>快速入門：設定您的 Azure Kinect DK

本快速入門提供如何設定 Azure Kinect DK 的指引。 我們將示範如何測試感應器串流視覺效果，並使用 [Azure Kinect 檢視器](azure-kinect-viewer.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="system-requirements"></a>系統需求

請查看[系統需求](system-requirements.md)，確認您的主機電腦設定符合所有 Azure Kinect DK 最低需求。

## <a name="set-up-hardware"></a>設定硬體

> [!NOTE]
> 請務必先移除相機保護膠片，再使用裝置。

1. 將電源接頭插入裝置背面的電源插孔。 將 USB 電源配接器連接到纜線的另一端，然後將配接器插入電源插座。
2. 將 USB 資料纜線連接到您的裝置，然後連接到您電腦上的 USB 3.0 連接埠。
   >[!NOTE]
   >為獲得最佳結果，請將纜線直接連接到裝置和電腦。 避免在連接中使用延長線或額外的配接器。

3. 確認電源指示器 LED (USB 纜線旁) 為純白色。
  
   裝置電源開啟需要幾秒鐘的時間。 當朝向正面的 LED 串流指示器關閉時，裝置便已準備好可供使用。  

   如需電源指示器 LED 的詳細資訊，請參閱[燈所代表的意思為何？](hardware-specification.md#what-does-the-light-mean)

    ![完整裝置功能](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>下載 SDK

1. 選取連結以[下載 SDK](sensor-sdk-download.md)。
2. 在您的電腦上安裝 SDK。

## <a name="update-firmware"></a>更新韌體

SDK 需要最新版本的裝置韌體，才能正常運作。 若要檢查並更新您的韌體版本，請遵循[更新 Azure Kinect DK 韌體](update-device-firmware.md)中的步驟。

## <a name="verify-that-the-device-streams-data"></a>確認裝置可串流資料

1. 啟動 [Azure Kinect 檢視器](azure-kinect-viewer.md)。 您可以使用下列其中一種方法來啟動此工具：
   - 您可以從命令列或按兩下可執行檔來啟動檢視器。 該檔案 (`k4aviewer.exe`) 位於 SDK 工具的目錄中 (例如 `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`，其中 `X.Y.Z` 是 SDK 的安裝版本)。
   - 您可以從裝置的 [開始]  功能表啟動 [Azure Kinect 檢視器]。
2. 在 Azure Kinect 檢視器中，選取 [開啟裝置]   > [啟動]  。

    ![Azure Kinect 檢視器](./media/quickstarts/viewer.png)

3. 確認此工具會將每個感應器串流視覺化：
   - 深度相機
   - 彩色相機
   - 紅外線相機
   - IMU
   - 麥克風

    ![視覺效果工具](./media/quickstarts/visualization-tool.png)

您已經完成 Azure Kinect DK 設定。 現在，您可以開始開發您的應用程式或整合服務。

如果您有任何問題，請參閱[疑難排解](troubleshooting.md)。

## <a name="see-also"></a>另請參閱

- [Azure Kinect DK 硬體資訊](hardware-specification.md)
- [更新裝置的軔體](update-device-firmware.md)
- 深入了解 [Azure Kinect 檢視器](azure-kinect-viewer.md)

## <a name="next-steps"></a>後續步驟

Azure Kinect DK 準備好且正常運作之後，您也可以了解如何
> [!div class="nextstepaction"]
> [將感應器串流記錄至檔案](record-sensor-streams-file.md)
