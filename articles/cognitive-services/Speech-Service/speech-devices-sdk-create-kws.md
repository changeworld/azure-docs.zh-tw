---
title: 創建自訂關鍵字 - 語音服務
titleSuffix: Azure Cognitive Services
description: 您的設備始終在偵聽關鍵字（或短語）。 當使用者說出關鍵字時，設備會將所有後續音訊發送到雲，直到使用者停止說話。 自訂關鍵字是區分您的設備並增強品牌的有效方法。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717015"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>使用語音工作室創建自訂關鍵字

您的設備始終在偵聽關鍵字（或短語）。 例如，"嘿，Cortana"是 Cortana 助手的關鍵字。 當使用者說出關鍵字時，設備會將所有後續音訊發送到雲，直到使用者停止說話。 自訂關鍵字是區分您的設備並增強品牌的有效方法。

在本文中，您將瞭解如何為設備創建自訂關鍵字。

## <a name="create-your-keyword"></a>創建關鍵字

在使用自訂關鍵字之前，您需要使用[語音工作室](https://aka.ms/sdsdk-speechportal)上的[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)頁面創建關鍵字。 提供關鍵字後，它會生成部署到設備的檔。

1. 轉到[語音工作室](https://aka.ms/sdsdk-speechportal)並**登錄**，或者，如果您尚未訂閱語音訂閱，請選擇"[**創建訂閱**](https://go.microsoft.com/fwlink/?linkid=2086754)"。

1. 在[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)頁面，創建新**專案**。 

1. 輸入**名稱**，可選**說明**，然後選擇語言。 您需要每種語言一個專案，並且支援當前僅限於 en-US 語言。

    ![描述關鍵字專案](media/custom-keyword/custom-kws-portal-new-project.png)

1. 從清單中選擇您的專案。 

    ![選擇關鍵字專案](media/custom-keyword/custom-kws-portal-project-list.png)

1. 要啟動一個新的關鍵字模型，請按一下 **"訓練"模型**。

1. 輸入關鍵字**模型的名稱**，並在您選擇的**關鍵字**中輸入可選**的"描述"，** 然後按一下"**下一步**"。 我們有一些[準則](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)來説明選擇有效的關鍵字。

    ![輸入關鍵字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 門戶現在將為您的關鍵字創建候選發音。 通過按一下播放按鈕收聽每個考生，並刪除任何發音不正確的檢查。 只檢查好發音後，按一下 **"訓練"** 開始生成關鍵字。 

    ![查看關鍵字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 生成模型可能需要長達三十分鐘。 當模型完成時，關鍵字清單將從 **"處理**"更改為 **"成功**"。 然後，您可以下載該檔。

    ![查看關鍵字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 將 .zip 檔案儲存到您的電腦。 您需要此檔才能將自訂關鍵字部署到您的設備。

## <a name="next-steps"></a>後續步驟

使用[語音設備 SDK 快速入門](https://aka.ms/sdsdk-quickstart)測試自訂關鍵字。
