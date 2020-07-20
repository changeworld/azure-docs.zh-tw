---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "72933491"
---
選取 [組建]  。 在開啟的對話方塊中，選取要匯出 Xcode 專案的目的地資料夾。

匯出完成時，會顯示含有匯出的 Xcode 專案的資料夾。

> [!NOTE]
> 如果出現詢問您是否要取代或附加的視窗，我們建議您選取 [附加]  ，因為速度較快。 如果您是變更場景中的資產，應該只需要選取 [取代]  。 (例如，如果您新增、移除或變更父/子關連性，或者如果您新增、移除或變更屬性。)如果您只是變更原始程式碼，[附加]  應該就夠了。

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>將 Xcode 專案轉換為包含 Azure Spatial Anchors 參考的 xcworkspace

在匯出的 Xcode 專案資料夾中，在終端機執行此命令以安裝專案所需的 CocoaPods：

```bash
pod install --repo-update
```

現在您可以開啟 `Unity-iPhone.xcworkspace`，以在 Xcode 中開啟專案：

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> 如果您在升級至 macOS Catalina (10.15) 之後遇到 CocoaPod 問題，請參閱[這裡](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015)的疑難排解步驟。

選取根 **Unity-iPhone** 節點以檢視專案設定，然後選取 [一般]  索引標籤。

在 [簽署]  底下，確認 [自動管理簽署]  已啟用。 如果未啟用，請啟用它，然後在出現的對話方塊中選取 [啟用自動]  以重設組建設定。

在 [部署資訊]  底下，確定 [部署目標]  已設定為 `11.0`。

### <a name="deploy-the-app-to-your-ios-device"></a>將應用程式部署到您的 iOS 裝置

將 iOS 裝置連接至 Mac，並將 [作用中配置]  設定為您的 iOS 裝置。

![選取裝置](./media/spatial-anchors-unity/select-device.png)

選取 [建置然後執行目前的配置]  。

![部署並執行](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> 如果您看到 `library not found for -lPods-Unity-iPhone` 錯誤，您可能開到 `.xcodeproj` 檔案，而不是 `.xcworkspace` 檔案。
