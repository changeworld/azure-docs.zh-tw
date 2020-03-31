---
title: 排除 Azure 監視器警報和通知的故障
description: Azure 監視器警報和可能的解決方案的常見問題。
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132334"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>在 Azure 監視器警報中排除問題 

本文討論 Azure 監視器警報中的常見問題。

Azure 監視器警報會在監視資料中找到重要條件時主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 有關警報的詳細資訊，請參閱 Microsoft [Azure 中的警報概述](alerts-overview.md)。

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>警報上的操作或通知未按預期工作

如果在 Azure 門戶中可以看到已觸發的警報，但某些操作或通知出現問題，請參閱以下部分。

## <a name="did-not-receive-expected-email"></a>未收到預期電子郵件

如果在 Azure 門戶中可以看到已觸發警報，但沒有收到已配置的電子郵件，請按照以下步驟操作： 

1. **電子郵件被[操作規則](alerts-action-rules.md)抑制嗎**？ 

    通過按一下門戶中的觸發警報進行檢查，並查看已抑制[操作組的](action-groups.md)歷史記錄選項卡： 

    ![警報操作規則抑制歷史記錄](media/alerts-troubleshoot/history-action-rule.png)

1. **操作類型是否"通過電子郵件發送 Azure 資源管理器角色"？**

    此操作僅查看訂閱範圍內的 Azure 資源管理器角色指派和*類型"使用者*"。  請確保在訂閱級別分配了角色，而不是在資源級別或資源組級別分配了角色。

1. **您的電子郵件伺服器和郵箱是否接受外部電子郵件？**

    驗證以下三個位址的電子郵件是否未被阻止：
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    通常，內部郵寄清單或通訊群組清單會阻止來自外部電子郵件地址的電子郵件。 您需要將上述電子郵件地址列入白名單。  
    要進行測試，請向操作組添加常規工作電子郵件地址（而不是郵寄清單），並查看警報是否到達該電子郵件。 

1. **電子郵件是由收件匣規則還是垃圾郵件篩選器處理？** 

    驗證沒有刪除這些電子郵件或將其移動到側資料夾的收件匣規則。 例如，收件匣規則可以捕獲主題中的特定寄件者或特定單詞。

    此外，請檢查：
    
      - 電子郵件用戶端的垃圾郵件設置（如 Outlook、Gmail）
      - 電子郵件伺服器的寄件者限制/垃圾郵件設置/隔離設置（如 Exchange、Office 365、G 套件）
      - 電子郵件安全設備的設置（如果有）（如梭子魚、思科）。 

1. **您是否意外取消訂閱操作組？** 

    警報電子郵件提供取消訂閱操作組的連結。 要檢查是否意外取消訂閱此操作組，請執行以下操作：

    1. 打開門戶中的操作組並檢查"狀態"列：

    ![操作組狀態列](media/alerts-troubleshoot/action-group-status.png)

    2. 搜索您的電子郵件以查找取消訂閱確認：

    ![取消訂閱警報操作組](media/alerts-troubleshoot/unsubscribe-action-group.png)

    要再次訂閱 - 要麼使用您收到的取消訂閱確認電子郵件中的連結，要麼從操作組中刪除電子郵件地址，然後再次添加。 
 
1. **您是否因許多電子郵件訪問單個電子郵件地址而被評為受限？** 

    電子郵件[的速率限制](alerts-rate-limiting.md)為每小時不超過 100 封電子郵件到每個電子郵件地址。 如果通過此閾值，將刪除其他電子郵件通知。  檢查您是否收到一條消息，指出您的電子郵件地址已暫時限制費率： 
 
   ![電子郵件速率受限](media/alerts-troubleshoot/email-paused.png)

   如果要在不限制速率的情況下接收大量通知，請考慮使用不同的操作，如 Webhook、邏輯應用、Azure 函數或自動化 Runbook，這些操作都不是速率限制的。 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>未收到預期的短信、語音電話或推送通知

如果在門戶中可以看到已觸發的警報，但沒有收到已配置的 SMS、語音電話或推送通知，請按照以下步驟操作： 

1. **[操作規則抑制了該操作嗎](alerts-action-rules.md)？** 

    通過按一下門戶中的觸發警報進行檢查，並查看已抑制[操作組的](action-groups.md)歷史記錄選項卡： 

    ![警報操作規則抑制歷史記錄](media/alerts-troubleshoot/history-action-rule.png)

   如果這是無意的，您可以修改、禁用或刪除操作規則。
 
1. **短信/語音：您的電話號碼是否正確？**

   檢查 SMS 操作中有關國家/地區代碼或電話號碼中的拼寫錯誤。 
 
