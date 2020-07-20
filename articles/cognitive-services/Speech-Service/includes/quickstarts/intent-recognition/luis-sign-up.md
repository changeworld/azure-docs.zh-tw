---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81421959"
---
若要完成意圖辨識快速入門，您必須使用 LUIS 預覽入口網站建立 LUIS 帳戶和專案。 本快速入門只需要 LUIS 訂用帳戶。 「不需要」  語音服務訂用帳戶。

您的首要工作，是使用 LUIS 預覽入口網站建立 LUIS 帳戶和應用程式。 您所建立的 LUIS 應用程式將使用預先建置的網域進行家庭自動化，以提供意圖、實體和範例語句。 完成之後，您會擁有在雲端中執行的 LUIS 端點，供您使用語音 SDK 進行呼叫。 

請依照下列指示建立 LUIS 應用程式：

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">快速入門：建置預先建置的網域應用程式<span class="docon docon-navigate-external x-hidden-focus"></span></a>

完成作業後，您將需要四項資訊：

* 將**語音預備**切換為開啟以重新發佈
* 您的 LUIS **主要金鑰**
* 您的 LUIS **位置**
* 您的 LUIS **應用程式識別碼**

您可以在此處透過 [LUIS 預覽入口網站](https://preview.luis.ai/)找到這些資訊：

1. 從 LUIS 預覽入口網站中選取您的應用程式，然後選取 [發佈]  按鈕。

2. 選取 [生產]  位置 (如果您使用 `en-US`，請將 [語音預備]  選項切換至 [開啟]  位置)。 然後，選取 [發佈]  按鈕。

    > [!IMPORTANT]
    > 強烈建議您使用**語音預備**，因為其可以改善語音辨識的精確度。

    > [!div class="mx-imgBorder"]
    > ![將 LUIS 發佈到端點](../../../media/luis/publish-app-popup.png)

3. 在 LUIS 預覽入口網站中選取 [管理]  ，然後選取 [Azure 資源]  。 在此頁面上，您可以找到您的 LUIS 金鑰和位置 (有時也稱為_區域_)。

   > [!div class="mx-imgBorder"]
   > ![LUIS 金鑰和位置](../../../media/luis/luis-key-region.png)

4. 取得金鑰和位置之後，您將需要應用程式識別碼。 選取 [應用程式設定]  -- 此頁面會提供您的應用程式識別碼。

   > [!div class="mx-imgBorder"]
   > ![LUIS 應用程式識別碼](../../../media/luis/luis-app-id.png)
