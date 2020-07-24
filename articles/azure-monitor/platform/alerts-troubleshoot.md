---
title: Azure 監視器警示和通知的疑難排解
description: Azure 監視器警示和可能解決方案的常見問題。
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: c3eed095d87d1e1fa1f210b360822ceefdbe0521
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045245"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>疑難排解 Azure 監視器警示中的問題

本文討論 Azure 監視器警示和通知的常見問題。

當您的監視資料中發現重要條件時，Azure 監視器警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 如需警示的詳細資訊，請參閱[Microsoft Azure 中的警示總覽](alerts-overview.md)。

如果您發生警示引發問題或未在預期時引發，請參閱下列文章。 您可以在 Azure 入口網站中看到「已引發」的警示。

- [針對 Microsoft Azure 中的 Azure 監視器計量警示進行疑難排解](alerts-troubleshoot-metric.md)  
- [針對 Microsoft Azure 中的 Azure 監視器記錄警示進行疑難排解](alerts-troubleshoot-metric.md)

如果警示根據 Azure 入口網站以預期的方式引發，但不會發生適當的通知，請使用本文其餘部分中的資訊來疑難排解該問題。

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>我的警示上的動作或通知未如預期般運作

如果您可以在 Azure 入口網站中看到引發的警示，但其某些動作或通知有問題，請參閱下列各節。

## <a name="did-not-receive-expected-email"></a>未收到預期的電子郵件

如果您可以在 Azure 入口網站中看到引發的警示，但未收到已設定的電子郵件，請遵循下列步驟：

1. **電子郵件是否由[動作規則](alerts-action-rules.md)隱藏**？

    按一下入口網站中引發的警示，然後查看記錄標籤中是否有隱藏的[動作群組](action-groups.md)：

    ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)

1. **動作的類型是「電子郵件 Azure Resource Manager 角色」嗎？**

    此動作只會查看位於訂用帳戶範圍的 Azure Resource Manager 角色指派，以及*使用者*類型的。  請確定您已在訂用帳戶層級而不是在資源層級或資源群組層級指派角色。

1. **您的電子郵件伺服器和信箱是否接受外部電子郵件？**

    確認不會封鎖來自這三個位址的電子郵件：
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    內部郵寄清單或通訊群組清單通常會封鎖來自外部電子郵件地址的電子郵件。 您必須允許來自上述電子郵件地址的郵件。  
    若要進行測試，請將一般工作電子郵件地址 (而非郵寄清單) 新增至動作群組，並查看警示是否送達該電子郵件。

1. **收件匣規則或垃圾郵件篩選器是否已處理電子郵件？**

    確認沒有刪除這些電子郵件的收件匣規則，或將其移至側邊資料夾。 例如，收件匣規則可能會攔截主旨中的特定寄件者或特定單字。

    此外，請檢查：

   - 電子郵件用戶端 (例如 Outlook、Gmail) 的垃圾郵件設定
      - 電子郵件伺服器 (例如 Exchange、Office 365、G-suite) 的寄件者限制/垃圾郵件設定/隔離設定
      - 您的電子郵件安全性設備（如 Barracuda、Cisco）的設定。

1. **您不小心取消訂閱動作群組嗎？**

    警示電子郵件會提供從動作群組取消訂閱的連結。 若要檢查您是否不小心取消訂閱此動作群組，您可以：

    1. 在入口網站中開啟動作群組，並查看 [狀態] 欄位：

    ![動作群組狀態欄位](media/alerts-troubleshoot/action-group-status.png)

    2. 搜尋您的電子郵件以確認取消訂閱：

    ![已取消訂閱警示動作群組](media/alerts-troubleshoot/unsubscribe-action-group.png)

    若要再次訂閱 – 請使用您所收到取消訂閱確認電子郵件中的連結，或從動作群組移除電子郵件地址，然後再次加以新增。 
 
1. **因為有許多電子郵件傳送至單一電子郵件地址，所以您已將其評分為有限？**

    電子郵件的[速率限制](alerts-rate-limiting.md)為每個電子郵件地址每小時不超過 100 封電子郵件。 如果您通過此閾值，則會捨棄額外的電子郵件通知。  請檢查您是否收到訊息，指出您的電子郵件地址已暫時受到速率限制： 
 
   ![電子郵件速率限制](media/alerts-troubleshoot/email-paused.png)

   如果想在沒有速率限制的情況下收到大量通知，請考慮使用其他動作，例如 Webhook、邏輯應用程式、Azure 函式或自動化 Runbook，這些動作都不會受到速率限制。 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>未收到預期的 SMS、語音通話或推播通知

