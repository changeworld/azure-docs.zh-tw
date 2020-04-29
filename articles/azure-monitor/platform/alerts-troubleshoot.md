---
title: Azure 監視器警示和通知的疑難排解
description: Azure 監視器警示和可能解決方案的常見問題。
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132334"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>疑難排解 Azure 監視器警示中的問題 

本文討論 Azure 監視器警示的常見問題。

當您的監視資料中發現重要條件時，Azure 監視器警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 如需警示的詳細資訊，請參閱[Microsoft Azure 中的警示總覽](alerts-overview.md)。

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>我的警示上的動作或通知未如預期般運作

如果您可以在 Azure 入口網站中看到引發的警示，但其某些動作或通知有問題，請參閱下列各節。

## <a name="did-not-receive-expected-email"></a>未收到預期的電子郵件

如果您可以在 Azure 入口網站中看到引發的警示，但未收到您已設定過的電子郵件，請遵循下列步驟： 

1. **電子郵件是否由[動作規則](alerts-action-rules.md)隱藏**？ 

    按一下入口網站中引發的警示，然後查看 [歷程記錄] 索引標籤中隱藏的[動作群組](action-groups.md)，以進行檢查： 

    ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)

1. **動作的類型是「電子郵件 Azure Resource Manager 角色」嗎？**

    此動作只會查看位於訂用帳戶範圍的 Azure Resource Manager 角色指派，以及*使用者*類型的。  請確定您已在訂用帳戶層級指派角色，而不是在資源層級或資源群組層級。

1. **您的電子郵件伺服器和信箱是否接受外部電子郵件？**

    確認不會封鎖來自這三個位址的電子郵件：
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    內部郵寄清單或通訊群組清單通常會封鎖來自外部電子郵件地址的電子郵件。 您必須將上述電子郵件地址列入允許清單。  
    若要進行測試，請將一般工作電子郵件地址（而不是郵寄清單）新增至動作群組，並查看警示是否送達該電子郵件。 

1. **收件匣規則或垃圾郵件篩選器是否已處理電子郵件？** 

    確認沒有刪除這些電子郵件的收件匣規則，或將它們移至側邊資料夾。 例如，收件匣規則可能會攔截主旨中的特定寄件者或特定單字。

    此外，請檢查：
    
      - 電子郵件客戶程式的垃圾郵件設定（例如 Outlook、Gmail）
      - 電子郵件伺服器的寄件者限制/垃圾郵件設定/隔離設定（例如 Exchange、Office 365、G suite）
      - 您的電子郵件安全性設備（如 Barracuda、Cisco）的設定。 

1. **您不小心取消訂閱動作群組嗎？** 

    警示電子郵件會提供從動作群組取消訂閱的連結。 若要檢查您是否不小心取消訂閱此動作群組，您可以：

    1. 在入口網站中開啟動作群組，並查看 [狀態] 欄：

    ![動作群組狀態資料行](media/alerts-troubleshoot/action-group-status.png)

    2. 搜尋您的電子郵件以進行取消訂閱確認：

    ![已取消訂閱警示動作群組](media/alerts-troubleshoot/unsubscribe-action-group.png)

    若要再次訂閱–請使用您收到的取消訂閱確認電子郵件中的連結，或從動作群組移除電子郵件地址，然後再次將其重新加入。 
 
1. **因為有許多電子郵件傳送至單一電子郵件地址，所以您已將其評分為有限？** 

    電子郵件的[速率限制](alerts-rate-limiting.md)為每個電子郵件地址每小時不超過100個電子郵件。 如果您通過此閾值，則會捨棄額外的電子郵件通知。  檢查是否已收到一則訊息，指出您的電子郵件地址已暫時受到速率限制： 
 
   ![電子郵件速率限制](media/alerts-troubleshoot/email-paused.png)

   如果您想要在沒有速率限制的情況下收到大量通知，請考慮使用不同的動作，例如 webhook、邏輯應用程式、Azure 函式或自動化 runbook，它們都不會受到速率限制。 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>未收到預期的 SMS、語音通話或推播通知

如果您可以在入口網站中看到引發的警示，但未收到您已設定的 SMS、語音通話或推播通知，請遵循下列步驟： 

1. **動作[規則](alerts-action-rules.md)是否已隱藏動作？** 

    按一下入口網站中引發的警示，然後查看 [歷程記錄] 索引標籤中隱藏的[動作群組](action-groups.md)，以進行檢查： 

    ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)

   如果不小心，您可以修改、停用或刪除動作規則。
 
1. **SMS/voice：您的電話號碼是否正確？**

   在 [國家/地區代碼] 或 [電話號碼] 中，檢查 SMS 動作是否有打字錯誤。 
 
