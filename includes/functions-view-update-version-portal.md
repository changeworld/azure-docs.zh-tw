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
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174628"
---
請使用下列程序，檢視並更新函式應用程式目前使用的執行階段版本。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至函式應用程式。

1. 在 [設定的功能]**** 下，選擇 [函式應用程式設定]****。

    ![選取函數應用程式設定](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. 在 [函數應用程式設定]**** 索引標籤上，找到 [執行階段版本]****。 請注意特定的執行階段版本和要求的主要版本。 在下列範例中，版本設定為 `~2`。

   ![選取函數應用程式設定](./media/functions-view-update-version-portal/function-app-view-version.png)

1. 若要將函式應用程式釘選至 1.x 版執行階段，請在 [執行階段版本]**** 下方選擇 [~1]****。 應用程式中有函式時，此開關會停用。

1. 當您變更執行階段版本時，請回到 [概觀]**** 索引標籤，然後選擇 [重新啟動]**** 來重新啟動應用程式。  函式應用程式會重新開始在 1.x 版執行階段上執行，且當您建立函式時，所使用的會是 1.x 版範本。