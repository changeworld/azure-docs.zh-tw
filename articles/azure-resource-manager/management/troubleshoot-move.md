---
title: 移動錯誤的疑難排解
description: 使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75891267"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>針對將 Azure 資源移至新的資源群組或訂用帳戶的作業進行疑難排解

本文提供的建議可協助您解決移動資源時的問題。

## <a name="upgrade-a-subscription"></a>升級訂閱

如果您實際上是想要升級 Azure 訂用帳戶 (例如從免費切換成隨用隨付)，則必須轉換您的訂用帳戶。

* 若要將免費試用升級，請參閱[將免費試用或 Microsoft Imagine Azure 訂用帳戶升級至隨用隨付](../../billing/billing-upgrade-azure-subscription.md)。
* 若要變更隨用隨付帳戶，請參閱[將您的 Azure 隨用隨付訂閱變更為其他供應項目](../../billing/billing-how-to-switch-azure-offer.md)。

如果您無法轉換訂用帳戶，請[建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 針對問題類型選取 [訂用帳戶管理]****。

## <a name="service-limitations"></a>服務限制

在移動資源時，某些服務需要額外的考慮。 如果您要移動下列服務，請務必檢查指引和限制。

* [應用程式服務](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [傳統部署模型](./move-limitations/classic-model-move-limitations.md)
* [網路功能](./move-limitations/networking-move-limitations.md)
* [[復原服務]](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [虛擬機器](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>大型要求

可能的話，請將大型移動細分為個別的移動作業。 單一作業超過 800 個資源時，Resource Manager 會立即傳回錯誤。 不過，移動少於 800 個資源也可能因為逾時而失敗。

## <a name="resource-not-in-succeeded-state"></a>資源不是處於成功狀態

當您收到錯誤訊息，指出無法移動資源，因為它不是處於成功狀態，實際上可能是封鎖移動的相依資源。 通常，錯誤碼是**MoveCannotProceedWithResourcesNotInSucceededState**。

如果來源或目標資源群組包含虛擬網路，則會在移動期間檢查虛擬網路的所有相依資源的狀態。 此檢查包含直接且間接相依于虛擬網路的這些資源。 如果其中任何一項資源處於失敗狀態，則會封鎖移動。 例如，如果使用虛擬網路的虛擬機器失敗，則移動會遭到封鎖。 即使虛擬機器不是其中一項要移動的資源，而且不在移動的其中一個資源群組中，移動也會遭到封鎖。

當您收到此錯誤時，您有兩個選項。 請將您的資源移至沒有虛擬網路的資源群組，或[聯絡支援](../../azure-portal/supportability/how-to-create-azure-support-request.md)人員。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。