1. **SMS/voice：您已有速率限制嗎？** 

    SMS 和語音通話的速率限制為每個電話號碼每五分鐘不會有一個以上的通知。 如果您通過此閾值，將會捨棄通知。 

      - 語音通話–檢查您的通話記錄，並查看您在前五分鐘是否有不同的 Azure 呼叫。 
      - SMS-檢查您的 SMS 歷程記錄是否有訊息，指出您的電話號碼已受到速率限制。 

    如果您想要在沒有速率限制的情況下收到大量通知，請考慮使用不同的動作，例如 webhook、邏輯應用程式、Azure 函式或自動化 runbook，它們都不會受到速率限制。 
 
1. **SMS：您不小心取消訂閱動作群組嗎？**

    開啟您的 SMS 歷程記錄，並檢查您是否已選擇取消來自此特定動作群組的 SMS 傳遞（使用停用 action_group_short_name 回復），或從所有動作群組（使用停止回復）。 若要再次訂閱，請傳送相關的 SMS 命令（[啟用 action_group_short_name] 或 [啟動]），或從 [動作] 群組移除 [SMS] 動作，然後再次將它重新加入。  如需詳細資訊，請參閱[動作群組中的 SMS 警示行為](alerts-sms-behavior.md)。

1. **您是否不小心封鎖了電話上的通知？**

   大部分的行動電話都可讓您封鎖來自特定電話號碼或短代碼的電話或 SMS，或封鎖來自特定應用程式（例如 Azure 行動應用程式）的推播通知。 若要檢查您是否意外封鎖了電話上的通知，請搜尋您的電話作業系統和型號所特有的檔，或使用不同的電話和電話號碼進行測試。 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>需要觸發另一種類型的動作，但它不是 

如果您可以在入口網站中看到引發的警示，但其設定的動作並未觸發，請遵循下列步驟： 

1. **動作規則是否已隱藏動作？** 

    按一下入口網站中引發的警示，然後查看 [歷程記錄] 索引標籤中隱藏的[動作群組](action-groups.md)，以進行檢查： 

    ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)
 
    如果不小心，您可以修改、停用或刪除動作規則。 

