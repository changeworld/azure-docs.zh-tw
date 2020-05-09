---
title: Windows 上的語音助理-總覽
titleSuffix: Azure Cognitive Services
description: 概述 Windows 上的語音助理，包括可用的功能和開發資源。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: fbcb262fee6a2cc62bfe64e8a8589c92b4fe2b17
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997764"
---
# <a name="voice-assistants-on-windows"></a>Windows 上的語音助理

在 Windows 10 2004 版和更新版本上，語音助理應用程式可以利用 Windows ConversationalAgent Api 來達到具備語音功能的完整助理體驗。

## <a name="voice-assistant-features"></a>語音助理功能

語音代理程式應用程式可以透過說出關鍵字來啟用，以實現免手、語音驅動的體驗。 當應用程式關閉且螢幕已鎖定時，語音啟用就會運作。

此外，Windows 還提供一組語音啟用隱私權設定，讓使用者可以控制每個應用程式的語音啟用和更高的鎖定啟用。

在語音啟用之後，Windows 會正確管理多個使用中的代理程式，並在每個語音助理遭到中斷或停用時通知每位 這可讓應用程式正確地管理中斷和其他代理程式間事件。

## <a name="how-does-voice-activation-work"></a>語音啟用如何運作？

代理程式啟動執行時間（AAR）是 Windows 中持續進行的進程，它會以說話關鍵字或按下按鈕來管理應用程式啟用。 只要系統上至少有一個應用程式在系統上註冊，它就會從 Windows 開始。 應用程式會透過 Windows SDK 中的 ConversationalAgent Api 與 AAR 進行互動。

當使用者說出關鍵字時，系統上偵查器的軟體或硬體關鍵字會通知 AAR 是否已偵測到關鍵字，並提供關鍵字識別碼。 AAR 接著會將要求傳送至 BackgroundService，以啟動具有對應應用程式識別碼的應用程式。

### <a name="registration"></a>註冊

第一次執行語音啟用的應用程式時，它會透過 ConversationalAgent Api 來註冊其應用程式識別碼和關鍵字資訊。 AAR 會使用系統上的硬體或軟體關鍵字偵查器，在全域對應中註冊所有設定，讓它們可以偵測應用程式的關鍵字。 應用程式也會[向背景服務註冊](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)。

請注意，這表示應用程式無法以語音啟動，直到它執行一次，並已允許註冊完成為止。

### <a name="receiving-an-activation"></a>接收啟用

收到來自 AAR 的要求時，背景服務會啟動應用程式。 應用程式會透過中`App.xaml.cs`的 OnBackgroundActivated 生命週期方法，使用唯一的事件引數接收信號。 這個引數會告訴應用程式它是由 AAR 啟動，而且它應該會開始關鍵字驗證。

如果應用程式成功驗證關鍵字，則會提出要求以顯示在前景中。 當此要求成功時，應用程式會顯示 UI，並繼續與使用者互動。

AAR 仍會在說出其關鍵字時，對作用中的應用程式發出信號。 不過，它會透過 ConversationalAgent Api 中的事件`App.xaml.cs`發出信號，而不是透過中的生命週期方法進行信號處理。

### <a name="keyword-verification"></a>關鍵字驗證

用來觸發應用程式啟動的關鍵字偵查器，藉由簡化關鍵字模型來達到低功率耗用量。 這可讓關鍵字偵查器成為「永遠開啟」，而不會有高度的影響，但這也表示關鍵字偵查器可能會有大量的「false 接受」，而它會偵測關鍵字，即使沒有說出關鍵字也一樣。 這就是語音啟用系統在背景啟動應用程式的原因：讓應用程式有機會在中斷使用者目前的會話之前，驗證關鍵字是否已被讀出。 AAR 會在發現關鍵字之前的幾秒後儲存音訊，並讓應用程式可以存取。 應用程式可以使用這個來執行相同音訊上更可靠的關鍵字偵查器。

## <a name="next-steps"></a>後續步驟

- 請**參閱設計方針：** 我們的[設計指導方針](windows-voice-assistants-best-practices.md)會針對在 Windows 10 上提供語音啟用的最佳體驗，說明所需的關鍵工作。
- **流覽 [消費者入門] 頁面：** 從[這裡](how-to-windows-voice-assistants-get-started.md)開始，以取得在 Windows 上開始實行語音助理的步驟，從設定您的開發環境，到實施指南的介紹。
- **試用範例應用程式**：若要體驗這些功能搶先它，請造訪[UWP 語音助理範例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)頁面，並遵循步驟來取得執行中的範例用戶端。
