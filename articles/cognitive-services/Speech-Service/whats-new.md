---
title: 檔中的新功能-語音服務
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
ms.openlocfilehash: c2b96193204c6938338594054bc3cd4715008313
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89055306"
---
# <a name="speech-service-whats-new-in-docs"></a>語音服務：檔的新功能

歡迎！ 本頁面涵蓋語音服務檔中的新功能。請每月回來查看，以取得服務變更的相關資訊、doc 新增專案和本月的更新。

### <a name="service-updates"></a>服務更新

如果您想要瞭解語音服務、語音 SDK、語音裝置 SDK 或語音 CLI 的更新，請參閱：
* [語音 SDK 版本](releasenotes.md)資訊。
* [語音裝置 SDK 版本資訊](devices-sdk-release-notes.md)

## <a name="may-2020"></a>2020 年 5 月

### <a name="new-articles"></a>新文章

* [改進自訂語音的模型](how-to-custom-speech-improve-accuracy.md)

### <a name="updated-articles"></a>更新的文章

* [關於語音 SDK 音訊輸入串流 API](how-to-use-audio-input-streams.md) -每秒更新的允許樣本數。
* [適用于語音轉換文字的自動語言偵測](how-to-automatic-language-detection.md) -檔的新增目標 C 指示。
* [選擇語音辨識模式](how-to-choose-recognition-mode.md) -將 JavaScript 指示新增至檔。
* [設定 RHEL/CentOS 7 For SPEECH SDK](how-to-configure-rhel-centos-7.md) -改善的安裝指示。
* [語音轉換文字](how-to-phrase-lists.md) 新增的 JavaScript 指示至檔的片語清單。
* [快速入門： Python 中適用于長格式音訊的非同步合成 (預覽版) ](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) -已更新為可支援公用神經語音，以及相關聯的參數。
* [快速入門：辨識來自音訊](quickstarts/speech-to-text-from-file.md) 檔案的語音-已新增 JavaScript 指示至檔。
* [快速入門：辨識來自麥克風的語音](quickstarts/speech-to-text-from-microphone.md ) -新增的 Go 和 JavaScript 指示至檔。
* [快速入門：辨識儲存在 blob 儲存體中的語音](quickstarts/from-blob.md) -將 JavaScript 指示新增至檔。
* [快速入門：使用 Language Understanding (LUIS 來辨識語音、意圖和實體) ](quickstarts/intent-recognition.md)
* [快速入門：設定開發環境](quickstarts/setup-platform.md) -將 JavaScript 指示新增至檔。
* [快速入門：將語音合成至音訊](quickstarts/text-to-speech-audio-file.md) 檔-將 JavaScript 指示新增至檔。
* [快速入門：將語音合成至](quickstarts/text-to-speech.md) 已新增至講師的 JavaScript 指示至檔。
* [什麼是關鍵字？](custom-keyword-overview.md) -已更新開始使用內容和連結。
* [針對語音轉換文字新增的](how-to-specify-source-language.md) JavaScript 和目標 C 指示，指定原始程式碼語言。

### <a name="github-issues-opened-in-may"></a>在5月開啟的 GitHub 問題

這些問題已于5月開啟。 下表列出開啟問題的使用者、開啟問題的時間，以及其狀態。  

此資料表每月更新一次，而且只會反映在5月開啟的問題。  

|識別碼|User|描述|已開啟|State|類型|
| :--- | :--- | :--- | :--- | :--- | :--- |
|[56045](https://github.com/MicrosoftDocs/azure-docs/issues/56045)|rhalaly|因為缺少 bot 的端點，所以已丟棄活動|2020-05-31|封閉式|問題|
|[56038](https://github.com/MicrosoftDocs/azure-docs/issues/56038)|rhalaly|發佈 bot 的步驟錯誤|2020-05-31|開啟|問題|
|[56014](https://github.com/MicrosoftDocs/azure-docs/issues/56014)|mosdav|新增更清楚的 PCM 範例格式檔|2020-05-30|開啟|問題|
|[55984](https://github.com/MicrosoftDocs/azure-docs/issues/55984)|chschrae|標題不符合側邊列|2020-05-29|封閉式|問題|
|[55857](https://github.com/MicrosoftDocs/azure-docs/issues/55857)|nitinbhatia-開發人員|Wave 檔案的 CLI 錯誤|2020-05-28|封閉式|問題|
|[55717](https://github.com/MicrosoftDocs/azure-docs/pull/55717)|dargilco|更新 speech-sdk.md|2020-05-27|開啟|提取要求|
|[55299](https://github.com/MicrosoftDocs/azure-docs/issues/55299)|Tirumala-K|不支援的語音名稱出現奇怪的錯誤|2020-05-20|封閉式|問題|
|[55099](https://github.com/MicrosoftDocs/azure-docs/issues/55099)|kmoore-riphaina|語音轉換文字 api 的檔不佳|2020-05-18|開啟|問題|
|[55032](https://github.com/MicrosoftDocs/azure-docs/issues/55032)|dubbySwords|Microsoft CognitiveServices 語音類別 SpeechRecognizer 無法收集產生的文字|2020-05-18|封閉式|問題|
|[55031](https://github.com/MicrosoftDocs/azure-docs/issues/55031)|dubbySwords|不清楚|2020-05-18|封閉式|問題|
|[55027](https://github.com/MicrosoftDocs/azure-docs/issues/55027)|ovishesh|深色主題中不可見的圖形|2020-05-17|封閉式|問題|
|[54919](https://github.com/MicrosoftDocs/azure-docs/issues/54919)|kmoore-riphaina|遺漏的區段？|2020-05-15|開啟|問題|
|[54743](https://github.com/MicrosoftDocs/azure-docs/issues/54743)|fifteenjoy|執行語音服務容器失敗|2020-05-13|開啟|問題|
|[54550](https://github.com/MicrosoftDocs/azure-docs/issues/54550)|manish-95|發音檔案的範例|2020-05-11|開啟|問題|
|[54522](https://github.com/MicrosoftDocs/azure-docs/issues/54522)|pjmlp|JAVA 範例不正確。|2020-05-10|開啟|問題|
|[54387](https://github.com/MicrosoftDocs/azure-docs/issues/54387)|ziadhassan7|無法取得發音分數|2020-05-08|封閉式|問題|
|[54382](https://github.com/MicrosoftDocs/azure-docs/issues/54382)|jgtellez1|YAML 檔案範本|2020-05-07|封閉式|問題|
|[54208](https://github.com/MicrosoftDocs/azure-docs/issues/54208)|paparush|C # 範例程式碼不會提示使用者進行交談。|2020-05-06|封閉式|問題|
|[54132](https://github.com/MicrosoftDocs/azure-docs/pull/54132)|anthonsu|將 TTS 從 v1.0 升級到1.4 版|2020-05-05|封閉式|提取要求|
|[54111](https://github.com/MicrosoftDocs/azure-docs/pull/54111)|anthonsu|將自訂 STT 最新版本更新為 v 2.2。0|2020-05-05|封閉式|提取要求|
|[53919](https://github.com/MicrosoftDocs/azure-docs/issues/53919)|eyast|Github 專案的連結已中斷|2020-05-03|開啟|問題|
|[53892](https://github.com/MicrosoftDocs/azure-docs/issues/53892)|viju2008|要定義的屬性： Android 麥克風的最大音訊辨識時間。 15秒後停止音訊辨識|2020-05-02|封閉式|問題|
|[53796](https://github.com/MicrosoftDocs/azure-docs/pull/53796)|singhsaumya|自訂命令：檔更新|2020-05-01|封閉式|提取要求|
