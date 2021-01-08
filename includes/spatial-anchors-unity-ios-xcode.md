---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 81d2804d99896200ea6f68592ea168112e172c20
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762591"
---
選取 [組建]  。 在開啟的窗格上，選取要將 Xcode 專案匯出至其中的資料夾。

   匯出完成時，包含所匯出 Xcode 專案的資料夾便會出現。

   > [!NOTE]
   > 如果出現一個視窗，詢問您要取代還是附加，我們建議您選取 [附加]，因為速度較快。 只在您是變更場景中的資產時，才應該選取 [取代]。 例如，您可能正在新增、移除或變更父/子關聯性，或者您可能正在新增、移除或變更屬性。 如果您只是變更原始程式碼，[附加] 應該就夠了。

## <a name="open-the-xcode-project"></a>開啟 Xcode 專案

現在您可以在 Xcode 中開啟 `Unity-iPhone.xcodeproj` 專案。 

您可以啟動 Xcode 並開啟匯出的 `Unity-iPhone.xcodeproj` 專案，或從您匯出專案的位置執行下列命令來啟動 Xcode 中的專案：

 ```bash
open ./Unity-iPhone.xcodeproj
```

選取根 **Unity-iPhone** 節點以檢視專案設定，然後選取 [一般] 索引標籤。

在 [部署資訊] 底下，確定部署目標設定為 **iOS 11.0**。

在 [簽署與功能] 索引標籤底下，確定 [自動管理簽署] 已啟用。 如果未啟用，請啟用它，然後在出現的窗格上選取 [啟用自動] 以重設組建設定。

## <a name="deploy-the-app-to-your-ios-device"></a>將應用程式部署到您的 iOS 裝置

將 iOS 裝置連線至 Mac，並將 [作用中配置] 設定為您的 iOS 裝置。

   ![[我的 iPhone] 按鈕的螢幕擷取畫面，此按鈕用於選取裝置。](./media/spatial-anchors-unity/select-device.png)

選取 [建置然後執行目前的配置]。

   ![[部署並執行] 箭號按鈕的螢幕擷取畫面。](./media/spatial-anchors-unity/deploy-run.png)
