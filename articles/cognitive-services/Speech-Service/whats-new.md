---
title: 檔的新功能-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解 Azure 語音服務的檔更新。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: erhopf
ms.openlocfilehash: 017de2f66d6e57c9081e0d1ed53232a4634d4f4f
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84740301"
---
# <a name="speech-service-whats-new-in-docs"></a>語音服務：檔的新功能

歡迎！ 本頁涵蓋語音服務檔的新功能。每月回顧一次，取得服務變更、檔新增及本月更新的相關資訊。

### <a name="service-updates"></a>服務更新

如果您想要瞭解語音服務、語音 SDK、語音裝置 SDK 或語音 CLI 的更新，請參閱：
* [語音 SDK 版本](releasenotes.md)資訊。
* [語音裝置 SDK 版本資訊](devices-sdk-release-notes.md)

## <a name="may-2020"></a>2020 年 5 月

### <a name="new-articles"></a>新文章

* [改善自訂語音的模型](how-to-custom-speech-improve-accuracy.md)

### <a name="updated-articles"></a>更新的文章

* [關於語音 SDK 音訊輸入資料流程 API](how-to-use-audio-input-streams.md) -每秒已更新的允許樣本數。
* [語音轉換文字新增的目標 C 指示檔的自動語言偵測](how-to-automatic-language-detection.md)。
* [選擇語音辨識模式](how-to-choose-recognition-mode.md)-將 JavaScript 指示新增至檔。
* [設定 RHEL/CentOS 7 For SPEECH SDK](how-to-configure-rhel-centos-7.md) -改良的安裝指示。
* [語音轉換文字-將](how-to-phrase-lists.md)JavaScript 指示新增至檔的片語清單。
* [快速入門： Python （預覽）中長格式音訊的非同步合成](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)-更新為支援公用類神經語音和相關聯的參數。
* [快速入門：從音訊檔案辨識語音](quickstarts/speech-to-text-from-file.md)-將 JavaScript 指示新增至檔。
* [快速入門：從麥克風新增的](quickstarts/speech-to-text-from-microphone.md )Go 和 JavaScript 指示，將語音辨識到檔。
* [快速入門：辨識儲存在 blob 儲存體中的語音](quickstarts/from-blob.md)-將 JavaScript 指示新增至檔。
* [快速入門：使用 Language Understanding 來辨識語音、意圖和實體（LUIS）](quickstarts/intent-recognition.md)
* [快速入門：設定開發環境](quickstarts/setup-platform.md)-將 JavaScript 指示新增至檔。
* [快速入門：將語音合成至音訊](quickstarts/text-to-speech-audio-file.md)檔案-將 JavaScript 指示新增至檔。
* [快速入門：將語音合成至喇叭](quickstarts/text-to-speech.md)-將 JavaScript 指示新增至檔。
* [快速入門：將語音轉換-語音](quickstarts/translate-speech-to-speech.md)新增的 JavaScript 指示轉譯為檔。
* [快速入門：將語音翻譯成多種語言](quickstarts/translate-speech-to-text-multiple-languages.md)-將 JavaScript 指示新增至檔。
* [快速入門：將語音轉換文字](quickstarts/translate-speech-to-text.md)-已新增的 JavaScript 指示轉譯為檔。
* [什麼是關鍵字？](custom-keyword-overview.md) -已更新開始使用的內容和連結。
* 為檔中[的語音轉換文字](how-to-specify-source-language.md)新增 JavaScript 和目標-C 指示指定來源語言。

### <a name="github-issues-opened-in-may"></a>在5月開啟的 GitHub 問題

這些問題已在六月開啟。 下表列出開啟問題的使用者、開啟的時間，以及其狀態。  

此資料表會每月更新，而且只會反映在5月中開啟的問題。  

