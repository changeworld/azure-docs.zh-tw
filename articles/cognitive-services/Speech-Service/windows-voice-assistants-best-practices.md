---
title: Windows 上的語音助理-設計指導方針
titleSuffix: Azure Cognitive Services
description: 設計語音代理程式體驗時的最佳作法指導方針。
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 30df02062d3b94836f0131ac1124f56d1deefb5b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997772"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Windows 10 的設計助理體驗

在 Windows 10 上開發的語音助理必須實行下列使用者經驗指導方針，以提供 Windows 10 上語音啟用的最佳體驗。 本檔將引導開發人員瞭解語音助理與 Windows 10 Shell 整合所需的重要工作。

## <a name="contents"></a>內容

- [Windows 10 中支援的語音啟用視圖摘要](#summary-of-voice-activation-views-supported-in-windows-10)
- [需求摘要](#requirements-summary)
- [良好接聽體驗的最佳做法](#best-practices-for-good-listening-experiences)
- [應用程式內語音啟用的設計指導方針](#design-guidance-for-in-app-voice-activation)
- [在鎖定上方進行語音啟用的設計指導方針](#design-guidance-for-voice-activation-above-lock)
- [語音啟用預覽的設計指導方針](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Windows 10 中支援的語音啟用視圖摘要

Windows 10 會根據裝置內容來推斷客戶內容的啟用體驗。 下列摘要表是螢幕開啟時可用之不同視圖的高階總覽。

| View （可用性） | 裝置內容 | 客戶目標 | 出現的時機 | 設計需求 |
| --- | --- | --- | --- | --- |
| **應用程式內（19H1）** | 在鎖定下方，助理具有焦點 | 與 assistant 應用程式互動 | 助理會處理應用程式內的要求 | 主要的應用程式內視圖接聽體驗 |
| **上方鎖定（19H2）** | 以上鎖定，未經驗證 | 與助理互動，但距離 | 系統已鎖定且助理要求啟用 | 適用于最遠欄位 UI 的全螢幕視覺效果。 執行關閉原則，而不封鎖解除鎖定。 |
| **語音啟用預覽（20H1）** | 在鎖定之下，助理沒有焦點 | 與助理互動，但以較不幹擾的方式操作 | 系統低於鎖定和助理要求背景啟用 | 最小畫布。 視需要調整主要應用程式視圖的大小或交付。 |

## <a name="requirements-summary"></a>需求摘要

需要最少的工作，才能存取不同的體驗。 不過，助理確實需要針對每個視圖執行正確的設計指引。 下表提供必須遵循之需求的檢查清單。

| **語音啟用視圖** | **助理需求摘要** |
| --- | --- |
| **應用程式內** | <ul><li>處理應用程式內的要求</li><li>提供接聽狀態的 UI 指標</li><li>視窗大小變更時的 UI 適應</li></ul> |
| **高於鎖定** | <ul><li>偵測鎖定狀態和要求啟用</li><li>不要提供一律會封鎖存取 Windows 鎖定畫面的持續性 UX</li><li>提供全螢幕視覺效果和語音優先體驗</li><li>遵循以下的關閉指導方針</li><li>遵循下列隱私權和安全性考慮</li></ul> |
| **語音啟用預覽** | <ul><li>偵測解除鎖定狀態和要求背景啟用</li><li>在預覽窗格中繪製最少的接聽 UX</li><li>在右上方繪製一個收盤 X，並在按下時自行關閉並停止串流音訊</li><li>視需要調整大小或交付主要助理應用程式視圖以提供解答</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>良好接聽體驗的最佳做法

助理應建立接聽體驗來提供重要的意見反應，讓客戶可以瞭解助理的狀態。 以下是建立助理體驗時應考慮的一些可能狀態。 這些只是可能的建議，而不是強制性指引。

- Assistant 適用于語音輸入
- 小幫手正在啟用（按關鍵字或 mic 按鈕）
- 小幫手正在將音訊串流到 assistant 雲端
- 助理已準備好讓客戶開始說話
- 小幫手聽得到話
- 小幫手瞭解客戶已完成演講
- Assistant 正在處理和準備回應
- 助理正在回應

即使狀態變更很快，還是值得考慮提供各種狀態的 UX，因為持續時間在 Windows 生態系統中是可變的。 視覺效果的意見反應以及簡短的音訊 chimes 或鳴叫（ &quot;也&quot;稱為 earcons）都可以是解決方案的一部分。 同樣地，與音訊描述結合的視覺卡也適用于良好的回應選項。

## <a name="design-guidance-for-in-app-voice-activation"></a>應用程式內語音啟用的設計指導方針

當 assistant 應用程式有焦點時，客戶意圖會清楚地與應用程式互動，因此所有的語音啟用體驗都應該由主應用程式視圖來處理。 客戶可以調整此視圖的大小。 為了協助說明助理 shell 的互動，本檔的其餘部分會使用名為 Contoso 的金融服務助理的具體範例。 在此圖和後續的圖表中，客戶所說的內容會顯示在左側的卡通語音氣泡中，並在右側的卡通氣泡中出現小幫手回應。

**應用程式內視圖。語音啟動開始時的初始狀態：**
![啟用前的 Windows 語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/initial_state.png)

**應用程式內視圖。語音啟用成功之後，接聽體驗會開始：**![Windows 上的語音助理在語音助理接聽時的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/listening.png)

**應用程式內視圖。所有回應都會保留在應用程式體驗中。**![Windows 上的語音助理作為輔助程式回復的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>在鎖定上方進行語音啟用的設計指導方針

以19H2 提供，Windows 語音啟用平臺上建立的助理可用來回答上述鎖定。

> [!NOTE]
> 由於有作用中的問題，在上述鎖定 UI 上繪製的助理必須針對所有 dismissals 執行 WindowService Closewindowsg （）。 這會導致應用程式終止，但會降低技術問題，並讓小幫手保持正常狀態。 此外，若要在啟用以上鎖定語音啟動的應用程式時維持「清除」狀態，則當裝置鎖定時，必須接聽鎖定狀態變更和 WindowService。 Closewindowsg （）。

### <a name="customer-opt-in"></a>客戶加入宣告

預設會停用高於鎖定的語音啟用。 客戶透過 Windows 設定加入宣告>隱私權>語音啟用。 如需監視和提示此設定的詳細資訊，請參閱[上述的鎖定執行指南](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)。

### <a name="not-a-lock-screen-replacement"></a>不是鎖定畫面取代

雖然通知或其他標準應用程式鎖定的整合點仍可供小幫手使用，但 Windows 鎖定畫面一律會定義初始的客戶體驗，直到語音啟用發生為止。 在偵測到語音啟用之後，助理應用程式會暫時顯示在鎖定畫面上方。 為避免客戶的混淆，當使用上述鎖定時，小幫手應用程式絕不能呈現 UI 來要求任何類型的認證或登入。

![Windows 鎖定畫面的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>語音啟用後的鎖定體驗高於

當螢幕開啟時，小幫手應用程式是全螢幕，鎖定畫面上方沒有標題列。 使用強式語音主要介面的較大視覺效果和強式語音描述，可讓客戶太遠無法讀取 UI，或使用其他（非電腦）工作來進行操作。

當螢幕保持關閉時，小幫手應用程式可能會播放 earcon，表示小幫手正在啟用，並提供僅限語音的體驗。

![[語音助理] 上方鎖定的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>關閉原則

小幫手必須實行本節中的關閉指引，讓客戶在下一次想要使用他們的 Windows 電腦時，能夠更輕鬆地登入。 以下是助理必須執行的特定需求：

- 顯示在 [鎖定] 上方的所有小幫手**畫布都必須包含**右上角的 X，以關閉助理。
- **按任意鍵也必須關閉助理應用程式**。 鍵盤輸入是客戶想要登入的傳統鎖定應用程式信號。 因此，任何鍵盤/文字輸入都不應該導向至應用程式。 相反地，應用程式應該在偵測到鍵盤輸入時自行關閉，讓客戶能夠輕鬆登入其裝置。
- **如果畫面停止，應用程式必須自行關閉。** 這可確保下一次客戶使用其電腦時，登入畫面將會就緒，並等待他們。
- 如果應用程式正在&quot;使用&quot;中，它可能會繼續進行鎖定。 &quot;使用&quot;中構成任何輸入或輸出。 例如，串流音樂或影片時，應用程式可能會繼續鎖定。 &quot;請遵循&quot; ，並允許其他 multiturn 對話步驟讓應用程式保持在鎖定上方。
- 如需**關閉應用程式的執行詳細資料**，請參閱[上述鎖定執行指南](windows-voice-assistants-implementation-guide.md#closing-the-application)。

![啟用前的 Windows 語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![啟用前的 Windows 語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>高於&amp;鎖定的隱私權安全性考慮

許多電腦都是可移植的，但不一定是在客戶觸及範圍內。 他們可能會短暫留在旅館室、飛機基座或工作區中，而其他人則具有實體存取權。 如果在上述鎖定上啟用的助理未備妥，它們可能會受到所謂的&quot;[惡意 maid](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot;攻擊類別所限制。

因此，助理應遵循本節中的指導方針，以協助保持安全的體驗。 當 Windows 使用者未經驗證時，就會發生與鎖定的互動。 這表示在一般情況下，**助理的輸入也應該被視為未驗證**。

- 助理應**實行技能白名單，以識別已確認安全且可安全存取的技能，而不**是鎖定。
- 說話者識別碼技術可以在減輕部分風險的情況下扮演角色，但是說話者識別碼並不適合用來取代 Windows 驗證。
- 技能白名單應考慮三種動作或技能類別：

| **Action 類別** | **說明** | **範例（不是完整的清單）** |
| --- | --- | --- |
| 安全但不驗證 | 一般用途資訊或基本應用程式命令與控制 | &quot;現在 幾點鐘？&quot;， &quot;播放下一個曲目&quot; |
| 具有說話者識別碼的安全 | 模擬風險，並洩漏個人資訊。 | &quot;我的下一個約會是什麼&#39;？&quot;， &quot;請參閱我的&quot;購物&quot;清單，回答電話&quot; |
| 只有在 Windows 驗證之後才安全 | 攻擊者可能用來傷害客戶的高風險動作 | &quot;購買更多&quot;雜貨商店&quot;，刪除我的（重要&quot;） &quot;約會，傳送（mean）文字&quot;訊息&quot;，啟動（惡意）網頁&quot; |

在 Contoso 的案例中，公用股票資訊的一般資訊在沒有驗證的情況下是安全的。 客戶特定的資訊（例如所擁有的共用數目）可能會受到說話者識別碼的安全。 不過，在沒有 Windows 驗證的情況下，不允許購買或銷售股票。

為了進一步保護體驗、 **weblinks 或其他應用程式對應用程式的啟動，將一律會由 Windows 封鎖，直到客戶登入為止。** 作為最後手段緩和措施，如果未及時解決嚴重的安全性問題，Microsoft 會保留從啟用的助理白名單移除應用程式的權利。

## <a name="design-guidance-for-voice-activation-preview"></a>語音啟用預覽的設計指導方針

低於鎖定，當 assistant 應用程式沒有焦點時， _Windows 會提供_較不具干擾性的語音啟用 UI，協助客戶保持流程。 在啟用完整應用程式的情況下，如果錯誤啟動會造成高幹擾性，這特別適用。 核心的想法是，每個小幫手在 Shell 中都有另一個首頁，也就是 assistant 的工作列圖示。 當背景啟用的要求發生時，會出現在 [助理] 工作列圖示上方的小型視圖。 助理應該會在此畫布中提供小型的接聽體驗。 處理要求之後，助理可以選擇調整此視圖的大小，以顯示內容中的答案，或遞交主要的應用程式視圖來顯示更大、更詳細的視覺效果。

- 為了保持最少，預覽沒有標題列，因此小幫手**必須在右上方繪製 X，以允許客戶關閉此視圖。** 請參閱[關閉應用程式](windows-voice-assistants-implementation-guide.md#closing-the-application)，以在按下 [關閉] 按鈕時呼叫特定 api。
- 若要支援語音啟用預覽，助理可以邀請客戶在第一次執行期間將助理釘選到工作列。

**語音啟用預覽：初始狀態**

Contoso 助理在工作列上有一個首頁：其 swirling、圓形圖示。

![Windows 上的語音助理作為工作列圖示預先啟用的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**當啟用進行時**，小幫手會要求背景啟用。 小幫手會獲得一個小型預覽窗格（預設寬度408和高度：248）。 如果伺服器端語音啟動判斷信號為誤報，則可以關閉此視圖，以進行最少的中斷。

![在驗證啟用時，精簡視圖中 Windows 語音助理的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**確認最終啟用時**，助理會顯示其接聽 UX。 小幫手必須一律在語音啟用預覽的右上方繪製「關閉 X」。

![在 Windows 上以 compact view 接聽的語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

**快速解答**可能會顯示在語音啟用預覽中。 TryResizeView 可讓助理要求不同的大小。

![Windows 上語音助理的螢幕擷取畫面在 compact view 中回復](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**遞交**。 在任何時間點，小幫手可能會交給其主要應用程式視圖，以提供需要更多螢幕空間的詳細資訊、對話或答案。 如需執行詳細資料，請參閱[從 compact View 轉換至完整視圖](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view)一節。

![展開壓縮視圖前後的 Windows 語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始開發您的語音助理](how-to-windows-voice-assistants-get-started.md)