---
title: Windows 上的語音助理-設計指導方針
titleSuffix: Azure Cognitive Services
description: 設計語音代理程式體驗時的最佳做法指導方針。
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165140"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Windows 10 的設計助理體驗

在 Windows 10 上開發的語音助理必須執行下列使用者經驗指導方針，以提供在 Windows 10 上語音啟用的最佳體驗。 本檔將引導開發人員瞭解語音助理與 Windows 10 Shell 整合所需的重要工作。

## <a name="contents"></a>目錄

- [Windows 10 支援的語音啟動視圖摘要](#summary-of-voice-activation-views-supported-in-windows-10)
- [需求摘要](#requirements-summary)
- [良好聆聽體驗的最佳作法](#best-practices-for-good-listening-experiences)
- [應用程式內語音啟用的設計指引](#design-guidance-for-in-app-voice-activation)
- [語音啟用高於鎖定的設計指導方針](#design-guidance-for-voice-activation-above-lock)
- [語音啟用預覽的設計指引](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Windows 10 支援的語音啟動視圖摘要

Windows 10 會根據裝置內容推斷客戶內容的啟用體驗。 下列摘要表格是螢幕開啟時可用之不同視圖的概要說明。

| View (Availability)  | 裝置內容 | 客戶目標 | 出現時間 | 設計需求 |
| --- | --- | --- | --- | --- |
| **應用程式內 (19H1) ** | 在鎖定下，assistant 具有焦點 | 與 assistant 應用程式互動 | Assistant 處理應用程式中的要求 | 主要的應用程式內視圖接聽體驗 |
| **上方鎖定 (19H2) ** | 高於鎖定，未驗證 | 與助理互動，但距離 | 系統已鎖定且助理要求啟用 | 適用于現場 UI 的全螢幕視覺效果。 執行關閉原則，不封鎖解除鎖定。 |
| **語音啟用預覽版 (20H1) ** | 低於鎖定，助理沒有焦點 | 與助理互動，但以較不具侵入性的方式進行互動 | 系統低於鎖定且助理要求背景啟用 | 基本畫布。 視需要調整主要應用程式視圖的大小或手。 |

## <a name="requirements-summary"></a>需求摘要

需要最少量的工作，才能存取不同的體驗。 不過，助理確實需要為每個觀點實行適當的設計指引。 下表提供必須遵循之需求的檢查清單。

| **語音啟動視圖** | **助理需求摘要** |
| --- | --- |
| **應用程式內** | <ul><li>處理應用程式內的要求</li><li>提供接聽狀態的 UI 指標</li><li>以視窗大小變更形式調整的 UI</li></ul> |
| **上方鎖定** | <ul><li>偵測鎖定狀態和要求啟用</li><li>請勿提供會封鎖 Windows 鎖定畫面存取的 always persistent UX</li><li>提供全螢幕視覺效果和語音優先體驗</li><li>遵循下列關閉指導方針</li><li>遵循下列隱私權和安全性考慮</li></ul> |
| **語音啟用預覽** | <ul><li>偵測解除鎖定狀態並要求背景啟用</li><li>在預覽窗格中繪製最基本的聆聽 UX</li><li>在右上角繪製一個接近的 X，並在按下滑鼠時停止串流音訊</li><li>視需要調整大小或遞交至主小幫手應用程式視圖，以提供解答</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>良好聆聽體驗的最佳作法

助理應建立接聽體驗來提供重要的意見反應，讓客戶可以瞭解助理的狀態。 以下是建立助理體驗時要考慮的一些可能狀態。 這些只是可能的建議，而不是必要的指引。

- 助理適用于語音輸入
- 小幫手正在啟用 (關鍵字或 mic 按鈕按下) 
- 助理正在主動將音訊串流處理至 assistant 雲端
- 助理已準備好讓客戶開始說話
- 小幫手聽到話說
- 助理瞭解客戶已完成演講
- 助理正在處理和準備回應
- 助理正在回應

即使狀態很快變更，仍值得考慮提供狀態的 UX，因為持續時間在 Windows 生態系統中是可變的。 視覺效果的意見反應以及簡短的音訊 chimes 或叫聲（也稱為 &quot; earcons &quot; ）都可以是解決方案的一部分。 同樣地，與音訊描述結合的視覺效果卡片也會產生良好的回應選項。

## <a name="design-guidance-for-in-app-voice-activation"></a>應用程式內語音啟用的設計指引

當小幫手應用程式具有焦點時，客戶意圖很清楚地與應用程式互動，因此所有語音啟動體驗都應由主要應用程式視圖處理。 客戶可以調整此視圖的大小。 為了協助說明助理 shell 互動，本檔的其餘部分會使用名為 Contoso 的金融服務助理的具體範例。 在這個和後續的圖表中，客戶所說的內容會顯示在左側的卡通語音反升圖中，並在右側的卡通反升圖中顯示助理回應。

**應用程式內視圖。語音啟用開始時的初始狀態：** 
 ![ 顯示 Contoso 財務助理應用程式的螢幕擷取畫面，其為預設的畫布。 右側的卡通語音反升會顯示「Contoso」。](media/voice-assistants/windows_voice_assistant/initial_state.png)

**應用程式內視圖。成功啟用語音之後，接聽體驗開始：** ![ 當語音助理正在接聽時，Windows 上的語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/listening.png)

**應用程式內視圖。所有回應都會保留在應用程式體驗中。** ![螢幕擷取畫面： Windows 上的語音助理回復](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>語音啟用高於鎖定的設計指導方針

可透過19H2 取得，以 Windows 語音啟用平臺為基礎的助理可用來回答上述鎖定。

### <a name="customer-opt-in"></a>客戶加入宣告

依預設，在鎖定之前的語音啟用一律會停用。 客戶透過 Windows 設定加入>隱私權>語音啟用。 如需監視和提示進行這種設定的詳細資訊，請參閱 [上述的鎖定執行指南](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)。

### <a name="not-a-lock-screen-replacement"></a>不是鎖定畫面取代

雖然通知或其他標準應用程式鎖定畫面整合點仍可供小幫手使用，但 Windows 鎖定畫面一律會定義初始客戶體驗，直到發生語音啟用為止。 偵測到語音啟用之後，assistant 應用程式會暫時出現在鎖定畫面上方。 為了避免客戶的混淆，當使用中的鎖定時，小幫手應用程式絕不能提供 UI 來要求任何種類的認證或登入。

![Windows 鎖定畫面的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>在語音啟用之後的鎖定體驗

當畫面開啟時，小幫手應用程式是全螢幕，在鎖定畫面上方沒有標題列。 較大的視覺效果和強式語音描述具有強大的語音主要介面，可讓客戶有太遠的距離無法讀取 UI，或有其他 (非電腦) 工作的情況。

當螢幕保持關閉時，小幫手應用程式可能會播放 earcon，表示助理正在啟用，並提供僅限語音的體驗。

![鎖定上方語音助理的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>關閉原則

小幫手必須執行本節中的關閉指引，讓客戶可以更輕鬆地在下一次想要使用 Windows 電腦時登入。 以下是助理必須執行的特定需求：

- **顯示在上方鎖定的所有小幫手畫布都必須包含** 右上方的 X，以關閉助理。
- **按下任何鍵也必須關閉 assistant 應用程式**。 鍵盤輸入是客戶想要登入的傳統鎖定應用程式信號。 因此，不應該將任何鍵盤/文字輸入導向至應用程式。 相反地，應用程式應該在偵測到鍵盤輸入時自行關閉，讓客戶可以輕鬆地登入其裝置。
- **如果畫面關閉，應用程式必須自行關閉。** 如此可確保下一次客戶使用其電腦時，登入畫面將會就緒，並等待他們。
- 如果應用程式正在 &quot; 使用中 &quot; ，它可能會繼續鎖定。 &quot;使用中的 &quot; 構成任何輸入或輸出。 例如，串流音樂或影片時，應用程式可能會繼續鎖定。 &quot;繼續進行 &quot; ，並允許其他多回合對話步驟，讓應用程式保持在鎖定的上方。
- 若要**關閉應用程式**，請參閱[上述「鎖定執行指南」中](windows-voice-assistants-implementation-guide.md#closing-the-application)的「執行」詳細資料。

![顯示 Contoso 財務助理應用程式上方鎖定視圖的螢幕擷取畫面。](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![桌面上顯示 Windows 鎖定畫面的螢幕擷取畫面。](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>&amp;高於鎖定的隱私權安全性考慮

許多電腦都是可移植的，但不一定會在客戶觸及範圍內。 它們可能會短暫留在旅館房間、飛機基座或工作區中，而其他人則有實體存取權。 如果啟用上述鎖定的助理未備妥，它們可能會受到所謂的 &quot; [惡意 maid](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot; 攻擊的類別。

因此，助理應遵循本節中的指導方針，以協助保持安全的體驗。 當 Windows 使用者未經驗證時，就會發生鎖定的互動。 這表示一般而言，對小幫手的 **輸入也應視為未經驗證**。

- 助理應該 **實行技能允許的清單，以識別已確認安全且安全** 地存取超過鎖定的技能。
- 說話者識別碼技術可以在減輕一些風險的情況下扮演角色，但說話者識別碼不是適合 Windows 驗證的替代方案。
- 「允許的技能」清單應考慮三個動作或技能類別：

| **Action 類別** | **說明** | ** (不是完整清單的範例) ** |
| --- | --- | --- |
| 無驗證的安全 | 一般用途資訊或基本應用程式命令和控制 | &quot;這是什麼時候？ &quot; 請 &quot; 播放下一個曲目&quot; |
| 使用說話者識別碼安全 | 模仿風險，洩漏個人資訊。 | &quot;我下一個約會&#39;？ &quot; ，請 &quot; 參閱我的購物清單 &quot; ， &quot; 回答來電&quot; |
| Windows 驗證後才安全 | 攻擊者可能用來傷害客戶的高風險動作 | &quot;購買更多 groceries &quot; 、 &quot; 刪除我的 (重要) 約會 &quot; 、 &quot; 傳送 (平均) 文字訊息 &quot; 、 &quot; 啟動 (惡意的) 網頁&quot; |

在 Contoso 的案例中，公開股票資訊的一般資訊安全，不需要驗證。 客戶特定資訊（例如所擁有的共用數量）可能會受到說話者識別碼的安全。 不過，若沒有 Windows 驗證，就不允許購買或銷售股票。

為了進一步保護體驗、 **網頁連結或其他應用程式對應用程式的啟動，Windows 會一直封鎖到客戶登入。** 在最後的手段緩和措施中，如果未及時解決嚴重的安全性問題，Microsoft 就會保留從啟用的助理清單中移除應用程式的權利。

## <a name="design-guidance-for-voice-activation-preview"></a>語音啟用預覽的設計指引

在鎖定下，當小幫手應用 _程式沒有焦點_ 時，Windows 會提供較不具干擾性的語音啟用 UI，以協助讓客戶保持在 flow 中。 尤其是在錯誤啟用的情況下，如果啟動完整的應用程式，則會造成高幹擾性的情況。 核心的概念是，每位小幫手都在 Shell 中有另一個首頁，也就是 assistant 工作列圖示。 發生背景啟用的要求時，會出現小幫手的小幫手工作列圖示。 助理應該會在此畫布中提供短暫的聆聽體驗。 處理要求之後，助理可以選擇調整此視圖的大小，以顯示內容中的答案，或將其主要的應用程式視圖遞交，以顯示更大、更詳細的視覺效果。

- 為了保持最小，預覽沒有標題列，因此 **助理必須在右上方繪製 X，以允許客戶關閉視圖。** 請參閱 [關閉應用程式](windows-voice-assistants-implementation-guide.md#closing-the-application) ，以在按下 [關閉] 按鈕時呼叫特定 api。
- 為了支援語音啟用預覽，助理可能會邀請客戶在第一次執行期間，將小幫手釘選到工作列。

**語音啟用預覽：初始狀態**

Contoso assistant 在工作列上有一個首頁：其 swirling、圓形圖示。

![Windows 上的語音助理作為工作列圖示預先啟用的螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**啟用時**，小幫手會要求背景啟用。 小幫手會提供一個小型預覽窗格 (預設寬度408和高度： 248) 。 如果伺服器端聲音啟用判斷出信號是假的，則可能會關閉此視圖以減少中斷。

![確認啟用時，Windows 上的語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**確認最終啟用時**，助理會顯示其接聽 UX。 助理一律必須在「語音啟用」預覽的右上方繪製一個「關閉 X」。

![螢幕擷取畫面： Windows 上的語音助理正在以 compact view 接聽](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

語音啟用預覽中可能會顯示**快速解答**。 TryResizeView 可讓助理要求不同的大小。

![螢幕擷取畫面： Windows 上的語音助理回復壓縮視圖](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**手**上。 在任何時間點，小幫手可能會離開其主要的應用程式視圖，以提供需要更多螢幕空間的詳細資訊、對話或解答。 請參閱 [從 compact View 轉換成完整視圖](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) 一節，以取得執行詳細資料。

![在展開壓縮視圖之前和之後，Windows 上的語音助理螢幕擷取畫面](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始開發您的語音助理](how-to-windows-voice-assistants-get-started.md)