---
title: 使用 Advisor 改善操作 excellency
description: 使用 Azure Advisor，為您的 Azure 訂用帳戶優化並成熟您的營運卓越。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 036adb7e7d59bd78980c72b210ad41faea277d00
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258487"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>使用 Azure Advisor 實現卓越的營運

Azure Advisor 中的操作卓越建議可協助您： 
- 進程和工作流程效率。
- 資源管理能力。
- 部署最佳作法。 

您可以在 Advisor 儀表板的 [ **操作卓越** ] 索引標籤上取得這些建議。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>建立 Azure 服務健康狀態警示，以在 Azure 問題影響您時收到通知

我們建議您設定 Azure 服務健康狀態警示，以便在 Azure 服務問題影響您時收到通知。 [Azure 服務健康狀態](https://azure.microsoft.com/features/service-health/) 是一項免費服務，可在您受到 Azure 服務問題的影響時，提供個人化的指引和支援。 Advisor 會識別未設定警示的訂用帳戶，並建議您設定這些訂閱。


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>設計您的儲存體帳戶，以避免達到最大訂用帳戶限制

Azure 區域每個訂用帳戶最多可支援250個儲存體帳戶。 達到該限制之後，您將無法在該區域/訂用帳戶組合中建立儲存體帳戶。 Advisor 會檢查您的訂用帳戶，並提供建議，讓您針對接近此限制的任何區域/訂用帳戶，設計較少的儲存體帳戶。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>請確定您在需要時可以存取「Azure Cloud Experts」

當您執行商務關鍵性工作負載時，請務必能夠在需要時存取技術支援。 Advisor 會識別其支援方案中沒有包含技術支援的潛在業務關鍵訂閱。 建議您升級至包含技術支援的選項。

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>刪除並重新建立您的集區，以移除已淘汰的內部元件

如果您的集區使用已被取代的內部元件，請刪除並重新建立集區，以提升穩定性和效能。

## <a name="repair-invalid-log-alert-rules"></a>修復不正確記錄警示規則

Azure Advisor 偵測到其 [條件] 區段中指定了無效查詢的警示規則。 您可以在 Azure 監視器中建立記錄警示規則，並使用它們在指定的間隔執行分析查詢。 查詢的結果會決定是否需要觸發警示。 分析查詢可能會因為參考的資源、資料表或命令的變更而在一段時間後失效。 Advisor 建議您更正警示規則中的查詢，以防止它被自動停用，並確保在 Azure 中監視資源的涵蓋範圍。 [深入瞭解警示規則的疑難排解。](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>使用 Azure 原則建議

Azure 原則是 Azure 中的一項服務，可讓您用來建立、指派和管理原則。 這些原則會對您的資源強制執行規則和效果。 下列 Azure 原則建議可協助您達成操作 excellency： 

**管理標記。** 此原則會在建立或更新任何資源時，新增或取代指定的標記和值。 您可以藉由觸發補救工作來補救現有的資源。 此原則不會修改資源群組上的標記。

**強制執行地理合規性需求。** 此原則可讓您限制您的組織在部署資源時可指定的位置。 

**指定部署所允許的虛擬機器 Sku。** 此原則可讓您指定一組組織所能部署的虛擬機器 SKU。

**強制 *執行未使用受控磁片的 Audit vm*。**

**[啟用] 會 *從資源群組繼承標記*。** 此原則會在建立或更新任何資源時，從父代資源群組中新增或取代指定的標記和值。 您可以藉由觸發補救工作來補救現有的資源。

## <a name="no-validation-environment-enabled"></a>未啟用任何驗證環境
Azure Advisor 判斷您未在目前的訂用帳戶中啟用驗證環境。 建立您的主機集區時，您 \" \" \" 在 [屬性] 索引標籤中選取 [否] 做為驗證環境 \" 。至少有一個已啟用驗證環境的主機集區，可透過在早期偵測潛在問題的情況下 Windows 虛擬桌面服務部署，確保商務持續性。 [深入了解](https://docs.microsoft.com/azure/virtual-desktop/create-validation-host-pool)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>確保生產 (非驗證) 環境，以從穩定的功能獲益
Azure Advisor 偵測到您的主機集區太多，已啟用驗證環境。 為了讓驗證環境最適合其用途，您應該至少有一個，但在驗證環境中不能超過一半的主機集區。 藉由在已啟用驗證環境的主機集區與已停用的使用者之間取得狀況良好的平衡，您將能充分利用多階段部署的優點，Windows 虛擬桌面提供特定的更新。 若要修正此問題，請開啟主機集區的內容，然後 \" \" 在 [ \" 驗證環境] 設定旁選取 [否] \" 。

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>啟用流量分析以深入了解 Azure 資源間的流量模式
流量分析是一個雲端式解決方案，可顯示 Azure 中的使用者和應用程式活動。 流量分析可分析網路監看員網路安全性群組 (NSG) 流量記錄，讓您深入了解流量。 透過流量分析，您可以檢視 Azure 和非 Azure 部署間的熱門 IP、調查環境中的開放連接埠、通訊協定和惡意流程，並將您的網路部署最佳化以提升效能。 您可以用 10 分鐘和 60 分鐘的處理間隔來處理流量記錄，以更快速地分析流量。 為您的 Azure 資源啟用流量分析是很好的作法。 


## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [開始使用](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 效能建議](advisor-performance-recommendations.md)
* [Advisor 可靠性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