1. **短信/語音：你被限制率嗎？** 

    短信和語音通話的速率限制為每個電話號碼每五分鐘不超過一次通知。 如果通過此閾值，通知將被刪除。 

      - 語音電話 – 檢查呼叫歷史記錄，並查看前五分鐘是否有來自 Azure 的不同呼叫。 
      - 短信 - 檢查您的短信歷史記錄，以顯示您的電話號碼已受到限制的短信。 

    如果要在不限制速率的情況下接收大量通知，請考慮使用不同的操作，如 Webhook、邏輯應用、Azure 函數或自動化 Runbook，這些操作都不是速率限制的。 
 
1. **短信：您是否意外取消訂閱操作組？**

    打開 SMS 歷史記錄，並檢查您是否退出宣告此特定操作組（使用禁用action_group_short_name回復）或所有操作組（使用停止回復）傳送簡訊。 要再次訂閱，請發送相關的 SMS 命令（啟用action_group_short_name或 START），或從操作組中刪除 SMS 操作，然後再次添加它。  有關詳細資訊，請參閱[操作組中的 SMS 警報行為](alerts-sms-behavior.md)。

1. **您是否不小心阻止了手機上的通知？**

   大多數行動電話都允許您阻止來自特定電話號碼或短代碼的呼叫或短信，或阻止來自特定應用（如 Azure 移動應用）的推送通知。 要檢查是否意外阻止了手機上的通知，請搜索特定于手機作業系統和型號的文檔，或使用其他電話和電話號碼進行測試。 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>預期將觸發另一種類型的操作，但它沒有 

如果在門戶中可以看到已觸發的警報，但其配置的操作未觸發，請按照以下步驟操作： 

1. **操作規則抑制了該操作嗎？** 

    通過按一下門戶中的觸發警報進行檢查，並查看已抑制[操作組的](action-groups.md)歷史記錄選項卡： 

    ![警報操作規則抑制歷史記錄](media/alerts-troubleshoot/history-action-rule.png)
 
    如果這是無意的，您可以修改、禁用或刪除操作規則。 

