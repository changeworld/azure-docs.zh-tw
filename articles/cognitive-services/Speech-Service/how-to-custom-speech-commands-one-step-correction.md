---
title: 在自訂命令預覽中新增單步驟更正-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何在自訂命令預覽應用程式中新增命令的單步驟更正。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310422"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>在自訂命令預覽應用程式的自訂命令中新增單步驟更正

在本文中，您將瞭解如何在自訂命令預覽應用程式的命令中新增一個步驟確認。

單步驟修正是用來更新剛完成的命令。 當您將單步驟更正新增至警示命令時，可以變更您的想法並更新警示的時間。 例如：

- 輸入：為明天的中午設定鬧鐘
- 輸出：正常，已為 2020-05-02 12:00:00 設定鬧鐘
- 輸入：否，明天下午
- 輸出：正常

> [!NOTE]
> 在真實世界的案例中，用戶端會執行動作做為命令完成的結果。 本文假設您有一個機制可更新後端應用程式中的鬧鐘。

## <a name="prerequisites"></a>Prerequisites

完成下列文章中的步驟：
> [!div class="checklist"]

> * [快速入門：建立自訂命令預覽應用程式](./quickstart-custom-speech-commands-create-new.md)
> * [快速入門：使用參數來建立自訂命令預覽應用程式](./quickstart-custom-speech-commands-create-parameters.md)
> * [如何：在自訂命令預覽應用程式中將確認新增至命令](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>新增單步驟更正的互動規則

若要示範單步驟更正，請擴充您在[如何：在自訂命令預覽中將確認新增至命令](./how-to-custom-speech-commands-confirmations.md)中所建立的**SetAlarm**命令。

1. 新增互動規則以更新您的**SetAlarm**命令。

    此規則會要求使用者確認警示的日期和時間，並預期會進行確認（是/否）以進行下一回合。

   | 設定               | 建議的值                                                  | 描述                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **規則名稱**             | **更新先前的警示**                                            | 描述規則用途的名稱          |
   | **條件**            | **上一個命令必須更新 & 必要參數-> DateTime**                | 判斷規則何時可執行檔條件    |   
   | **動作**               | **傳送語音回應-> 簡單編輯器-> 將先前的警示更新為 {DateTime}**      | 當規則條件為 true 時所要採取的動作 |
   | **執行後狀態** | **命令已完成**        | 回合後的使用者狀態                   |

1. 在窗格中，選取您剛才建立的互動規則。 選取窗格左上角的省略號（**...**）按鈕。 然後使用 [**上移**] 箭號，將規則移至 [**互動規則**] 清單的頂端。
   > [!div class="mx-imgBorder"]
   > ![新增範圍驗證](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > 在真實世界的應用程式中，使用 [**動作**] 區段將活動傳送回用戶端，或呼叫 HTTP 端點以更新系統中的警示。

## <a name="try-it-out"></a>試試看

1. 選取 [訓練]。

1. 定型完成之後，請選取 [**測試**]，然後嘗試下列互動：

   - 輸入：為明天的中午設定鬧鐘
   - 輸出：您確定要設定 2020-05-02 12:00:00 的鬧鐘
   - 輸入：是
   - 輸出：正常，已為 2020-05-02 12:00:00 設定鬧鐘
   - 輸入：否，明天下午2：2
   - 輸出：將先前的警示更新為 2020-05-02 14:00:00
