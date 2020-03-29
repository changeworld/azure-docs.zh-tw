---
title: 使用 Azure 顧問提高 Azure 訂閱的操作功能
description: 使用 Advisor 優化 Azure 訂閱的卓越操作並成熟
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443081"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>借助 Azure 顧問實現卓越運營

Azure Advisor 卓越運營建議説明客戶提供流程和工作流效率、資源可管理性和部署最佳實踐。 您可以在"顧問"儀表板的 **"卓越運營"** 選項卡上從顧問那裡獲得這些建議。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>建立在 Azure 問題影響您時所要通知的 Azure 服務健康狀態警示

建議您設定當 Azure 問題影響您時所要通知的 Azure 服務健康狀態警示。 [Azure 服務健康狀態](https://azure.microsoft.com/features/service-health/)是一項免費服務，可在您受到 Azure 服務問題影響時，提供個人化的指導及支援。 Advisor 會識別沒有設定警示的訂用帳戶，並建議您建立一個警示。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>設計存儲帳戶以防止達到最大訂閱限制

Azure 區域每個訂閱最多可以支援 250 個存儲帳戶。 達到限制後，您將無法在該區域/訂閱組合中創建任何存儲帳戶。 Advisor 將檢查您的訂閱和表面建議，以便您為接近最大限制的任何存儲帳戶進行設計。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>請確定您在需要時可以存取「Azure Cloud Experts」

當您執行業務關鍵工作負載時，在有需要時能諮詢支援小組至關緊要。 Advisor 可識別支援計劃中不包含支援小組的潛在業務關鍵訂用帳戶，並建議升級至包含支援小組的選項。

## <a name="repair-invalid-log-alert-rules"></a>修復不正確日誌警報規則

Azure Advisor 將檢測在其條件部分中指定的無效查詢的警報規則。 記錄警示規則會在 Azure 監視器中建立，並可用來以指定間隔執行分析查詢。 查詢的結果會決定是否需要觸發警示。 隨著時間過去，分析查詢可能會因為所參照的資源、資料表或命令有所變更而變得無效。 Advisor 將建議您更正警報規則中的查詢，以防止其自動禁用，並確保監視 Azure 中資源的覆蓋範圍。 [瞭解有關故障排除警報規則的更多](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>使用 Azure 策略遵循最佳實踐

Azure 原則是 Azure 中的一個服務，您可以用來建立、指派和管理原則。 這些原則會對您的資源強制執行不同規則和影響。 以下是説明您實現運營出色的 Azure 策略建議： 
1. 使用 Azure 策略管理標記：此策略在創建或更新任何資源時添加或替換指定的標記和值。 您可以藉由觸發補救工作來補救現有的資源。 此外，這不會修改資源組上的標記。
2. 使用 Azure 策略強制實施地理合規性要求：該策略使您能夠限制組織在部署資源時可以指定的位置。 
3. 為部署指定允許的虛擬機器 SKU：此策略使您能夠指定組織可以部署的一組虛擬機器 SKU。
4. 使用 Azure 策略強制實施"不使用託管磁片的審核 VM"
5. 使用 Azure 策略使用"從資源組繼承標記"：當創建或更新任何資源時，策略會添加或替換父資源組中的指定標記和值。 您可以藉由觸發補救工作來補救現有的資源。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [入門](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
