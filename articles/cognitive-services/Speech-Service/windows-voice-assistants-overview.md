---
title: Windows 上的語音助理-總覽
titleSuffix: Azure Cognitive Services
description: 概要說明 Windows 上的語音助理，包括可用的功能和開發資源。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: fbcb262fee6a2cc62bfe64e8a8589c92b4fe2b17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997764"
---
# <a name="voice-assistants-on-windows"></a>Windows 上的語音助理

在 Windows 10 2004 版和更新版本上，語音助理應用程式可以利用 Windows ConversationalAgent Api 來達成完整的啟用語音助理體驗。

## <a name="voice-assistant-features"></a>語音助理功能

語音代理程式應用程式可以透過說出的關鍵字來啟用，以啟用免實際操作的語音驅動體驗。 當應用程式關閉時，以及當螢幕鎖定時，語音啟用會正常運作。

此外，Windows 還提供一組語音啟用隱私權設定，讓使用者能夠控制每個應用程式的語音啟用和鎖定啟用。

語音啟用之後，Windows 會適當地管理多個使用中的代理程式，並在每個語音助理遭到中斷或停用時通知他們。 這可讓應用程式適當地管理中斷和其他代理程式間事件。

## <a name="how-does-voice-activation-work"></a>語音啟用的運作方式為何？

代理程式啟動執行時間 (AAR) 是 Windows 中的持續性處理常式，可管理語音關鍵字或按鈕按下的應用程式啟用。 它是從 Windows 開始，只要系統上至少有一個應用程式向系統註冊即可。 應用程式會透過 Windows SDK 中的 ConversationalAgent Api 與 AAR 互動。

當使用者說出關鍵字時，系統上偵查器的軟體或硬體關鍵字會通知 AAR 已偵測到關鍵字，提供關鍵字識別碼。 AAR 接著會將要求傳送至 BackgroundService，以使用對應的應用程式識別碼啟動應用程式。

### <a name="registration"></a>註冊

當語音啟動的應用程式第一次執行時，它會透過 ConversationalAgent Api 來註冊其應用程式識別碼和關鍵字資訊。 AAR 會向系統上的硬體或軟體關鍵字偵查器註冊全域對應中的所有設定，讓它們偵測應用程式的關鍵字。 應用程式也會 [向背景服務註冊](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)。

請注意，這表示應用程式無法透過語音啟用，直到它執行一次且已允許註冊完成為止。

### <a name="receiving-an-activation"></a>接收啟用

從 AAR 收到要求時，背景服務會啟動應用程式。 應用程式會 `App.xaml.cs` 使用唯一的事件引數，在中透過 OnBackgroundActivated 生命週期方法來接收信號。 此引數會告訴應用程式，它是由 AAR 啟用，而且應該啟動關鍵字驗證。

如果應用程式成功驗證關鍵字，它可能會提出要求以顯示在前景中。 當此要求成功時，應用程式會顯示 UI，並繼續與使用者互動。

當使用中的應用程式的關鍵字被讀出時，AAR 仍會發出信號。 不過，它不會在中透過生命週期方法發出信號，而是 `App.xaml.cs` 透過 ConversationalAgent api 中的事件發出信號。

### <a name="keyword-verification"></a>關鍵字驗證

觸發應用程式啟動的關鍵字偵查器，藉由簡化關鍵字模型來達成低耗電量。 這可讓關鍵字偵查器「永遠開啟」，而不會有高度的電源影響，但這也表示關鍵字偵查器很可能會有較高的「false 接受」值，即使沒有說出關鍵字也是一樣。 這就是語音啟用系統在背景中啟動應用程式的原因：為了讓應用程式有機會在中斷使用者目前的會話之前，先確認是否已說出關鍵字。 AAR 會在出現關鍵字之前幾秒鐘後儲存音訊，並讓應用程式可以存取。 應用程式可以使用此應用程式，在相同的音訊上執行更可靠的關鍵字偵查器。

## <a name="next-steps"></a>接下來的步驟

- 請**參閱設計指導方針：** 我們的[設計指導方針](windows-voice-assistants-best-practices.md)會配置在 Windows 10 上提供語音啟用的最佳體驗所需的重要工作。
- **造訪消費者入門頁面：** 從 [這裡](how-to-windows-voice-assistants-get-started.md) 開始，以瞭解在 Windows 上開始執行語音助理的步驟，從設定開發環境的簡介指南。
- **試用範例應用程式**：若要體驗這些功能第一手，請造訪 [UWP 語音助理範例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) 頁面，並遵循步驟以取得範例用戶端執行。
