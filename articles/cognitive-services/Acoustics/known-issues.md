---
title: 專案聲學外掛程式已知問題
titlesuffix: Azure Cognitive Services
description: 在"專案聲學"中，您可能會遇到以下已知問題。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243030"
---
# <a name="project-acoustics-known-issues"></a>專案聲學已知問題
本文介紹了在使用"專案聲學"時可能會遇到的問題。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>將場景重新命名時遺失聲場參數

如果重命名場景，則屬於該場景的所有聲學參數不會自動傳輸到新場景。 但它們仍然存在於舊資產檔中。 在場景檔旁邊的*編輯器*目錄中查找 *[SceneName] _AcousticParameters.資產*檔。 重命名檔以反映新的場景名稱。

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>從某些 Unity 版本部署到安卓錯誤

Unity 的某些版本在如何將音訊外掛程式部署到 Android 方面存在[漏洞](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)。 請確保您沒有使用受此 Bug 影響的版本。

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"找不到元資料檔案系統.安全.dll"錯誤

請確保**播放機**設置中的**腳本執行階段版本**為 *.NET 4.x 等效*版本，然後重新開機 Unity。

## <a name="authentication-problems-when-connecting-to-azure"></a>連接到 Azure 時的身份驗證問題

請檢查︰
- 對於 Azure 帳戶，您使用了正確的憑據。
- 您的帳戶支援您在烘焙中請求的節點類型。
- 系統時鐘設置正確。

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>取消後，"烘焙"選項卡仍顯示"刪除"
專案聲學在成功完成或取消後清理作業的所有 Azure 資源。 此過程最多可能需要 5 分鐘。

## <a name="next-steps"></a>後續步驟
* 嘗試[統一](unity-quickstart.md)或[虛幻](unreal-quickstart.md)示例內容。