如果您可以在入口網站中看到引發的警示，但未收到已設定的簡訊、語音電話或推播通知，請遵循下列步驟： 

1. **動作[規則](alerts-action-rules.md)是否已隱藏動作？**

    按一下入口網站中引發的警示，然後查看記錄標籤中是否有隱藏的[動作群組](action-groups.md)： 

    ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)

   如果是不小心隱藏的，您可以修改、停用或刪除動作規則。
 
1. **SMS/voice：您的電話號碼是否正確？**

   檢查國家/地區代碼或電話號碼的簡訊動作是否有打字錯誤。
 
1. **SMS/voice：您已有速率限制嗎？**

    簡訊和語音電話的速率限制為每個電話號碼每五分鐘不超過一則通知。 如果您通過此閾值，將不會發出通知。

      - 語音電話 - 檢查您的通話記錄，並查看您在前五分鐘是否接到 Azure 的其他來電。
      - 簡訊 - 請檢查您的簡訊記錄，是否有訊息指出您的電話號碼已限制速率。

    如果想在沒有速率限制的情況下收到大量通知，請考慮使用其他動作，例如 Webhook、邏輯應用程式、Azure 函式或自動化 Runbook，這些動作都不會受到速率限制。 
 
1. **SMS：您不小心取消訂閱動作群組嗎？**

    開啟您的 SMS 歷程記錄，並檢查您是否已選擇取消來自此特定動作群組的 SMS 傳遞（使用停用 action_group_short_name 回復），或從所有動作群組（使用停止回復）。 若要再次訂閱，請傳送相關的簡訊命令 (ENABLE action_group_short_name 或 START)，或從動作群組移除簡訊動作，然後再重新新增。  如需詳細資訊，請參閱[動作群組中的簡訊警示行為](alerts-sms-behavior.md)。

1. **您是否不小心封鎖了電話上的通知？**

   大部分的行動電話都可讓您封鎖來自特定電話號碼或短代碼的電話或簡訊，或封鎖來自特定應用程式 (例如 Azure 行動應用程式) 的推播通知。 若要檢查您是否意外封鎖了電話上的通知，請搜尋您電話作業系統和型號的特定文件，或使用不同的電話和電話號碼進行測試。

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>需要觸發另一種類型的動作，但它不是 
   
如果您可以在入口網站中看到引發的警示，但其設定的動作並未觸發，請遵循下列步驟：

1. **動作規則是否已隱藏動作？**

    按一下入口網站中引發的警示，然後查看記錄標籤中是否有隱藏的[動作群組](action-groups.md)：

    ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)
 
    如果是不小心隱藏的，您可以修改、停用或刪除動作規則。

