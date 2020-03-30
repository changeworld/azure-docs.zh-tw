---
title: 查看 Azure 地圖 API 使用指標 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何在 Azure 門戶中查看 Microsoft Azure 地圖 API 呼叫的指標。
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335157"
---
# <a name="view-azure-maps-api-usage-metrics"></a>檢視 Azure 地圖服務 API 使用計量

本文演示如何在[Azure 門戶](https://portal.azure.com)中查看 Azure 地圖帳戶的 API 使用指標。 計量是以方便檢視的圖形格式顯示，並包含可自訂的持續時間。

## <a name="view-metric-snapshot"></a>檢視計量快照集

您可以在您地圖服務帳戶的 [概觀]**** 頁面查看一些常見的計量。 它目前顯示可選取之持續時間內的 [要求總數]**、[錯誤總數]** 和 [可用性]**。

![Azure 地圖使用方式指標概述](media/how-to-view-api-usage/portal-overview.png)

如果您需要針對特定分析來自訂這些圖表，請繼續閱讀下一節。

## <a name="view-detailed-metrics"></a>檢視詳細計量

1. 在[入口網站](https://portal.azure.com)中登入您的 Azure 訂用帳戶。

2. 按一下左側的 [所有資源]**** 功能表項目，並瀏覽到您的 [Azure 地圖服務帳戶]**。

3. 您的地圖服務帳戶開啟之後，按一下左邊的 [計量]**** 功能表。

4. 在 **"指標"** 窗格中，選擇以下選項之一：

   1. [可用性]**** - 顯示持續時間內 API 可用性的「平均值」**。
   2. [使用量]**** - 顯示您帳戶的使用量「計數」** 為何。

      ![Azure 地圖使用方式指標窗格](media/how-to-view-api-usage/portal-metrics.png)

5. 接下來，您可以按一下 [過去 24 小時 (自動)]**** 來選取「時間範圍」**。 根據預設，時間範圍會設定為 24 小時。 按一下後，您將看到所有可選擇的時間範圍。 在同一個下拉式功能表中，您也可以選取 [時間細微性]**，並選擇以 [當地]** 或 [GMT]** 顯示時間。 按一下 [套用]****。

    ![Azure 地圖指標時間範圍](media/how-to-view-api-usage/time-range.png)

6. 添加指標後，可以從與該指標相關的屬性**添加篩選器**。 然後，選擇要在圖形上看到的屬性的值。

    ![Azure 地圖使用方式指標篩選器](media/how-to-view-api-usage/filter.png)

7. 您也可以根據所選的計量屬性 [套用分割]****。 它允許將圖形拆分為多個圖形，該屬性的每個值。 在下列圖片中，每個圖表顏色都對應到顯示在圖表底部的屬性值。

    ![Azure 映射使用指標拆分](media/how-to-view-api-usage/splitting.png)

8. 您也可以在同一個圖表上觀察多個計量，只要按一下頂端的 [新增計量]**** 按鈕即可。

## <a name="next-steps"></a>後續步驟

深入了解您要追蹤其使用量的 Azure 地圖服務 API：
> [!div class="nextstepaction"] 
> [Azure 映射 Web SDK 如何](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure 映射 Android SDK 如何](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Azure 地圖服務 REST API 文件](https://docs.microsoft.com/rest/api/maps) \(英文\)
