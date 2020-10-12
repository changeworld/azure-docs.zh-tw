---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83343349"
---
請使用下列程序，檢視並更新函式應用程式目前使用的執行階段版本。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至函式應用程式。

1. 在 [設定] **Configuration**底下，選擇 [**設定**]。 在 [ **函數執行時間設定** ] 索引標籤中，找出 **執行階段版本**。 請注意特定的執行階段版本。 在下列範例中，版本設定為 `~3`。

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="查看執行階段版本。" border="true":::

1. 若要將函式應用程式釘選至 1.x 版執行階段，請在 [執行階段版本]**** 下方選擇 [~1]****。 應用程式中有函式時，此開關會停用。

1. 當您變更執行階段版本時，請回到 [概觀]**** 索引標籤，然後選擇 [重新啟動]**** 來重新啟動應用程式。  函式應用程式會重新開始在 1.x 版執行階段上執行，且當您建立函式時，所使用的會是 1.x 版範本。

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="查看執行階段版本。" border="true":::