1. **Webhook 不會觸發嗎？**

    1. **已封鎖來源 IP 位址嗎？**
    
       將 webhook 所呼叫的[IP 位址](action-groups.md#action-specific-information)從新增至您的允許清單。

    1. **您的 webhook 端點是否正常運作？**

       請確認您已設定的 Webhook 端點正確，且端點正常運作。 檢查您的 Webhook 記錄或檢測其程式碼，以便進行調查 (例如，記錄傳入的承載)。

    1. **您呼叫的是時差或 Microsoft 小組嗎？**  
    這些端點都需要特定的 JSON 格式。 請依照[這些指示](action-groups-logic-app.md)，改為設定邏輯應用程式動作。

    1. **您的 webhook 變得沒有回應或傳回錯誤？** 

        我們的 Webhook 回應逾時期限為 10 秒。 傳回下列 HTTP 狀態碼時，最多會重試 2 次額外的 Webhook 呼叫：408、429、503、504 或 HTTP 端點沒有回應時。 第一次重試會在 10 秒後執行。 第二次和最後一次重試會在 100 秒後執行。 如果第二次重試失敗則任何動作群組在 30 分鐘內不會再呼叫端點。

## <a name="action-or-notification-happened-more-than-once"></a>動作或通知發生多次 

如果您收到一次以上的警示通知 (例如電子郵件或簡訊)，或警示的動作 (例如 Webhook 或 Azure 函式) 已觸發多次，請遵循下列步驟： 

1. **這真的是相同的警示嗎？** 

    在某些情況下，會在大約同一時間同時引發多個類似的警示。 因此，可能看起來像相同的警示多次觸發其動作。 例如，活動記錄警示規則可能設定為在事件啟動和完成 (無論成功或失敗) 時引發；且不會篩選 [事件狀態] 欄位。 

    若要檢查這些動作或通知是否來自不同的警示，請檢查警示詳細資料，例如其時間戳記和警示識別碼或其相互關聯識別碼。或者，在入口網站中檢查引發的警示清單。 如果是這種情況，您將需要調整警示規則邏輯，或設定警示來源。 

1. **動作會在多個動作群組中重複嗎？** 

    當警示引發時，其每個動作群組都會獨立處理。 因此，如果動作 (例如電子郵件地址) 出現在多個已觸發的動作群組中，每個動作群組就會呼叫一次動作。 

    若要檢查已觸發的動作群組，請查看警示記錄索引標籤。您會看到警示規則中定義了兩個動作群組，以及藉由 動作規則新增至警示的動作群組： 

    ![在多個動作群組中重複動作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>動作或通知有未預期的內容

如果您已收到警示，但認為其部分欄位遺失或不正確，請遵循下列步驟： 

1. **您是否為動作挑選正確的格式？** 

    每個動作類型（電子郵件、webhook 等）都有兩種格式：預設、舊版格式，以及[較新的通用架構格式](alerts-common-schema.md)。 當您建立動作群組時，會針對每個動作指定您想要的格式 – 動作群組中的不同動作可能會有不同的格式。 

    例如，針對 webhook 動作： 

    ![webhook 動作架構選項](media/alerts-troubleshoot/webhook.png)

    檢查在動作層級指定的格式是否符合您的預期。 例如，您可能已開發回應警示 (Webhook、函式、邏輯應用程式等) 的程式碼，其預期為一種格式，但稍後在動作中，您或其他人指定了不同的格式。  

    此外，檢查適用於[活動記錄警示](activity-log-alerts-webhook.md)、適用於[記錄搜尋警示](alerts-log-webhook.md) (Application Insights 和記錄分析)、適用於[計量警示](alerts-metric-near-real-time.md#payload-schema)、適用於[常見的警示結構描述](alerts-common-schema-definitions.md)，以及適用於已淘汰的[傳統計量警示](alerts-webhooks.md)的承載格式 (JSON)。

 
1. **活動記錄警示：活動記錄中是否有可用的資訊？** 

    [活動記錄警示](activity-log-alerts.md)是以寫入 Azure 活動記錄的事件為基礎的警示，例如有關建立、更新或刪除 azure 資源、服務健康狀態和資源健康狀態事件的事件，或是來自 Azure Advisor 和 Azure 原則的結果。 如果您收到以活動記錄為基礎的警示，但您需要的部分欄位遺失或不正確，請先檢查活動記錄其中的事件。 如果 Azure 資源未在其活動記錄事件中寫入您要尋找的欄位，則這些欄位將不會包含在對應的警示中。 

## <a name="action-rule-is-not-working-as-expected"></a>動作規則未如預期般運作 

如果您可以在入口網站中看到引發的警示，但相關的動作規則未如預期般運作，請遵循下列步驟： 

1. **動作規則是否已啟用？** 

    檢查動作規則狀態欄，確認已啟用相關的動作角色。 

    ![圖形](media/alerts-troubleshoot/action-rule-status.png) 

    如果未啟用，您可以加以選取並按一下 [啟用] 來啟用動作規則。 

1. **這是服務健康狀態警示嗎？** 

    服務健康情況警示 (監視服務 =「服務健康情況」) 不會受到動作規則影響。 

1. **動作規則是否對您的警示採取行動？** 

    按一下入口網站中引發的警示，然後查看記錄標籤中的動作規則是否已處理您的警示。

    以下是隱藏所有動作群組的動作規則範例： 
 
     ![警示動作規則隱藏專案歷程記錄](media/alerts-troubleshoot/history-action-rule.png)

    以下是新增另一個動作群組的動作規則範例：

    ![在多個動作群組中重複動作](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **動作規則範圍和篩選準則是否符合引發的警示？** 

    如果您認為動作規則應該已引發但卻未引發，或不應該引發但卻已引發，請仔細檢查動作規則範圍和篩選準則，與引發警示的屬性。 


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

    您應具有「 [監視參與者」內建角色](../../role-based-access-control/built-in-roles.md#monitoring-contributor)，或與動作規則和警示相關的特定許可權。

1. **您驗證了動作規則參數嗎？**  

    檢查[動作規則檔](alerts-action-rules.md)或[動作規則 PowerShell AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule?view=azps-3.5.0)命令。 


## <a name="next-steps"></a>後續步驟
- 如果使用記錄警示，另請參閱[疑難排解記錄警示](./alerts-troubleshoot-log.md)。
- 回到[Azure 入口網站](https://portal.azure.com)，以檢查您是否已解決上述指引的問題 
