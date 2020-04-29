---
title: 如何：將單步驟更正新增至自訂命令（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，我們會說明如何在自訂命令中為命令執行一個步驟更正。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75462389"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>如何：在自訂命令中新增單步驟更正（預覽）

在本文中，您將瞭解如何在命令中新增一個步驟確認。

單步驟修正是用來更新剛完成的命令。

也就是說，如果您剛設定了警示，就可以改變您的想法，並更新警示的時間。

- 輸入：為明天的中午設定鬧鐘
- 輸出：「正常，已為 12/06/2019 12:00:00 設定鬧鐘」
- 輸入：否，明天下午
- 輸出：「確定

請記住，這表示身為開發人員的您可以在後端應用程式中更新警示。

## <a name="prerequisites"></a>先決條件

您必須已完成下列文章中的步驟：

- [快速入門：建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-new.md)
- [快速入門：使用參數來建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
- [如何：在自訂命令中新增確認（預覽）](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>新增單步驟更正的 advanced 規則 

為了示範單步驟更正，讓我們擴充在[確認如何](./how-to-custom-speech-commands-confirmations.md)中建立的**SetAlarm**命令。
 
1. 新增「高級」規則以更新先前的警示。 

    此規則會要求使用者確認警示的日期和時間，並預期會有確認（是/否）以進行下一回合。

   | 設定               | 建議的值                                                  | 描述                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 規則名稱             | 更新先前的警示                                            | 描述規則用途的名稱          |
   | 條件            | UpdateLastCommand & 必要參數-DateTime                | 判斷規則何時可執行檔條件    |   
   | 動作               | SpeechResponse-"-正在將先前的警示更新至 {DateTime}"       | 規則條件為 true 時要採取的動作 |
   | 執行後的狀態 | 完成命令                                                 | 回合後的使用者狀態                   |

1. 將您剛才建立的規則移至 [高級規則] 頂端（在面板中的規則上按下，然後按一下向上箭號）。
   > [!div class="mx-imgBorder"]
   > ![新增範圍驗證](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> 在實際的應用程式中，在此規則的 [動作] 區段中，您也會將活動傳送回用戶端或呼叫 HTTP 端點，以更新您系統中的警示。

## <a name="try-it-out"></a>試試看

選取 [測試] 面板，然後嘗試一些互動。

- 輸入：為明天的中午設定鬧鐘
- 輸出：「您確定要設定 12/07/2019 12:00:00 的鬧鐘嗎？」
- 輸入：是
- 輸出：「正常，已為 12/07/2019 12:00:00 設定鬧鐘」
- 輸入：否，明天下午
- 輸出：「將先前的警示更新為 12/07/2019 13:00:00」
