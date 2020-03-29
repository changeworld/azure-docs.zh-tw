---
title: 如何：向自訂命令（預覽）添加一步更正 - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，我們將解釋如何在自訂命令中實現命令的一步更正。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75462389"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>如何：向自訂命令添加一步更正（預覽）

在本文中，您將學習如何向命令添加一步確認。

一步更正用於更新剛剛完成的命令。

即，如果你剛剛設置了通報器，你可以改變主意，更新鬧鐘的時間。

- 輸入：為明天中午設置鬧鐘
- 輸出："好的，2019年6月12日12：00：00的報警設置"
- 輸入：否，明天下午 1 點
- 輸出："好的

請記住，這意味著您作為開發人員有一個機制來更新後端應用程式中的警報。

## <a name="prerequisites"></a>Prerequisites

您必須已完成以下文章中的步驟：

- [快速入門：創建自訂命令（預覽）](./quickstart-custom-speech-commands-create-new.md)
- [快速入門：使用參數創建自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
- [如何：向自訂命令添加確認（預覽）](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>添加用於一步更正的高級規則 

為了演示一步更正，讓我們擴展在["如何執行"](./how-to-custom-speech-commands-confirmations.md)中的**SetAlarm**命令。
 
1. 添加高級規則以更新上一個警報。 

    此規則將要求使用者確認警報的日期和時間，並期待下一個回合的確認（是/否）。

   | 設定               | 建議的值                                                  | 描述                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 規則名稱             | 更新上一個警報                                            | 描述規則目的的名稱          |
   | 條件            | 更新狀態命令&所需參數 - 日期時間                | 確定規則何時可以運行的條件    |   
   | 動作               | 語音回應 - "- 將上一個警報更新到 [DateTime]"       | 規則條件為 true 時要執行的操作 |
   | 執行後的狀態 | 完成命令                                                 | 轉彎後使用者的狀態                   |

1. 將剛剛創建的規則移到高級規則的頂部（滾動面板中的規則，然後按一下 UP 箭頭）。
   > [!div class="mx-imgBorder"]
   > ![添加範圍驗證](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> 在實際應用程式中，在此規則的"操作"部分中，您還將將活動發送回用戶端或調用 HTTP 終結點以更新系統中的警報。

## <a name="try-it-out"></a>試做

選擇"測試"面板並嘗試一些交互。

- 輸入：為明天中午設置鬧鐘
- 輸出："是否確實要為 2019 年 7 月 12 日 12：00：00 設置鬧鐘？"
- 輸入： 是
- 輸出："好的，2019年7月12日12：00：00的報警設置"
- 輸入：否，明天下午 1 點
- 輸出："將以前的警報更新至 2019 年 7 月 12 日 13：00：00"
