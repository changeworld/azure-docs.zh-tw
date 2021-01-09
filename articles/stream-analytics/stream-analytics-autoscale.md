---
title: 自動調整串流分析作業
description: 本文說明如何根據預先定義的排程或作業計量值來自動調整串流分析作業
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016281"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>使用 Azure 自動化自動調整串流分析作業

您可以設定自動調整，以優化串流分析作業的成本。 自動調整會增加或減少您作業的串流單位 (su) ，以符合輸入負載的變更。 您可以視需要擴大或縮小工作，而不是過度布建您的作業。 有兩種方式可以設定要自動調整的作業：
1. 當您有可預測的輸入負載時，**預先定義排程**。 例如，您預期日間期間的輸入事件比率較高，而且您想要使用更多 su 來執行您的作業。
2. 當您沒有可預測的輸入負載時，**根據工作計量觸發擴大和縮小作業**。 您可以根據您的工作計量（例如輸入事件的數目或囤積的輸入事件），以動態方式變更 SUs 數目。

## <a name="prerequisites"></a>必要條件
開始設定作業的自動調整之前，請先完成下列步驟。
1. 您的作業已優化，可擁有 [平行拓撲](./stream-analytics-parallelization.md)。 如果您可以在作業執行時變更作業的規模，則您的作業會有平行拓撲，而且可以設定為自動調整。
2. 建立已啟用 "RunAsAccount" 選項的[Azure 自動化帳戶](../automation/automation-create-standalone-account.md)。 此帳戶必須具有管理串流分析作業的許可權。

## <a name="set-up-azure-automation"></a>設定 Azure 自動化
### <a name="configure-variables"></a>設定變數
在 Azure 自動化帳戶內新增下列變數。 這些變數將用於後續步驟所述的 runbook 中。

| 名稱 | 類型 | 值 |
| --- | --- | --- |
| **jobName** | String | 您要自動調整的串流分析作業名稱。 |
| **resourceGroupName** | String | 工作所在資源群組的名稱。 |
| **subId** | String | 您的作業存在的訂用帳戶識別碼。 |
| **increasedSU** | 整數 | 您希望作業依排程調整為較高的 SU 值。 此值必須是您在作業執行時的 **調整規模** 設定中看到的有效 SU 選項之一。 |
| **decreasedSU** | 整數 | 您希望工作依排程調整的較低 SU 值。 此值必須是您在作業執行時的 **調整規模** 設定中看到的有效 SU 選項之一。 |
| **maxSU** | 整數 | 當負載自動調整時，您要讓作業在步驟中調整的最大 SU 值。 此值必須是您在作業執行時的 **調整規模** 設定中看到的有效 SU 選項之一。 |
| **minSU** | 整數 | 當負載自動調整時，您要讓作業在步驟中調整規模的最小 SU 值。 此值必須是您在作業執行時的 **調整規模** 設定中看到的有效 SU 選項之一。 |

![在 Azure 自動化中新增變數](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>建立 Runbook
下一步是建立兩個 PowerShell runbook。 一個用於擴大，另一個用於縮小作業。
1. 在 Azure 自動化帳戶中，移至 [程式 **自動化**] 下的 [ **runbook** ]，然後選取 [**建立 Runbook**]。
2. 將類型設定為 PowerShell 的第一個 runbook *ScaleUpRunbook* 命名為。 使用 GitHub 中提供的 [ScaleUpRunbook PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) 。 儲存併發布它。
3. 使用 PowerShell 類型建立另一個名為 *ScaleDownRunbook* 的 runbook。 使用 GitHub 中提供的 [ScaleDownRunbook PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) 。 儲存併發布它。

![Azure 自動化中的自動調整 runbook](./media/autoscale/runbooks.png)

您現在有可在串流分析作業上自動觸發擴大和縮小作業的 runbook。 您可以使用預先定義的排程來觸發這些 runbook，也可以根據作業計量動態設定這些 runbook。

## <a name="autoscale-based-on-a-schedule"></a>根據排程自動調整
Azure 自動化可讓您設定排程來觸發您的 runbook。
1. 在您的 Azure 自動化帳戶中 **，選取 [** **共用資源**] 底下的 [排程]。 然後，選取 [ **新增排程**]。
2. 例如，您可以建立兩個排程。 當您想要讓作業擴大，而另一個代表當您想要讓作業縮小時，會顯示一個。 您可以定義這些排程的週期。

   ![Azure 自動化中的排程](./media/autoscale/schedules.png)

3. 開啟您的 **ScaleUpRunbook** ，然後 **選取** [ **資源**] 底下的 [排程]。 然後，您可以將 runbook 連結至您在先前步驟中所建立的排程。 您可以有多個與相同 runbook 連結的排程，當您想要在一天中的不同時間執行相同的調整作業時，這會很有説明。

![在 Azure 自動化中排程 runbook](./media/autoscale/schedulerunbook.png)

1. 針對 **ScaleDownRunbook** 重複上述步驟。

## <a name="autoscale-based-on-load"></a>根據負載自動調整
您可能會在某些情況下無法預測輸入負載。 在這種情況下，最適合在系結的最小和最大值範圍內擴大/縮小。 您可以在串流分析作業中設定警示規則，以在工作計量超過或低於臨界值時觸發 runbook。
1. 在您的 Azure 自動化帳戶中，建立兩個名為 **minSU** 和 **maxSU** 的整數變數。 這會設定您的作業在各步驟中進行調整的範圍。
2. 建立兩個新的 runbook。 您可以使用 [StepScaleUp PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) ，以遞增方式增加作業的 su，直到 **maxSU** 值為止。 您也可以使用 [StepScaleDown PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) 來減少作業的 su，直到達到 **minSU** 值為止。 或者，如果您有想要調整的特定 SU 值，您可以使用上一節中的 runbook。
3. 在您的串流分析作業中，選取 [**監視**] 底下的 [**警示規則**]。 
4. 建立兩個動作群組。 一個用來進行擴大作業，另一個用於縮小運算。 選取 [ **管理動作** ]，然後按一下 [ **新增動作群組**]。 
5. 填寫必要的欄位。 當您選取 [**動作類型**] 時，請選擇 [**自動化 Runbook** ]。 選取警示引發時所要觸發的 runbook。 然後，建立動作群組。

   ![建立動作群組](./media/autoscale/create-actiongroup.png)
6. 在您的作業中建立 [**新的警示規則**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) 。 根據您選擇的度量來指定條件。 [*輸入事件*、 *SU% 使用率* 或 *囤積的輸入事件*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) 是建議用來定義自動調整邏輯的度量。 此外，也建議在觸發擴大作業時，使用1分鐘的 *匯總資料細微性* 和 *評估頻率* 。 這麼做可確保您的作業有足夠的資源，可應付輸入磁片區中的大量尖峰。
7. 選取在上一個步驟中建立的動作群組，並建立警示。
8. 針對您想要根據作業計量條件觸發的任何其他調整規模作業，重複步驟2到4。

在生產環境中執行作業之前，最好先執行調整規模測試。 當您針對不同的輸入負載測試您的作業時，您可以瞭解您的作業需要多少 su 來進行不同的輸入輸送量。 這可以通知您在警示規則中所定義的條件，以觸發擴大和縮小作業。 

## <a name="next-steps"></a>後續步驟
* [在 Azure 串流分析中建立可平行查詢](stream-analytics-parallelization.md)
* [調整 Azure 串流分析作業以增加輸送量](stream-analytics-scale-jobs.md)