1. **Webhook 不會觸發嗎？**

    1. **已封鎖來源 IP 位址嗎？**
    
       將呼叫 webhook 的[IP 位址](action-groups.md#action-specific-information)列入允許清單。

    1. **您的 webhook 端點是否正常運作？**

       請確認您設定的 webhook 端點是否正確，以及端點是否正常運作。 檢查您的 webhook 記錄或檢測其程式碼，以便您可以調查（例如，記錄傳入的裝載）。 

    1. **您呼叫的是時差或 Microsoft 小組嗎？**  
    每個端點都需要特定的 JSON 格式。 請遵循[這些指示](action-groups-logic-app.md)，改為設定邏輯應用程式動作。

    1. **您的 webhook 變得沒有回應或傳回錯誤？** 

        Webhook 回應的超時時間為10秒。 當下列 HTTP 狀態碼傳回時，webhook 呼叫將會重試兩次以上：408、429、503、504，或當 HTTP 端點沒有回應時。 第一次重試會在 10 秒後執行。 第二個和最後一個重試會在100秒後發生。 如果第二次重試失敗則任何動作群組在 30 分鐘內不會再呼叫端點。

## <a name="action-or-notification-happened-more-than-once"></a>動作或通知發生多次 

如果您收到警示的通知（例如電子郵件或 SMS）超過一次，或警示的動作（例如 webhook 或 Azure function）已觸發多次，請遵循下列步驟： 

1. **這真的是相同的警示嗎？** 

    在某些情況下，會在同一時間同時引發多個類似的警示。 因此，它可能會看似相同的警示多次觸發其動作。 例如，活動記錄警示規則可能設定為在事件啟動時引發，以及完成（成功或失敗）時，不會篩選 [事件狀態] 欄位。 

    若要檢查這些動作或通知是否來自不同的警示，請檢查警示詳細資料，例如其時間戳記和警示識別碼或其相互關聯識別碼。或者，請在入口網站中檢查引發的警示清單。 如果是這種情況，您將需要調整警示規則邏輯，或設定警示來源。 

1. **動作會在多個動作群組中重複嗎？** 

    當警示引發時，每個動作群組都會獨立處理。 因此，如果動作（例如電子郵件地址）出現在多個已觸發的動作群組中，每個動作群組就會呼叫一次。 

    若要檢查已觸發的動作群組，請核取 [警示歷程記錄] 索引標籤。您會看到警示規則中定義了兩個動作群組，以及由動作規則新增至警示的動作群組： 

    ![在多個動作群組中重複動作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>動作或通知有未預期的內容

如果您已收到警示，但認為其部分欄位遺失或不正確，請遵循下列步驟： 

1. **您是否為動作挑選正確的格式？** 

    每個動作類型（電子郵件、webhook 等）都有兩種格式：預設、舊版格式，以及[較新的通用架構格式](alerts-common-schema.md)。 當您建立動作群組時，會指定您想要的每個動作格式–動作群組中的不同動作可能會有不同的格式。 

    例如，針對 webhook 動作： 

    ![webhook 動作架構選項](media/alerts-troubleshoot/webhook.png)

    檢查在動作層級指定的格式是否符合您的預期。 例如，您可能已開發回應警示（webhook、函式、邏輯應用程式等）的程式碼，其應為一種格式，但稍後在動作中，或其他人指定了不同的格式。  

    此外，請檢查適用于[活動記錄警示](activity-log-alerts-webhook.md)的承載格式（JSON）、適用于計量[警示](alerts-metric-near-real-time.md#payload-schema)的[記錄搜尋警示](alerts-log-webhook.md)（Application Insights 和 log analytics）、[一般警示架構](alerts-common-schema-definitions.md)和已淘汰的傳統計量[警示](alerts-webhooks.md)。

 
1. **活動記錄警示：活動記錄中是否有可用的資訊？** 

    [活動記錄警示](activity-log-alerts.md)是以寫入 Azure 活動記錄的事件為基礎的警示，例如有關建立、更新或刪除 azure 資源的事件、服務健康情況和資源健康狀態事件，或是來自 Azure Advisor 和 Azure 原則的結果。 如果您收到以活動記錄為基礎的警示，但有些欄位遺失或不正確，請先檢查活動記錄檔中的事件。 如果 Azure 資源未在其活動記錄事件中寫入您要尋找的欄位，這些欄位將不會包含在對應的警示中。 

## <a name="action-rule-is-not-working-as-expected"></a>動作規則未如預期般運作 

如果您可以在入口網站中看到引發的警示，但相關動作規則未如預期般運作，請遵循下列步驟： 

1. **動作規則是否已啟用？** 

    檢查 [動作規則狀態] 欄，確認已啟用相關的動作角色。 

    ![圖形](media/alerts-troubleshoot/action-rule-status.png) 

    如果未啟用，您可以藉由選取並按一下 [啟用] 來啟用動作規則。 

1. **這是服務健康狀態警示嗎？** 

    服務健康狀態警示（監視器服務 = "服務健康狀態"）不會受到動作規則的影響。 

1. **動作規則是否對您的警示採取行動？** 

    按一下入口網站中引發的警示，檢查動作規則是否已處理警示，並查看 [歷程記錄] 索引標籤。

    以下是隱藏所有動作群組的動作規則範例： 
 
     ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)

    以下是新增另一個動作群組的動作規則範例：

    ![在多個動作群組中重複動作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **動作規則範圍和篩選準則是否符合引發的警示？** 

    如果您認為動作規則應該已引發但未引發，或不應該引發，但卻不應該引發，請仔細檢查動作規則範圍和篩選準則，與引發警示的屬性。 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>如何尋找所引發警示的警示識別碼

開啟特定引發警示的案例時（例如–如果您未收到其電子郵件通知），您將需要提供警示識別碼。 

若要找出它，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至引發的警示清單，並尋找該特定警示。 您可以使用篩選器來協助您找到它。 

1. 按一下警示以開啟 [警示詳細資料]。 

1. 在第一個索引標籤的 [警示] 欄位中向下滾動（[摘要] 索引標籤），直到您找到它並加以複製。 該欄位也包含您可以使用的 [複製到剪貼簿] 協助程式按鈕。  

    ![尋找警示識別碼](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>建立、更新或刪除 Azure 入口網站中的動作規則時發生問題

如果您在嘗試建立、更新或刪除[動作規則](alerts-action-rules.md)時收到錯誤，請遵循下列步驟： 

1. **您是否收到許可權錯誤？**  

    您應具有「 [監視參與者」內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)，或與動作規則和警示相關的特定許可權。

1. **您驗證了動作規則參數嗎？**  

    檢查[動作規則檔](alerts-action-rules.md)或[動作規則 PowerShell AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0)命令。 


## <a name="next-steps"></a>後續步驟
- 如果使用記錄警示，另請參閱[疑難排解記錄警示](alert-log-troubleshoot.md)。
- 回到[Azure 入口網站](https://portal.azure.com)，以檢查您是否已解決上述指引的問題 
