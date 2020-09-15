---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536241"
---
選取 [組建]  。 在開啟的對話方塊中，選取要匯出 Xcode 專案的目的地資料夾。

匯出完成時，會顯示含有匯出的 Xcode 專案的資料夾。

> [!NOTE]
> 如果出現詢問您是否要取代或附加的視窗，我們建議您選取 [附加]  ，因為速度較快。 如果您是變更場景中的資產，應該只需要選取 [取代]  。 (例如，如果您新增、移除或變更父/子關連性，或者如果您新增、移除或變更屬性。)如果您只是變更原始程式碼，[附加]  應該就夠了。

## <a name="open-the-xcode-project"></a>開啟 Xcode 專案

現在您可以在 Xcode 中開啟 `Unity-iPhone.xcodeproj`。 您可以啟動 Xcode 並開啟匯出的 `Unity-iPhone.xcodeproj` 專案，或從您匯出專案的位置執行下列命令來啟動 Xcode 中的專案：

```bash
open ./Unity-iPhone.xcodeproj
```

選取根 **Unity-iPhone** 節點以檢視專案設定，然後選取 [一般]**** 索引標籤。

在 [簽署]**** 底下，確認 [自動管理簽署]**** 已啟用。 如果未啟用，請啟用它，然後在出現的對話方塊中選取 [啟用自動]**** 以重設組建設定。

在 [部署資訊]**** 底下，確定 [部署目標]**** 已設定為 `11.0`。

## <a name="deploy-the-app-to-your-ios-device"></a>將應用程式部署到您的 iOS 裝置

將 iOS 裝置連接至 Mac，並將 [作用中配置] 設定為您的 iOS 裝置。

![選取裝置](./media/spatial-anchors-unity/select-device.png)

選取 [建置然後執行目前的配置]。

![部署並執行](./media/spatial-anchors-unity/deploy-run.png)
