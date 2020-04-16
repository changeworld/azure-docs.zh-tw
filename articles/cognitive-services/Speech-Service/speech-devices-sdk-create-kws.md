---
title: 建立自訂關鍵字 ─語音服務
titleSuffix: Azure Cognitive Services
description: 您的設備始終在偵聽關鍵字(或短語)。 當使用者說出關鍵字時,設備會將所有後續音訊發送到雲,直到使用者停止說話。 自定義關鍵字是區分您的設備並增強品牌的有效方法。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400067"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>使用語音工作室建立自訂關鍵字

您的設備始終在偵聽關鍵字(或短語)。 例如,「嘿,Cortana」是 Cortana 助手的關鍵字。 當使用者說出關鍵字時,設備會將所有後續音訊發送到雲,直到使用者停止說話。 自定義關鍵字是區分您的設備並增強品牌的有效方法。

在本文中,您將瞭解如何為設備創建自定義關鍵字。

## <a name="create-your-keyword"></a>建立關鍵字

在使用自定義關鍵字之前,您需要使用[語音工作室](https://aka.ms/sdsdk-speechportal)上的[自定義關鍵字](https://aka.ms/sdsdk-wakewordportal)頁面創建關鍵字。 提供關鍵字後,它會生成部署到設備的檔。

1. 轉到[語音工作室](https://aka.ms/sdsdk-speechportal)並**登入**,或者,如果您尚未訂閱語音訂閱,請選擇「[**創建訂閱**](https://go.microsoft.com/fwlink/?linkid=2086754)」。

1. 自[訂關鍵字](https://aka.ms/sdsdk-wakewordportal)頁面,建立新**項目**。 

1. 輸入**名稱**,可選**說明**,然後選擇語言。 您需要每種語言一個項目,並且支援當前僅限於 en-US 語言。

    ![描述關鍵字項目](media/custom-keyword/custom-kws-portal-new-project.png)

1. 從清單中選擇您的專案。 

    ![選擇關鍵字項目](media/custom-keyword/custom-kws-portal-project-list.png)

1. 要啟動新的關鍵字模型,請按下 **「訓練」 模型**。

1. 輸入關鍵字**模型的名稱**,並在您選擇的**關鍵字**中輸入可選**的「描述」,** 然後按下「**下一步**」 。 我們有一些[準則](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)來幫助選擇有效的關鍵字。

    ![輸入關鍵字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 門戶現在將為您的關鍵字創建候選發音。 通過按一下播放按鈕收聽每個考生,並刪除任何發音不正確的檢查。 只檢查好發音后,單擊 **「訓練」** 開始生成關鍵字。 

    ![檢視關鍵字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 生成模型可能需要長達三十分鐘。 當模型完成時,關鍵字清單將從 **「處理**」更改為 **「成功**」。 然後,您可以下載該檔。

    ![檢視關鍵字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 將 .zip 檔案儲存到您的電腦。 您需要此檔才能將自定義關鍵字部署到您的設備。

## <a name="next-steps"></a>後續步驟

使用[語音裝置 SDK 快速入門](https://aka.ms/sdsdk-quickstart)測試自定義關鍵字。