|ID|User|描述|已開啟|State|類型|
| :--- | :--- | :--- | :--- | :--- | :--- |
|[56045](https://github.com/MicrosoftDocs/azure-docs/issues/56045)|rhalaly|因為 bot 的端點遺失，所以已捨棄活動|2020-05-31|關閉|問題|
|[56038](https://github.com/MicrosoftDocs/azure-docs/issues/56038)|rhalaly|發佈 bot 步驟錯誤|2020-05-31|開啟|問題|
|[56014](https://github.com/MicrosoftDocs/azure-docs/issues/56014)|mosdav|新增更清楚的關於 PCM 範例格式的檔|2020-05-30|開啟|問題|
|[55984](https://github.com/MicrosoftDocs/azure-docs/issues/55984)|chschrae|標題不符合側條|2020-05-29|關閉|問題|
|[55857](https://github.com/MicrosoftDocs/azure-docs/issues/55857)|nitinbhatia-dev|Wave 檔案的 CLI 錯誤|2020-05-28|關閉|問題|
|[55717](https://github.com/MicrosoftDocs/azure-docs/pull/55717)|dargilco|更新 speech-sdk.md|2020-05-27|開啟|提取要求|
|[55299](https://github.com/MicrosoftDocs/azure-docs/issues/55299)|Tirumala-K|具有不支援之語音名稱的奇怪錯誤|2020-05-20|關閉|問題|
|[55099](https://github.com/MicrosoftDocs/azure-docs/issues/55099)|kmoore-riphaina|語音轉換文字 api 上的檔不佳|2020-05-18|開啟|問題|
|[55032](https://github.com/MicrosoftDocs/azure-docs/issues/55032)|dubbySwords|Microsoft CognitiveServices 語音類別 SpeechRecognizer 無法收集產生的文字|2020-05-18|關閉|問題|
|[55031](https://github.com/MicrosoftDocs/azure-docs/issues/55031)|dubbySwords|非清除|2020-05-18|關閉|問題|
|[55027](https://github.com/MicrosoftDocs/azure-docs/issues/55027)|ovishesh|深色主題中看不到圖形|2020-05-17|關閉|問題|
|[54919](https://github.com/MicrosoftDocs/azure-docs/issues/54919)|kmoore-riphaina|遺漏區段？|2020-05-15|開啟|問題|
|[54743](https://github.com/MicrosoftDocs/azure-docs/issues/54743)|fifteenjoy|執行語音服務容器失敗|2020-05-13|開啟|問題|
|[54550](https://github.com/MicrosoftDocs/azure-docs/issues/54550)|manish-95|發音檔案的範例|2020-05-11|開啟|問題|
|[54522](https://github.com/MicrosoftDocs/azure-docs/issues/54522)|pjmlp|JAVA 範例不正確。|2020-05-10|開啟|問題|
|[54387](https://github.com/MicrosoftDocs/azure-docs/issues/54387)|ziadhassan7|無法取得 pronounciation 分數|2020-05-08|關閉|問題|
|[54382](https://github.com/MicrosoftDocs/azure-docs/issues/54382)|jgtellez1|YAML 檔案範本|2020-05-07|關閉|問題|
|[54208](https://github.com/MicrosoftDocs/azure-docs/issues/54208)|paparush|C # 範例程式碼不會提示使用者說話。|2020-05-06|關閉|問題|
|[54132](https://github.com/MicrosoftDocs/azure-docs/pull/54132)|anthonsu|將 TTS 從1.3 版升級至1.4 版|2020-05-05|關閉|提取要求|
|[54111](https://github.com/MicrosoftDocs/azure-docs/pull/54111)|anthonsu|將自訂 STT 最新版本更新為 v 2.2。0|2020-05-05|關閉|提取要求|
|[53919](https://github.com/MicrosoftDocs/azure-docs/issues/53919)|eyast|Github 專案的連結已中斷|2020-05-03|開啟|問題|
|[53892](https://github.com/MicrosoftDocs/azure-docs/issues/53892)|viju2008|要定義的屬性： Android 麥克風的最大音訊識別時間。 15秒後停止音訊識別|2020-05-02|關閉|問題|
|[53796](https://github.com/MicrosoftDocs/azure-docs/pull/53796)|singhsaumya|自訂命令：檔更新|2020-05-01|關閉|提取要求|
