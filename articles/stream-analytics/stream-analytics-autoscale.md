---
title: 自動調整串流分析作業
description: 本文說明如何根據預先定義的排程或作業計量值，自動調整串流分析作業
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: 07cbb28b98fcbac1932424c1c72f388813ec2400
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037557"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>使用 Azure 自動化自動調整串流分析作業

您可以藉由設定自動調整來將串流分析作業的成本優化。 自動調整會增加或減少作業的串流單位（su），以符合輸入負載中的變更。 您可以視需要相應增加或相應減少，而不是過度布建您的作業。 有兩種方式可將您的作業設定為自動調整：
1. 當您有可預測的輸入負載時，**預先定義排程**。 例如，您預期在白天的輸入事件速率較高，而且想要使用更多的 su 來執行作業。
2. 當您沒有可預測的輸入負載時，**根據工作計量觸發相應增加和相應減少作業**。 您可以根據您的作業計量（例如輸入事件數目或待進行的輸入事件）來動態變更 su 數目。

## <a name="prerequisites"></a>必要條件
在您開始設定作業的自動調整之前，請完成下列步驟。
1. 您的作業已優化，可具有[平行拓撲](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)。 如果您可以在作業執行時變更其規模，則您的作業會有平行拓朴，而且可以設定為自動調整。
2. 建立已啟用「RunAsAccount」選項的[Azure 自動化帳戶](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)。 此帳戶必須具有管理串流分析作業的許可權。

## <a name="set-up-azure-automation"></a>設定 Azure 自動化
### <a name="configure-variables"></a>設定變數
在 Azure 自動化帳戶內加入下列變數。 這些變數將會在後續步驟所述的 runbook 中使用。

| 名稱 | 類型 | 值 |
| --- | --- | --- |
| **jobName** | String | 您想要自動調整的串流分析作業名稱。 |
| **resourceGroupName** | String | 您的作業所在的資源組名。 |
| **subId** | String | 有您的作業所在的訂用帳戶識別碼。 |
| **increasedSU** | 整數 | 您想要讓作業依排程調整的 SU 值愈高。 此值必須是您在作業執行時，在工作的 [**調整規模**設定] 中看到的其中一個有效 SU 選項。 |
| **decreasedSU** | 整數 | 您想要讓作業依排程調整的較低 SU 值。 此值必須是您在作業執行時，在工作的 [**調整規模**設定] 中看到的其中一個有效 SU 選項。 |
| **maxSU** | 整數 | 當負載自動調整時，您想要讓作業依照步驟擴充的最大 SU 值。 此值必須是您在作業執行時，在工作的 [**調整規模**設定] 中看到的其中一個有效 SU 選項。 |
| **minSU** | 整數 | 當負載自動調整時，您想要讓作業依照步驟擴充的最小 SU 值。 此值必須是您在作業執行時，在工作的 [**調整規模**設定] 中看到的其中一個有效 SU 選項。 |

![在 Azure 自動化中新增變數](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>建立 Runbook
下一個步驟是建立兩個 PowerShell runbook。 一個用於相應增加，另一個用於相應減少作業。
1. 在 Azure 自動化帳戶中，移至 [程式**自動化**] 底下的 [ **runbook** ]，然後選取 [**建立 Runbook**]。
2. 將類型設定為 PowerShell 的第一個 runbook *ScaleUpRunbook*命名為。 使用 GitHub 中提供的[ScaleUpRunbook PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1)。 儲存併發布它。
3. 使用 PowerShell 類型建立另一個名為*ScaleDownRunbook*的 runbook。 使用 GitHub 中提供的[ScaleDownRunbook PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1)。 儲存併發布它。

![Azure 自動化中的自動調整 runbook](./media/autoscale/runbooks.png)

您現在有 runbook 可以自動觸發串流分析作業的相應增加和相應減少作業。 這些 runbook 可以使用預先定義的排程來觸發，或可以根據作業計量動態設定。

## <a name="autoscale-based-on-a-schedule"></a>根據排程自動調整
Azure 自動化可讓您設定排程以觸發 runbook。
1. 在您的 Azure 自動化帳戶中 **，選取 [** **共用資源**] 底下的 [排程]。 接著，選取 [**新增排程**]。
2. 例如，您可以建立兩個排程。 一個，代表您想要讓作業相應增加，另一個代表當您想要調整作業的時機。 您可以為這些排程定義週期。

   ![Azure 自動化中的排程](./media/autoscale/schedules.png)

3. 開啟您的**ScaleUpRunbook** ，然後**選取**[**資源**] 下的 [排程]。 接著，您可以將您的 runbook 連結到您在先前步驟中建立的排程。 您可以擁有多個與相同 runbook 連結的排程，當您想要在一天的不同時間執行相同的調整作業時，這會很有説明。

![排程 Azure 自動化中的 runbook](./media/autoscale/schedulerunbook.png)

1. 針對**ScaleDownRunbook**重複上一個步驟。

## <a name="autoscale-based-on-load"></a>根據負載自動調整
在某些情況下，您可能無法預測輸入負載。 在這種情況下，在最小和最大界限內的步驟中相應增加/相應減少會比較理想。 您可以在串流分析作業中設定警示規則，以在作業計量高於或低於臨界值時觸發 runbook。
1. 在您的 Azure 自動化帳戶中，建立兩個更多整數變數，稱為**minSU**和**maxSU**。 這會設定您的作業將在步驟中相應縮小的範圍。
2. 建立兩個新的 runbook。 您可以使用[StepScaleUp PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1)，以遞增方式增加作業的 su，直到**maxSU**值為止。 您也可以使用[StepScaleDown PowerShell 腳本](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1)來減少作業的 su，直到達到**minSU**值為止。 或者，如果您有想要調整的特定 SU 值，您可以使用上一節中的 runbook。
3. 在您的串流分析作業中，選取 [**監視**] 下的 [**警示規則**]。 
4. 建立兩個動作群組。 一個用於相應增加作業，另一個用於 scale down 作業。 選取 [**管理動作**]，然後按一下 [**新增動作群組**]。 
5. 填寫必要欄位。 當您選取 [**動作類型**] 時，請選擇 [**自動化 Runbook** ]。 選取您想要在警示引發時觸發的 runbook。 然後，建立動作群組。

   ![建立動作群組](./media/autoscale/create-actiongroup.png)
6. 在您的作業中建立[**新的警示規則**](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)。 根據您選擇的度量來指定條件。 [*輸入事件*、 *SU% 使用率*或待執行的*輸入事件*](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)是用來定義自動調整邏輯的建議計量。 此外，也建議在觸發相應增加作業時，使用1分鐘*匯總資料細微性*和*評估頻率*。 這麼做可確保您的作業有充足的資源，可應付輸入磁片區中的大量尖峰。
7. 選取在上一個步驟中建立的動作群組，然後建立警示。
8. 針對您想要根據作業計量條件觸發的任何其他調整規模作業，重複步驟2到4。

最佳做法是先執行調整測試，再于生產環境中執行作業。 當您針對不同的輸入負載測試您的作業時，您可以瞭解您的作業需要多少個 su，以進行不同的輸入輸送量。 這會通知您在警示規則中定義的條件，以觸發相應增加和相應減少作業。 

## <a name="next-steps"></a>後續步驟
* [在 Azure 串流分析中建立可平行查詢](stream-analytics-parallelization.md)
* [調整 Azure 串流分析作業以增加輸送量](stream-analytics-scale-jobs.md)
