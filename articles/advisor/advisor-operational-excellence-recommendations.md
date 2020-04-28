---
title: 使用 Azure Advisor 改善 Azure 訂用帳戶的操作 excellency
description: 使用 Advisor 優化您的 Azure 訂用帳戶，並獲得成熟的營運品質
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 4133fbe40c0fc1bdaf9c3f734bc19f94437c9acb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187411"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>透過 Azure Advisor 實現卓越的營運

Azure Advisor 操作卓越的建議可協助客戶處理流程和工作流程效率、資源管理性和部署最佳作法。 您可以在 Advisor 儀表板的 [**操作卓越**] 索引標籤上，從 [advisor] 取得這些建議。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>建立在 Azure 問題影響您時所要通知的 Azure 服務健康狀態警示

建議您設定當 Azure 問題影響您時所要通知的 Azure 服務健康狀態警示。 [Azure 服務健康狀態](https://azure.microsoft.com/features/service-health/)是一項免費服務，可在您受到 Azure 服務問題影響時，提供個人化的指導及支援。 Advisor 會識別沒有設定警示的訂用帳戶，並建議您建立一個警示。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>設計您的儲存體帳戶，以避免達到最大訂用帳戶限制

Azure 區域每個訂用帳戶最多可支援250個儲存體帳戶。 達到限制之後，您將無法在該區域/訂用帳戶組合中建立其他任何儲存體帳戶。 Advisor 將會檢查您的訂用帳戶和呈現建議，讓您針對任何接近達到上限的儲存體帳戶進行設計。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>請確定您在需要時可以存取「Azure Cloud Experts」

當您執行業務關鍵工作負載時，在有需要時能諮詢支援小組至關緊要。 Advisor 可識別支援計劃中不包含支援小組的潛在業務關鍵訂用帳戶，並建議升級至包含支援小組的選項。

## <a name="repair-invalid-log-alert-rules"></a>修復不正確記錄警示規則

Azure Advisor 將會偵測到其 [條件] 區段中指定了無效查詢的警示規則。 記錄警示規則會在 Azure 監視器中建立，並可用來以指定間隔執行分析查詢。 查詢的結果會決定是否需要觸發警示。 隨著時間過去，分析查詢可能會因為所參照的資源、資料表或命令有所變更而變得無效。 Advisor 會建議您更正警示規則中的查詢，以防止它自動停用，並確保您在 Azure 中的資源有涵蓋範圍。 [深入瞭解警示規則疑難排解](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>遵循使用 Azure 原則的最佳做法

Azure 原則是 Azure 中的一個服務，您可以用來建立、指派和管理原則。 這些原則會對您的資源強制執行不同規則和影響。 以下是可協助您達成操作 excellency 的 Azure 原則建議： 
1. 使用 Azure 原則管理標記：此原則會在建立或更新任何資源時，新增或取代指定的標記和值。 您可以藉由觸發補救工作來補救現有的資源。 此外，這不會修改資源群組上的標記。
2. 使用 Azure 原則強制執行地理合規性需求：原則可讓您限制組織在部署資源時可指定的位置。 
3. 指定部署所允許的虛擬機器 Sku：此原則可讓您指定您的組織可以部署的一組虛擬機器 Sku。
4. 使用 Azure 原則強制執行「稽核不是使用受控磁碟的 VM」
5. 使用 Azure 原則的「從資源群組繼承標記」：原則會在建立或更新任何資源時，從父資源群組新增或取代指定的標記和值。 您可以藉由觸發補救工作來補救現有的資源。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [開始使用](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
