---
title: 針對常見的 Azure Automanage 上線錯誤進行疑難排解
description: 常見的 Automanage 上線錯誤以及如何進行疑難排解
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 13388bf289c1d10a2e7da04a4dd5d26be109535e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697790"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>針對常見的 Automanage 上線錯誤進行疑難排解
Automanage 可能無法將機器上架到服務上。 本檔說明如何針對部署失敗進行疑難排解、共用部署可能失敗的一些常見原因，並說明緩和措施的潛在後續步驟。

## <a name="troubleshooting-deployment-failures"></a>針對部署失敗進行疑難排解
將機器上架至 Automanage 會導致建立 Azure Resource Manager 部署。 如果登入失敗，請參閱部署以取得詳細資料，以瞭解失敗的原因。 [失敗詳細資料] 飛出視窗中有部署的連結，如下圖所示。

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="Automanage 失敗詳細資料飛出視窗。":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>檢查包含失敗 VM 之資源群組的部署
失敗的飛出視窗將包含資源群組內的部署連結，此資源群組包含登入失敗的電腦，以及您可以用來篩選部署的首碼名稱。 按一下此連結將會帶您前往部署分頁，然後您可以在其中篩選部署，以查看電腦的 Automanage 部署。 如果您要跨多個區域進行部署，請確定您在正確的區域中按一下部署。

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>檢查包含失敗 VM 之訂用帳戶的部署
如果您在資源群組部署中沒看到任何失敗，則下一個步驟就是查看訂用帳戶中的部署，其中包含故障上架的 VM。 按一下失敗飛出視窗中的 [訂用帳戶的 **部署** ] 連結，然後使用 **Automanage-DefaultResourceGroup** 篩選器來篩選部署。 使用 [失敗] 分頁中的資源組名來篩選部署。 部署名稱的尾碼會是區功能變數名稱稱。 如果您要跨多個區域進行部署，請確定您在正確的區域中按一下部署。

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>檢查連結至 Log Analytics 工作區的訂用帳戶中的部署
如果您在資源群組或訂用帳戶中未看到包含失敗 VM 的任何失敗部署，且失敗的 VM 連線至不同訂用帳戶中的 Log Analytics 工作區，請移至連結至 Log Analytics 工作區的訂用帳戶，並檢查失敗的部署。

## <a name="common-deployment-errors"></a>一般部署錯誤

錯誤 |  降低
:-----|:-------------|
Automanage 帳戶許可權不足錯誤 | 如果您最近將包含新 Automanage 帳戶的訂用帳戶移至新的租使用者，則可能會發生這種情況。 解決此問題的步驟位於 [此處](./repair-automanage-account.md)。
工作區區域不符合區域對應需求 | Automanage 無法上架您的電腦，但電腦目前連結的 Log Analytics 工作區未對應至支援的自動化區域。 確定您現有的 Log Analytics 工作區和自動化帳戶位於支援的 [區域對應](https://docs.microsoft.com/azure/automation/how-to/region-mappings)中。
「指派失敗;沒有其他可用的資訊」 | Microsoft Azure 支援服務，請開啟案例。

## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [針對 Azure 入口網站中的虛擬機器啟用 Automanage](quick-create-virtual-machines-portal.md)

