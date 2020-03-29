---
title: 語音裝置 SDK 文件
titleSuffix: Azure Cognitive Services
description: 版本資訊提供更新、增強功能、錯誤修復和對語音設備 SDK 的更改的日誌。 本文將隨語音設備 SDK 的每個版本進行更新。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371608"
---
# <a name="release-notes-speech-devices-sdk"></a>版本資訊：語音設備 SDK

下列各節列出最新版本中的變更。

## <a name="speech-devices-sdk-190"></a>語音設備 SDK 1.9.0：

- 提供了[Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) （Linux ARM64） 的初始二進位檔案。
- Roobo v1 現在使用 Maven 進行語音 SDK
- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本 1.9.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-170"></a>語音設備 SDK 1.7.0：

- 現在支援 Linux ARM。
- 提供了[Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2)的初始二進位檔案（Linux ARM64）。
- Windows 使用者可以使用`AudioConfig.fromDefaultMicrophoneInput()`或`AudioConfig.fromMicrophoneInput(deviceName)`指定要使用的麥克風。
- 庫大小已過優化。
- 支援使用相同的語音/意圖識別器物件進行多圈識別。
- 修復在停止識別時偶爾發生的掛起。
- 示例應用現在包含一個示例參與者。屬性檔來演示檔的格式。
- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本 1.7.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-160"></a>語音設備 SDK 1.6.0：

- 通過常見的[應用程式範例](https://aka.ms/sdsdk-download)在 Windows 和 Linux 上支援[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本 1.6.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-151"></a>語音設備 SDK 1.5.1：

- 在示例應用中包括[對話轉錄](conversation-transcription-service.md)。
- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本 1.5.1。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-150-2019-may-release"></a>語音設備 SDK 1.5.0： 2019-5 月發佈

- 語音設備 SDK 現在是 GA，不再是門控預覽。
- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本 1.5.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- 新的關鍵字技術帶來了顯著的品質改進，請參閱"重大變革"。
- 新的音訊處理管道，用於改進遠場識別。

**重大變更**

- 由於新的關鍵字技術，所有關鍵字都必須在我們的改進的關鍵字門戶重新創建。 要從設備中完全刪除舊關鍵字，請卸載舊應用。
  - adb 卸載 com.microsoft.認知服務.語音.sample.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>語音設備 SDK 1.4.0： 2019-4 月版本

- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本 1.4.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-131-2019-mar-release"></a>語音設備 SDK 1.3.1：2019-3 月版本

- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)元件更新為版本 1.3.1。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- 更新了關鍵字處理，請參閱"中斷更改"。
- 應用程式範例增加了語音辨識和翻譯的語言選擇。

**重大變更**

- [安裝關鍵字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)已簡化，它現在是應用程式的一部分，不需要單獨安裝在設備上。
- 關鍵字識別已更改，支援兩個事件。
  - `RecognizingKeyword,`指示語音結果包含（未經驗證）關鍵字文本。
  - `RecognizedKeyword`，指示關鍵字識別已完成對給定關鍵字的識別。

## <a name="speech-devices-sdk-110-2018-nov-release"></a>語音設備 SDK 1.1.0： 2018-11 月版本

- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 1.1.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- 遠場語音辨識精確度已藉由增強的音訊處理演算法獲得提升。
- 範例應用程式新增了中文語音辨識支援。

## <a name="speech-devices-sdk-101-2018-oct-release"></a>語音設備 SDK 1.0.1： 2018-10 月版本

- 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 1.0.1。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
- 語音辨識精確度將藉由我們已改良的音訊處理演算法獲得提升
- 已修正一個連續辨識音訊工作階段 Bug。

**重大變更**

- 此版本引進了幾個重大變更。 如需有關 API 的詳細資料，請參閱[此頁面](https://aka.ms/csspeech/breakingchanges_1_0_0) \(英文\)。
- KWS 模型檔案與「語音裝置 SDK 1.0.1」不相容。 將新關鍵字檔寫入設備後，將刪除現有關鍵字檔。

## <a name="speech-devices-sdk-050-2018-aug-release"></a>語音設備 SDK 0.5.0： 2018-8 月版本

- 已透過修正音訊處理程式碼中的錯誤，改善語音辨識的精確度。
- 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 0.5.0。 如需詳細資訊，請參閱其[版本資訊](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)。

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>語音設備 SDK 0.2.12733： 2018-5 月發佈

認知服務語音裝置 SDK 的第一個公開預覽版本。
