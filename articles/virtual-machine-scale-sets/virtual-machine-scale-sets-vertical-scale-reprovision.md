---
title: 垂直調整 Azure 虛擬機器擴展集
description: 如何垂直調整虛擬機器大小以回應 Azure 自動化的監視警示
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 69c613de02b9601966cae2d36c13428ca6c7becc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120992"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>使用虛擬機器擴展集垂直自動調整

這篇文章描述如何使用或不使用重新佈建以垂直調整 Azure [虛擬機器擴充集](https://azure.microsoft.com/services/virtual-machine-scale-sets/) 。 

垂直調整也稱為向相應增加** 和相應減少**，意味增加或減少虛擬機器 (VM) 大小以回應工作負載。 將此行為與[水平調整](virtual-machine-scale-sets-autoscale-overview.md) (也稱為相應放大** 和相應縮小**) 做比較，後者會根據工作負載改變虛擬機器數目。

重新佈建表示移除現有的 VM，並以新的 VM 取代它。 當您增加或減少虛擬機器擴展集中的虛擬機器大小時，在某些情況下您想要調整現有虛擬機器的大小並保留資料，而在其他情況下您需要部署具有新大小的新虛擬機器。 本文件涵蓋這兩種情況。

在下列情況下，垂直調整可能十分有用︰

* 內建在虛擬機器上的服務使用量過低 (例如在週末)。 減少 VM 大小可以降低每月成本。
* 增加 VM 大小以應付更大的需求，而不需要建立額外的 VM。

您可以根據虛擬機器擴展集的度量型警示來設定要觸發的垂直調整。 啟動警示時它就會引發 Webhook 來觸發 Runbook，可讓您調整相應增加和相應減少設定。 您可以依照下列步驟來設定垂直調整︰

1. 使用執行身分功能來建立 Azure 自動化帳戶。
2. 將虛擬機器擴展集的 Azure 自動化垂直調整大小 Runbook 匯入訂用帳戶。
3. 將 Webhook 加入您的 Runbook 中。
4. 使用 Webhook 通知將警示加入至您的虛擬機器擴展集。

> [!NOTE]
> 因為這是第一部虛擬機器大小的緣故，所以它可以調整的大小，會受限於目前虛擬機器部署所在之叢集中是否可使用其他大小。 本文所用的已發佈自動化 Runbook 中，已考量了這個情況，只會於下列成對的 VM 大小內調整大小。 這表示 Standard_D1v2 虛擬機器不會突然相應增加為 Standard_G5 或相應減少為 Basic_A0。 也不支援限制的虛擬機器大小相應增加/減少。 您可以在以下大小配對之間選擇調整︰
> 
> | 成對的調整 VM 大小 |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | 標準_D1 |標準_D4 |
> | 標準_D11 |標準_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>使用執行身分功能來建立 Azure 自動化帳戶
您需要做的第一件事是建立 Azure 自動化帳戶，以此帳戶裝載用來調整虛擬機器擴展集執行個體的 Runbook。 最近，[Azure 自動化](https://azure.microsoft.com/services/automation/)引進了「執行身分帳戶」功能，此功能可設定服務主體以便代表使用者自動執行 Runbook。 如需詳細資訊，請參閱

* [使用 Azure 執行身分帳戶驗證 Runbook](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>將 Azure 自動化垂直調整大小 Runbook 匯入訂用帳戶

Azure 自動化 Runbook 資源庫已發佈垂直調整虛擬機器擴展集所需的 Runbook。 若要將其匯入到您的訂用帳戶，請依照這篇文章的步驟︰

* [Azure 自動化的 Runbook 和模組資源庫](../automation/automation-runbook-gallery.md)

從 Runbooks 功能表選擇 [瀏覽資源庫] 選項︰

![要匯入的 Runbook][runbooks]

需要如下所示匯入 Runbook。 根據您要使用重新佈建來垂直調整，以選取 Runbook：

![Runbook 資源庫][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>將 Webhook 加入您的 Runbook 中

匯入 Runbook 之後，請將 Webhook 新增至 Runbook 中，使其可由虛擬機器擴展集所發出的警示加以觸發。 本文說明如何為 Runbook 建立 Webhook 的詳細資訊：

* [Azure 自動化 Webhook](../automation/automation-webhooks.md)

> [!NOTE]
> 關閉 Webhook 對話方塊之前，請務必複製 Webhook URI，因為下一節會需要此位址。
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>對虛擬機器擴展集新增警示

下面的 PowerShell 指令碼會顯示如何對虛擬機器擴展集新增警示。 請參閱下列文章，取得度量名稱以引發警示︰[Azure 監視器自動調整的常用度量](../azure-monitor/platform/autoscale-common-metrics.md)。

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> 建議您設定合理的警示時間範圍以避免頻繁觸發垂直調整，及任何相關聯的服務中斷。 請考慮至少 20-30 分鐘以上的時段。 如果需要避免任何中斷，請考慮水平調整。
> 
> 

如需如何建立警示的詳細資訊，請參閱下列文章：

* [Azure 監視器 PowerShell 快速入門範例](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure 監視器跨平台 CLI 快速入門範例](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>摘要

這篇文章示範簡單的垂直調整範例。 藉助這些建置組塊 (自動化帳戶、Runbook、Webhook、警示)，您可以連接各式各樣的事件與一組自訂的動作。

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