1. **網鉤沒有觸發嗎？**

    1. **源 IP 位址已被阻止嗎？**
    
       將網路鉤子的[IP 位址](action-groups.md#action-specific-information)列入白名單。

    1. **Webhook 終結點是否正常工作？**

       驗證已配置的 Webhook 終結點正確且終結點工作正常。 檢查 Webhook 日誌或檢測其代碼，以便進行調查（例如，記錄傳入的有效負載）。 

    1. **你是在給Slack或微軟團隊打電話？**  
    每個終結點都需要特定的 JSON 格式。 請按照[這些說明](action-groups-logic-app.md)配置邏輯應用操作。

    1. **您的 Webhook 是否變得無回應或返回錯誤？** 

        我們 Webhook 回應的超時時間是 10 秒。 返回以下 HTTP 狀態碼時，最多重試兩次 Webhook 調用：408、429、503、504 或 HTTP 終結點未回應。 第一次重試會在 10 秒後執行。 第二次也是最後一次重試在 100 秒後進行。 如果第二次重試失敗則任何動作群組在 30 分鐘內不會再呼叫端點。

## <a name="action-or-notification-happened-more-than-once"></a>多次執行操作或通知 

如果您多次收到警報通知（如電子郵件或 SMS），或者警報的操作（如 Webhook 或 Azure 函數）已多次觸發，請按照以下步驟操作： 

1. **真的是同一個警報嗎？** 

    在某些情況下，大約同時觸發多個類似的警報。 因此，它可能只是像同一個警報觸發了它的行動多次。 例如，活動日誌警報規則可能配置為在事件啟動時和事件完成（成功或失敗）時觸發，因為不篩選事件狀態欄位。 

    要檢查這些操作或通知是否來自不同警報，請檢查警報詳細資訊，例如其時間戳記和警報 ID 或其相關 ID。或者，檢查門戶中已觸發警報的清單。 如果是這種情況，則需要調整警報規則邏輯或以其他方式配置警報源。 

1. **該操作是否在多個操作組中重複？** 

    觸發警報時，將獨立處理其每個操作組。 因此，如果操作（如電子郵件地址）出現在多個觸發的操作組中，則每個操作組將調用一次。 

    要檢查觸發了哪些操作組，請檢查警報歷史記錄選項卡。您將看到警報規則中定義的操作組和按操作規則添加到警報的操作組： 

    ![在多個操作組中重複的操作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>操作或通知具有意外內容

如果您已收到警報，但認為其某些欄位丟失或不正確，請按照以下步驟操作： 

1. **您是否為操作選擇了正確的格式？** 

    每個操作類型（電子郵件、Webhook 等）有兩種格式 - 預設格式、舊格式和[較新的通用架構格式](alerts-common-schema.md)。 創建操作組時，可以指定每個操作所需的格式 - 操作組中的不同操作可能具有不同的格式。 

    例如，對於 Webhook 操作： 

    ![webhook 操作架構選項](media/alerts-troubleshoot/webhook.png)

    檢查在操作級別指定的格式是否達到預期。 例如，您可能已經開發了回應警報（Webhook、函數、邏輯應用等）的代碼，期望使用一種格式，但在稍後的操作中，您或其他人指定了不同的格式。  

    此外，檢查活動[日誌警報](activity-log-alerts-webhook.md)的有效負載格式 （JSON），檢查[日誌搜索警報](alerts-log-webhook.md)（應用程式見解和日誌分析）、[指標警報](alerts-metric-near-real-time.md#payload-schema)、[常見警報架構](alerts-common-schema-definitions.md)以及棄用[的經典指標警報](alerts-webhooks.md)。

 
1. **活動日誌警報：活動日誌中的資訊是否可用？** 

    [活動日誌警報](activity-log-alerts.md)是基於寫入 Azure 活動日誌的事件的警報，例如有關創建、更新或刪除 Azure 資源的事件、服務運行狀況和資源運行狀況事件，或 Azure 顧問和 Azure 策略的調查結果。 如果收到基於活動日誌的警報，但需要的某些欄位丟失或不正確，請先檢查活動日誌本身中的事件。 如果 Azure 資源未在其活動日誌事件中寫入要查找的欄位，則這些欄位將不會包含在相應的警報中。 

## <a name="action-rule-is-not-working-as-expected"></a>操作規則未按預期工作 

如果在門戶中可以看到已觸發的警報，但相關操作規則未按預期工作，請按照以下步驟操作： 

1. **操作規則是否啟用？** 

    檢查操作規則狀態列以驗證是否啟用了相關操作角色。 

    ![圖形](media/alerts-troubleshoot/action-rule-status.png) 

    如果未啟用，則可以通過選擇操作規則並按一下"啟用"來啟用該操作規則。 

1. **是服務運行狀況警報嗎？** 

    服務運行狀況警報（監視器服務 ="服務運行狀況"）不受操作規則的影響。 

1. **操作規則對警報起作用了嗎？** 

    通過按一下門戶中的已觸發警報，檢查操作規則是否處理了警報，並查看歷史記錄選項卡。

    下面是禁止所有操作組的操作規則的示例： 
 
     ![警報操作規則抑制歷史記錄](media/alerts-troubleshoot/history-action-rule.png)

    下面是添加另一個操作組的操作規則的示例：

    ![在多個操作組中重複的操作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **操作規則範圍和篩選器是否與觸發的警報匹配？** 

    如果您認為操作規則應該已觸發，但未觸發，或者它不應觸發，但確實觸發了，請仔細檢查操作規則範圍和篩選準則與觸發警報的屬性。 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>如何查找已觸發警報的警報 ID

打開有關特定觸發警報的案例時（例如，如果您未收到其電子郵件通知），則需要提供警報 ID。 

要找到它，請按照以下步驟操作：

1. 在 Azure 門戶中，導航到已觸發警報的清單，並查找該特定警報。 您可以使用篩選器來説明找到它。 

1. 按一下警報以打開警報詳細資訊。 

1. 向下滾動到第一個選項卡（摘要選項卡）的警報欄位中，直到找到它，然後複製它。 該欄位還包括一個"複製到剪貼簿"説明器按鈕，您可以使用。  

    ![查找警報 ID](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>在 Azure 門戶中創建、更新或刪除操作規則時遇到問題

如果在嘗試創建、更新或刪除[操作規則](alerts-action-rules.md)時收到錯誤，請按照以下步驟操作： 

1. **您是否收到許可權錯誤？**  

    您應該具有 [監視參與者內置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)，或者具有與操作規則和警報相關的特定許可權。

1. **是否驗證操作規則參數？**  

    檢查[操作規則文檔](alerts-action-rules.md)，或[操作規則 PowerShell 集-AzActionRule 命令](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0)。 


## <a name="next-steps"></a>後續步驟
- 如果使用日誌警報，請參閱[故障排除日誌警報](alert-log-troubleshoot.md)。
- 返回 Azure[門戶](https://portal.azure.com)，檢查是否通過上述指南解決了問題 
