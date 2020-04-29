---
title: 建立自訂關鍵字-語音服務
titleSuffix: Azure Cognitive Services
description: 您的裝置一律會接聽關鍵字（或片語）。 當使用者說出關鍵字時，裝置會將所有後續的音訊傳送到雲端，直到使用者停止說話為止。 自訂您的關鍵字是區分您的裝置並強化商標的有效方式。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400067"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>使用語音 Studio 建立自訂關鍵字

您的裝置一律會接聽關鍵字（或片語）。 例如，「嘿 Cortana」是 Cortana assistant 的關鍵字。 當使用者說出關鍵字時，裝置會將所有後續的音訊傳送到雲端，直到使用者停止說話為止。 自訂您的關鍵字是區分您的裝置並強化商標的有效方式。

在本文中，您將瞭解如何為您的裝置建立自訂關鍵字。

## <a name="create-your-keyword"></a>建立關鍵字

在您可以使用自訂關鍵字之前，您必須使用[語音 Studio](https://aka.ms/sdsdk-speechportal)上的[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)頁來建立關鍵字。 在您提供關鍵字之後，它會產生一個檔案，您可以將它部署到您的裝置。

1. 移至[語音 Studio](https://aka.ms/sdsdk-speechportal)並登**入**，或者，如果您還沒有語音訂用帳戶，請選擇 [[**建立訂用**](https://go.microsoft.com/fwlink/?linkid=2086754)帳戶]。

1. 在 [[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)] 頁面上，建立**新的專案**。 

1. 輸入**名稱**、選擇性**描述**，然後選取語言。 您將需要每個語言一個專案，而且支援目前僅限於 en-us 語言。

    ![描述關鍵字專案](media/custom-keyword/custom-kws-portal-new-project.png)

1. 從清單中選取您的專案。 

    ![選取您的關鍵字專案](media/custom-keyword/custom-kws-portal-project-list.png)

1. 若要啟動新的關鍵字模型，請按一下 [**定型模型**]。

1. 輸入關鍵字模型的 [**名稱**] 和選擇性 [**描述**]，然後鍵入您選擇的**關鍵字**，然後按 **[下一步]**。 我們有一些[指導方針](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)可協助您選擇有效的關鍵字。

    ![輸入您的關鍵字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 入口網站現在會為您的關鍵字建立候選的發音。 按一下 [播放] 按鈕，並移除任何不正確之發音旁的檢查，以接聽每個候選。 一旦核取了良好的發音，請按一下 [**定型**] 開始產生關鍵字。 

    ![檢查關鍵字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 最多可能需要30分鐘的時間來產生模型。 當模型完成時，關鍵字清單會從**處理**變更為**成功**。 然後您就可以下載檔案。

    ![檢查關鍵字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 將 .zip 檔案儲存到您的電腦。 您將需要此檔案，才能將自訂關鍵字部署至您的裝置。

## <a name="next-steps"></a>後續步驟

使用[語音裝置 SDK 快速入門](https://aka.ms/sdsdk-quickstart)測試您的自訂關鍵字。
