---
title: 查看或刪除您的資料-自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 您可以完全掌控您的資料。 本文說明如何在自訂視覺服務中查看、匯出或刪除您的資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527389"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>在自訂視覺中查看或刪除使用者資料

自訂視覺會收集使用者資料來操作服務，但客戶可以完全掌控使用自訂視覺 [訓練 api](https://go.microsoft.com/fwlink/?linkid=865446)來查看和刪除其資料。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

若要瞭解如何在自訂視覺中查看和刪除使用者資料，請參閱下表。

| 資料 | 查看作業 | 刪除動作 |
| ---- | ---------------- | ---------------- |
| 帳戶資訊 (訂用帳戶金鑰)  | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | 使用 Azure 入口網站刪除 (Azure 訂用帳戶)。 或使用 [CustomVision.ai 設定] 頁面中的 [刪除您的帳戶] 按鈕 (Microsoft 帳戶訂閱)  | 
| 反覆項目詳細資料 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 反復專案效能詳細資料 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 反覆項目清單 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 專案和專案詳細資料 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 映像標籤 | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 影像 | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (提供下載影像的 URI) 和 [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (提供下載影像的 URI) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 匯出的反覆運算 | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | 刪除帳戶時刪除 |
