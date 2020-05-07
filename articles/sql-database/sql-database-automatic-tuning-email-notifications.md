---
title: 自動調整電子郵件通知的操作說明指南
description: 啟用 Azure SQL Database 自動查詢調整的電子郵件通知。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: f117f1518211680c4ea1f5ac51ecf0e44396a133
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856760"
---
# <a name="email-notifications-for-automatic-tuning"></a>針對自動調整的電子郵件通知

SQL Database 的調整建議是由 Azure SQL Database [自動調整](sql-database-automatic-tuning.md)產生。 這個解決方案會持續監視並分析 SQL Database 的工作負載，針對與建立索引、刪除索引和最佳化查詢執行計畫相關的個別資料庫提供自訂的調整建議。

您可以在 [Azure 入口網站](sql-database-advisor-portal.md)中檢視使用 [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) 呼叫或使用 [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) 和 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) 命令擷取的 SQL Database 自動調整建議。 本文使用 PowerShell 指令碼來擷取自動調整建議。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>對自動調整建議的電子郵件通知進行自動化

下列解決方案會自動傳送包含自動調整建議的電子郵件通知。 所描述的解決方案包含使用 [Azure 自動化](https://docs.microsoft.com/azure/automation/automation-intro)來自動執行 PowerShell 指令碼以擷取調整建議，以及使用 [Microsoft Flow](https://flow.microsoft.com) 來自動排程電子郵件傳送工作。

## <a name="create-azure-automation-account"></a>建立 Azure 自動化帳戶

要使用 Azure 自動化，第一步是建立自動化帳戶，並設定要用於執行 PowerShell 指令碼的 Azure 資源。 若要深入了解 Azure 自動化和其功能，請參閱[開始使用 Azure 自動化](https://docs.microsoft.com/azure/automation/automation-offering-get-started)。

遵循下列步驟，以透過從 Marketplace 選取和設定自動化應用程式的方法來建立 Azure 自動化帳戶：

1. 登入 Azure 入口網站。
1. 按一下左上角的 [**+ 建立資源**]。
1. 搜尋「**自動化**」（按 enter）。
1. 在搜尋結果中按一下自動化應用程式。

    ![加入 Azure 自動化](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

1. 在 [建立自動化帳戶] 窗格內，按一下 [**建立**]。
1. 填入所需的資訊：輸入此自動化帳戶的名稱，並選取您的 Azure 訂用帳戶識別碼和要用於執行 PowerShell 腳本的 Azure 資源。
1. 針對 [**建立 Azure 執行身分帳戶**] 選項，選取 [**是]** 以設定執行 PowerShell 腳本的帳戶類型，並提供 Azure 自動化的協助。 若要深入瞭解帳戶類型，請參閱[執行身分帳戶](https://docs.microsoft.com/azure/automation/automation-create-runas-account)。
1. 藉由按一下 [**建立**]，結束建立自動化帳戶。

> [!TIP]
> 正確記錄 (例如複製並貼到 [記事本]) 建立自動化應用程式時所輸入的 Azure 自動化帳戶名稱、訂用帳戶識別碼和資源。 稍後會需要此資訊。
>

如果您想為多個 Azure 訂用帳戶建置相同的自動化功能，則必須針對其他訂用帳戶重複此程序。

## <a name="update-azure-automation-modules"></a>更新 Azure 自動化模組

用來抓取自動調整建議的 PowerShell 腳本會使用[get-azresource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource)和[AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction)命令，其中需要 Azure 模組第4版和更新版本。

- 如果您的 Azure 模組需要更新，請參閱[Azure 自動化中的 Az 模組支援](../automation/shared-resources/modules.md)。

## <a name="create-azure-automation-runbook"></a>建立 Azure 自動化 Runbook

下一步是在 Azure 自動化中建立擷取調整建議的 PowerShell 指令碼所在的 Runbook。

遵循下列步驟來建立新的 Azure 自動化 Runbook：

1. 存取您在上一個步驟中建立的 Azure 自動化帳戶。
1. 在 [自動化帳戶] 窗格中，按一下左側的 [**runbook**] 功能表項目，以使用 PowerShell 腳本建立新的 Azure 自動化 runbook。 若要深入瞭解如何建立自動化 runbook，請參閱[建立新的 runbook](../automation/manage-runbooks.md#create-a-runbook)。
1. 若要加入新的 runbook，請按一下 [**+ 新增 runbook**] 功能表選項，然後按一下 [**快速建立-建立新的 runbook**]。
1. 在 [Runbook] 窗格中，輸入您的 runbook 名稱（基於此範例的用途，使用 "**AutomaticTuningEmailAutomation**"），選取 runbook 類型做為**PowerShell** ，並撰寫此 runbook 的描述以描述其用途。
1. 按一下 [**建立**] 按鈕以完成建立新的 runbook。

    ![加入 Azure 自動化 Runbook](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

遵循下列步驟來將 PowerShell 指令碼載入已建立的 Runbook 中：

1. 在 [**編輯 PowerShell Runbook**] 窗格中，選取功能表樹狀結構上的 [**runbook**] 並展開此流覽，直到您看到 Runbook 的名稱（在此範例中為 "**AutomaticTuningEmailAutomation**"）。 選取此 Runbook。
1. 在「編輯 PowerShell Runbook」的第一行（從數位1開始），複製並貼上下列 PowerShell 腳本程式碼。 這個 PowerShell 指令碼是依現狀提供，以協助您開始使用。 請修改指令碼以符合您的需求。

在提供的 PowerShell 指令碼標頭中，必須以您的 Azure 訂用帳戶識別碼取代 `<SUBSCRIPTION_ID_WITH_DATABASES>`。 若要深入了解如何擷取您的 Azure 訂用帳戶識別碼，請參閱[取得您的 Azure 訂用帳戶 GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/) \(英文\)。

如果有數個訂用帳戶，您可以將它們以逗號分隔，加上腳本標頭中的 "$subscriptions" 屬性。

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

按一下右上角的 [**儲存**] 按鈕來儲存腳本。 當您對腳本感到滿意時，請按一下 [**發佈**] 按鈕以發佈此 runbook。

在主要 [runbook] 窗格中，您可以選擇按一下 [**啟動**] 按鈕來**測試**腳本。 按一下 [**輸出**] 以查看執行之腳本的結果。 此輸出將是您的電子郵件內容。 在下列螢幕擷取畫面中，可以看到指令碼的範例輸出。

![使用 Azure 自動化執行檢視自動調整建議](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

請務必自訂 PowerShell 指令碼來調整內容，以符合您的需求。

使用上述步驟，擷取自動調整建議的 PowerShell 指令碼就會載入 Azure 自動化中。 下一步是自動化和排程電子郵件傳送工作。

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>使用 Microsoft Flow 自動化電子郵件工作

若要完成此解決方案，最後一個步驟是在 Microsoft Flow 中建立包含三個動作 (工作) 的自動化流程：

 - 「**Azure 自動化建立作業**」–用來執行 PowerShell 腳本，以抓取 Azure 自動化 runbook 內的自動調整建議。
 - 「**Azure 自動化取得作業輸出**」–用來從已執行的 PowerShell 腳本中抓取輸出。
 - 「**Office 365 Outlook –傳送電子郵件**」–用來傳送電子郵件。 電子郵件會使用建立流程之人員的 Office 365 帳戶傳送。

若要深入了解 Microsoft Flow 功能，請參閱[開始使用 Microsoft Flow](https://docs.microsoft.com/flow/getting-started)。

此步驟的先決條件是註冊 [Microsoft Flow](https://flow.microsoft.com) 帳戶並登入。 一旦進入解決方案，請遵循下列步驟來設定**新的流程**：

1. 存取 [**我的流程**] 功能表項目。
1. 在 [我的流程] 中，選取頁面頂端的 [**+ 從空白建立**] 連結。
1. 按一下頁面底部的 [**搜尋數百個連接器和觸發程式**] 連結。
1. 在搜尋欄位中輸入「**週期**」，然後從搜尋結果中選取 [**排程-週期**]，以排程要執行的電子郵件傳遞作業。
1. 在 [頻率] 欄位的 [週期] 窗格中，選取執行此流程的排程頻率，例如以每分鐘、每小時、每天、每週等週期傳送自動化電子郵件。

下一步是在新建立的週期性流程中加入三個工作 (建立、取得輸出和傳送電子郵件)。 若要將必要的工作加入流程，請遵循下列步驟：

1. 建立動作來執行 PowerShell 指令碼，以擷取調整建議

   - 在 [週期] 流程窗格內，選取 [**+ 新增步驟**]，然後按一下 [**加入動作**]。
   - 在搜尋欄位中輸入「**自動化**」，然後從搜尋結果中選取 [**Azure 自動化–建立作業**]。
   - 在 [建立工作] 窗格中，設定工作屬性。 針對此設定，您將需要先前在 ****[自動化帳戶]**** 窗格中記錄的 Azure 訂用帳戶識別碼、資源群組和自動化帳戶的詳細資料。 若要深入了解此區段所提供的選項，請參閱 [Azure 自動化 - 建立工作](https://docs.microsoft.com/connectors/azureautomation/#create-job) \(英文\)。
   - 按一下 [**儲存流程**] 以完成建立此動作。

2. 建立動作以從已執行的 PowerShell 指令碼擷取輸出

   - 在 [週期] 流程窗格內選取 [**+ 新增步驟**]，然後按一下 [**加入動作**]
   - 在 [搜尋] 中，輸入「**自動化**」，然後從搜尋結果中選取 [**Azure 自動化–取得工作輸出**]。 若要深入了解此區段所提供的選項，請參閱 [Azure 自動化 – 取得工作輸出](https://docs.microsoft.com/connectors/azureautomation/#get-job-output) \(英文\)。
   - 填入所需的欄位（類似于建立先前的工作）-填入您的 Azure 訂用帳戶識別碼、資源群組和自動化帳戶（如 [自動化帳戶] 窗格中所輸入）。
   - 在 [**工作識別碼**] 欄位內按一下，以顯示 [**動態內容**] 功能表。 從這個功能表中，選取 [**作業識別碼**] 選項。
   - 按一下 [**儲存流程**] 以完成建立此動作。

3. 使用 Office 365 整合建立動作以傳送電子郵件

   - 在 [週期] 流程窗格內，選取 [**+ 新增步驟**]，然後按一下 [**加入動作**]。
   - 在 [搜尋] 中，輸入「**傳送電子郵件**」，然後從搜尋結果中選取 [**Office 365 Outlook –傳送電子郵件**]。
   - 在 [**到**] 欄位中，輸入您需要傳送通知電子郵件的電子郵件地址。
   - 在 **[主旨] 欄位**中輸入您的電子郵件主旨，例如「自動調整建議電子郵件通知」。
   - 在 [**主體**] 欄位內按一下，以顯示 [**動態內容**] 功能表。 從這個功能表中的 [**取得作業輸出**] 底下，選取 [**內容**]。
   - 按一下 [**儲存流程**] 以完成建立此動作。

> [!TIP]
> 若要傳送自動化電子郵件給不同的收件者，請建立個別流程。 在這些額外的流程中，變更 [收件者] 欄位中的收件者電子郵件地址，以及 [主旨] 欄位中的電子郵件主旨行。 使用自訂 PowerShell 指令碼 (例如變更 Azure 訂用帳戶識別碼) 在 Azure 自動化中建立新的 Runbook 可進一步自訂自動化案例，例如針對個別的訂用帳戶，傳送有關自動調整建議的電子郵件給個別的收件者。
>

以上是設定電子郵件傳送工作之工作流程所需的步驟。 整個流程包含三個動作 (顯示於下圖)。

![檢視自動調整電子郵件通知流程](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

若要測試流程，請按一下 [流程] 窗格內右上角的 [**立即執行**]。

從 [流程分析] 窗格中可以看到執行自動化工作的統計資料 (顯示成功送出電子郵件通知)。

![自動調整電子郵件通知的執行流程](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

[流程] 分析可協助您監視工作執行是否成功，以及是否需要進行疑難排解。  在進行疑難排解時，您也可以檢查可透過 Azure 自動化應用程式存取的 PowerShell 指令碼執行記錄。

自動化電子郵件的最終輸出，看起來類似在建置和執行此解決方案後所收到的電子郵件：

![自動調整電子郵件通知的範例電子郵件輸出](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

藉由調整 PowerShell 指令碼，您可以調整自動化電子郵件的輸出和格式，以符合您的需求。

視您的自訂案例而定，您可進一步將解決方案自訂為根據特定調整事件建立電子郵件通知，以及針對多個訂用帳戶或資料庫傳送給多個收件者。

## <a name="next-steps"></a>後續步驟

- 若要了解自動調整如何能協助您提升資料庫效能，請參閱 [Azure SQL Database 中的自動調整](sql-database-automatic-tuning.md)。
- 若要在 Azure SQL Database 中啟用自動調整以管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要手動檢閱及套用自動調整建議，請參閱[尋找及套用效能建議](sql-database-advisor-portal.md)。
