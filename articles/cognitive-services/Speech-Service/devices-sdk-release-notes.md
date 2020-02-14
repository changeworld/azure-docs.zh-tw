---
title: 語音裝置 SDK 文件
titleSuffix: Azure Cognitive Services
description: 版本資訊提供「語音裝置 SDK」的更新、增強功能、錯誤修正和變更記錄。 這篇文章已在每個版本的語音裝置 SDK 中更新。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: c12aaea1dbc99a3f6db064e03b4b49e569f15194
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189066"
---
# <a name="release-notes-speech-devices-sdk"></a>版本資訊：語音裝置 SDK

下列各節列出最新版本中的變更。

## <a name="speech-devices-sdk-190"></a>語音裝置 SDK 1.9.0：

- 提供[URBETTER DDK](https://aka.ms/sdsdk-download-urbetter)的初始二進位檔（Linux ARM64）。
- Roobo v1 現在會使用適用于語音 SDK 的 Maven
- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本1.9.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-170"></a>語音裝置 SDK 1.7.0：

- 現在支援 Linux ARM。
- 系統會提供[Roobo V2 DDK](https://aka.ms/sdsdk-download-roobov2)的初始二進位檔（Linux ARM64）。
- Windows 使用者可以使用 `AudioConfig.fromDefaultMicrophoneInput()` 或 `AudioConfig.fromMicrophoneInput(deviceName)` 來指定要使用的麥克風。
- 程式庫大小已經過優化。
- 支援使用相同的語音/意圖辨識器物件進行多重回合辨識。
- 修正停止辨識時偶爾會發生的暫止。
- 範例應用程式現在包含範例參與者. properties 檔案，以示範檔案的格式。
- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本1.7.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-160"></a>語音裝置 SDK 1.6.0：

- 使用通用[範例應用程式](https://aka.ms/sdsdk-download)支援 Windows 和 Linux 上的[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本1.6.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-151"></a>語音裝置 SDK 1.5.1：

- 在範例應用程式中包含[交談](conversation-transcription-service.md)轉譯。
- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本1.5.1。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-150-2019-may-release"></a>語音裝置 SDK 1.5.0： 2019-可能發行

- 語音裝置 SDK 現已正式推出，不再是閘道預覽。
- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為1.5.0 版。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- New 關鍵字技術帶來重大的品質改進，請參閱重大變更。
- 新的音訊處理管線，改善了遠欄位辨識。

**重大變更**

- 由於新的關鍵字技術，所有關鍵詞都必須在我們改良的關鍵字入口網站上重新建立。 若要從裝置中完全移除舊的關鍵字，請卸載舊的應用程式。
  - adb 卸載 coginitiveservices. sdsdkstarterapp。

## <a name="speech-devices-sdk-140-2019-apr-release"></a>語音裝置 SDK 1.4.0： 2019-Apr 版本

- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本1.4.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-131-2019-mar-release"></a>語音裝置 SDK 1.3.1： 2019-Mar 版本

- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本1.3.1。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- 已更新關鍵字處理，請參閱重大變更。
- 範例應用程式會為語音辨識和翻譯新增語言選擇。

**重大變更**

- [安裝關鍵字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)已簡化，它現在已是應用程式的一部分，而且不需要在裝置上個別安裝。
- 關鍵字識別已變更，而且支援兩個事件。
  - `RecognizingKeyword,` 表示語音結果包含（未驗證）關鍵字文字。
  - `RecognizedKeyword`，表示關鍵字識別已完成辨識指定的關鍵字。

## <a name="speech-devices-sdk-110-2018-nov-release"></a>語音裝置 SDK 1.1.0： 2018-11 月版本

- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 1.1.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- 遠場語音辨識精確度已藉由增強的音訊處理演算法獲得提升。
- 範例應用程式新增了中文語音辨識支援。

## <a name="speech-devices-sdk-101-2018-oct-release"></a>語音裝置 SDK 1.0.1： 2018-Oct 版本

- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 1.0.1。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- 語音辨識精確度將藉由我們已改良的音訊處理演算法獲得提升
- 已修正一個連續辨識音訊工作階段 Bug。

**重大變更**

- 此版本引進了幾個重大變更。 如需有關 API 的詳細資料，請參閱[此頁面](https://aka.ms/csspeech/breakingchanges_1_0_0) \(英文\)。
- KWS 模型檔案與「語音裝置 SDK 1.0.1」不相容。 將新的關鍵字檔案寫入裝置之後，將會刪除現有的關鍵字檔案。

## <a name="speech-devices-sdk-050-2018-aug-release"></a>語音裝置 SDK 0.5.0： 2018-8 月版本

- 已透過修正音訊處理程式碼中的錯誤，改善語音辨識的精確度。
- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 0.5.0。 如需詳細資訊，請參閱其[版本資訊](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)。

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>語音裝置 SDK 0.2.12733： 2018-可能發行

認知服務語音裝置 SDK 的第一個公開預覽版本。
