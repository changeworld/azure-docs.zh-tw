---
title: 開始使用 Advisor
description: 開始使用 Advisor。
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85117857"
---
# <a name="get-started-with-azure-advisor"></a>開始使用 Advisor

了解如何透過 Azure 入口網站存取 Advisor、取得建議，以及實作建議。

> [!NOTE]
> Azure Advisor 會自動在背景中執行，以尋找新建立的資源。 提供這些資源的建議，最多可能需要 24 小時。

## <a name="get-recommendations"></a>取得建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左窗格中，按一下 [Advisor]****。  如果在左窗格中沒有看到 [Advisor]，請按一下 [所有服務]****。  在服務功能表窗格中，於 [監視與管理]**** 底下，按一下 [Advisor]****。 隨即會顯示 Advisor 儀表板。

   ![使用 Azure 入口網站存取 Azure 建議程式](./media/advisor-get-started/advisor-portal-menu.png) 

1. Advisor 儀表板會顯示所有選取之訂用帳戶的建議摘要。  您可以使用訂用帳戶篩選下拉式清單，選擇您想要顯示建議的訂用帳戶。

1. 若要取得特定類別的建議，請按一下其中一個索引標籤： [**可靠性**]、[**安全性**]、[**效能**] 或 [**成本**]。 

   ![Azure 建議程式儀表板](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>取得建議詳細資料並實作解決方案

您可以選取 Advisor 中的建議來檢視其他詳細資料 (例如建議動作和受影響的資源)，以及實作建議的解決方案。  

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

1. 選取一個建議類別以顯示該類別內的建議清單，或選取 [全部]**** 索引標籤以檢視所有建議。

1. 按一下您想要詳細檢閱的建議。

1. 檢閱該建議的相關資訊，以及該建議適用的資源。

1. 按一下 [建議的動作]**** 以實作建議。

## <a name="filter-recommendations"></a>篩選建議

您可以篩選建議來向下鑽研至對您最重要的建議。  您可以依訂用帳戶、資源類型或建議狀態進行篩選。  

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

1. 使用 Advisor 儀表板上的下拉式清單來依訂用帳戶、資源類型或建議狀態進行篩選。

    ![Advisor 搜尋篩選條件](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>延期或解除建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

1. 瀏覽至您想要延期或關閉的建議。

1. 按一下該建議。

1. 按一下 [延期]****。 

1. 指定延期時間週期，或選取 [永不]**** 來關閉建議。

## <a name="exclude-subscriptions-or-resource-groups"></a>排除訂用帳戶或資源群組

您可能有不想要收到 Advisor 建議的資源群組或訂用帳戶，例如「測試」資源。  您可以將 Advisor 設定成只針對特定訂用帳戶和資源群組產生建議。

> [!NOTE]
> 若要將訂用帳戶或資源群組包含在 Advisor 中或從中排除，您必須是訂用帳戶「擁有者」。  如果您沒有訂用帳戶或資源群組的必要權限，將其包含或排除的選項在使用者介面中就會停用。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

1. 按一下動作列中的 [設定] ****。

1. 將所有您不想要收到 Advisor 建議的訂用帳戶或資源群組取消選取。

    ![Advisor 設定資源範例](./media/advisor-get-started/advisor-configure-resources.png)

1. 按一下 [套用]**** 按鈕。

## <a name="configure-low-usage-vm-recommendation"></a>設定低使用量 VM 建議

此程序針對低使用量虛擬機器建議設定平均 CPU 使用率規則。

Advisor 會監視您的虛擬機器使用量7天，然後找出低使用率的虛擬機器。 如果虛擬機器的 CPU 使用率為5% 或更低，而其網路使用率低於2%，或目前的工作負載可由較小的虛擬機器大小容納，則會視為低使用率。

如果您想要更積極地識別低使用量虛擬機器，您可以依據個別訂用帳戶調整平均 CPU 使用率規則。  CPU 使用率規則可以設定為5%、10%、15% 或20%。

> [!NOTE]
> 若要調整平均 CPU 使用率規則以識別低使用量虛擬機器，您必須是訂用帳戶「擁有者」**。  如果您沒有訂用帳戶或資源群組的必要權限，將其包含或排除的選項在使用者介面中就會停用。 

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

1. 按一下動作列中的 [設定] ****。

1. 按一下 [規則]**** 索引標籤。

1. 選取您要調整平均 CPU 使用率規則的訂用帳戶，然後按一下 [編輯]****。

1. 選取所需的平均 CPU 使用率值，然後按一下 [套用]****。

1. 按一下 [重新整理建議]****，以將現有的建議更新成使用新的平均 CPU 使用率規則。 

   ![Advisor 設定建議規則範例](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>下載建議

Advisor 可讓您下載建議摘要。  您可以將建議下載為 PDF 檔案或 CSV 檔案。  下載建議可讓您與同事輕鬆共用建議，或是在建議資料上執行您自己的分析。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

1. 按一下動作列上的 [下載為 CSV]**** 或 [下載為 PDF]****。

下載選項會遵守您已套用至 Advisor 儀表板的所有篩選。  如果您在檢視特定建議類別或建議時選取下載選項，則下載的摘要只會包含該類別或建議的資訊。 

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor，請參閱︰

- [Azure 建議程式簡介](advisor-overview.md)
- [Advisor 可靠性建議](advisor-high-availability-recommendations.md)
- [Advisor 安全性建議](advisor-security-recommendations.md)
- [建議程式效能建議](advisor-performance-recommendations.md)
- [Advisor 成本建議](advisor-cost-recommendations.md)
- [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
