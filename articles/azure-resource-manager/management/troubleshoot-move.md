---
title: 排除移動錯誤
description: 使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891267"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>針對將 Azure 資源移至新的資源群組或訂用帳戶的作業進行疑難排解

本文提供建議，以説明在移動資源時解決問題。

## <a name="upgrade-a-subscription"></a>升級訂閱

如果您實際上是想要升級 Azure 訂用帳戶 (例如從免費切換成隨用隨付)，則必須轉換您的訂用帳戶。

* 若要將免費試用升級，請參閱[將免費試用或 Microsoft Imagine Azure 訂用帳戶升級至隨用隨付](../../billing/billing-upgrade-azure-subscription.md)。
* 若要變更隨用隨付帳戶，請參閱[將您的 Azure 隨用隨付訂閱變更為其他供應項目](../../billing/billing-how-to-switch-azure-offer.md)。

如果您無法轉換訂用帳戶，請[建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 針對問題類型選取 [訂用帳戶管理]****。

## <a name="service-limitations"></a>服務限制

某些服務在移動資源時需要其他注意事項。 如果要移動以下服務，請確保檢查指南和限制。

* [應用程式服務](./move-limitations/app-service-move-limitations.md)
* [Azure 開發人員服務](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [經典部署模型](./move-limitations/classic-model-move-limitations.md)
* [網路功能](./move-limitations/networking-move-limitations.md)
* [復原服務](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [虛擬機器](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>大請求

可能的話，請將大型移動細分為個別的移動作業。 單一作業超過 800 個資源時，Resource Manager 會立即傳回錯誤。 不過，移動少於 800 個資源也可能因為逾時而失敗。

## <a name="resource-not-in-succeeded-state"></a>資源未處於成功狀態

當您收到一條錯誤訊息，指示資源由於未處於成功狀態而無法移動時，它實際上可能是阻止移動的從屬資源。 通常，錯誤代碼是**移動無法繼續與資源未成功狀態**。

如果源資源或目標資源組包含虛擬網路，則在移動過程中將檢查虛擬網路的所有從屬資源狀態。 該檢查包括直接或間接依賴于虛擬網路的資源。 如果這些資源中的任何一個處於失敗狀態，則移動將被阻止。 例如，如果使用虛擬網路的虛擬機器出現故障，則移動將被阻止。 即使虛擬機器不是要移動的資源之一，並且不在移動的資源組中，也會阻止移動。

收到此錯誤時，有兩個選項。 將資源移動到沒有虛擬網路的資源組，或者[與支援人員聯繫](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。
