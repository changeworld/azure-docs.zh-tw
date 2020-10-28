---
title: 在 Azure 成本管理中管理 AWS 成本和使用量
description: 本文協助您了解如何使用 Azure 成本管理中的成本分析和預算，來管理 AWS 成本和使用量。
author: bandersmsft
ms.author: banders
ms.date: 10/16/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: 5fed70ccdbebbd178412c416f37c2e9001a81f38
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148975"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>在 Azure 中管理 AWS 成本和使用量

在 Azure 成本管理中設定 AWS 成本和使用量報告整合之後，即可開始管理 AWS 成本和使用量。 本文協助您了解如何使用 Azure 成本管理中的成本分析和預算，來管理 AWS 成本和使用量。

如果您尚未設定此整合，請參閱[設定 AWS 使用量報告整合](aws-integration-set-up-configure.md)。

_開始之前：_ 如果您不熟悉成本分析，請參閱 [使用成本分析探索及分析成本](quick-acm-cost-analysis.md)快速入門。 此外，如果您不熟悉 Azure 中的預算，請參閱[建立和管理 Azure 預算](tutorial-acm-create-budgets.md)教學課程。

## <a name="view-aws-costs-in-cost-analysis"></a>在成本分析中檢視 AWS 成本

成本分析中提供以下範圍的 AWS 成本：

- 管理群組下的 AWS 連結帳戶
- AWS 連結帳戶成本
- AWS 合併帳戶成本

以下幾節將說明如何使用範圍，讓您可以查看每一項的成本和使用量資料。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>檢視管理群組下的 AWS 連結帳戶

想要查看來自不同 Azure 訂用帳戶和 AWS 連結帳戶的彙總成本，使用管理群組範圍來查看成本是唯一方法。 使用管理群組可提供跨雲端的檢視，以同時查看 Azure 和 AWS 的成本。

在成本分析中，開啟範圍選擇器，然後選取保存您 AWS 連結帳戶的管理群組。 以下是 Azure 入口網站的範例圖：

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="管理群組下包含已連結帳戶的「選取範圍」檢視範例" :::

以下範例顯示成本分析中的管理群組成本，並依提供者 (Azure 和 AWS) 分組。

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="管理群組下包含已連結帳戶的「選取範圍」檢視範例" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> Microsoft 客戶合約 (MCA) 客戶目前不支援管理群組。 MCA 客戶可以建立連接器及檢視其 AWS 資料。 不過，MCA 客戶無法在管理群組下同時檢視其 Azure 成本和 AWS 成本。

### <a name="view-aws-linked-account-costs"></a>檢視 AWS 連結帳戶成本

若要檢視 AWS 連結帳戶的成本，請開啟範圍選擇器，然後選取 AWS 連結帳戶。 請注意，連結帳戶會與管理群組相關聯，如 AWS 連接器中所定義。

以下範例顯示如何選取 AWS 連結帳戶範圍。

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="管理群組下包含已連結帳戶的「選取範圍」檢視範例" :::

### <a name="view-aws-consolidated-account-costs"></a>檢視 AWS 合併帳戶成本

若要檢視 AWS 合併帳戶的成本，請開啟範圍選擇器，然後選取 AWS 合併帳戶。 以下範例顯示如何選取 AWS 合併帳戶範圍。

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="管理群組下包含已連結帳戶的「選取範圍」檢視範例" :::

此範圍會提供與 AWS 合併帳戶相關聯之所有 AWS 連結帳戶的彙總檢視。 以下範例顯示 AWS 合併帳戶的成本，並依服務名稱分組。

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="管理群組下包含已連結帳戶的「選取範圍」檢視範例" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>可供篩選和分組的維度

下表描述在成本分析中可用於分組和篩選的維度。

| 維度 | Amazon CUR 標頭 | 範圍 | 註解 |
| --- | --- | --- | --- |
| 可用性區域 | lineitem/AvailabilityZone | 全部 |   |
| 位置 | product/Region | 全部 |   |
| 計量 |   | 全部 |   |
| 計量類別 | lineItem/ProductCode | 全部 |   |
| 計量子類別 | lineitem/UsageType | 全部 |   |
| 作業 | lineItem/Operation | 全部 |   |
| 資源 | lineItem/ResourceId | 全部 |   |
| 資源類型 | product/instanceType | 全部 | 如果 product/instanceType 為 null，則使用 lineItem/UsageType。 |
| ResourceGuid | N/A | 全部 | Azure 計量 GUID。 |
| 服務名稱 | product/ProductName | 全部 | 如果 product/ProductName 為 null，則使用 lineItem/ProductCode。 |
| 服務層級 |   |   |   |
| 訂用帳戶識別碼 | lineItem/UsageAccountId | 合併帳戶和管理群組 |   |
| 訂閱名稱 | N/A | 合併帳戶和管理群組 | 帳戶名稱是使用 AWS 組織 API 所收集。 |
| Tag | resourceTags | 全部 | 會從使用者定義的標籤中移除 _user:_ 首碼，以允許跨雲端標籤。 _aws:_ 前置詞保留不變。 |
| 計費帳戶識別碼 | bill/PayerAccountId | 管理群組 |   |
| 計費帳戶名稱 | N/A | 管理群組 | 帳戶名稱是使用 AWS 組織 API 所收集。 |
| 提供者 | N/A | 管理群組 | AWS 或 Azure。 |

## <a name="set-budgets-on-aws-scopes"></a>設定預算範圍為 AWS

使用預算來主動管理成本，並在您的組織中推動責任。 預算的範圍設定在 AWS 合併帳戶和 AWS 連結帳戶。 以下的預算範例是 Azure 成本管理中顯示的 AWS 合併帳戶：

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="管理群組下包含已連結帳戶的「選取範圍」檢視範例" :::

## <a name="aws-data-collection-process"></a>AWS 資料收集程序

設定 AWS 連接器之後，資料收集和探索程序就會啟動。 收集所有使用量資料可能需要幾小時的時間。 時間長短取決於：

- 處理 AWS S3 貯體中 CUR 檔案所需的時間。
- 建立 AWS 合併帳戶和 AWS 連結帳戶範圍所需的時間。
- AWS 寫入 S3 貯體中成本和使用量報告檔案的時間和頻率

## <a name="aws-integration-pricing"></a>AWS 整合價格

每個 AWS 連接器都有 90 天的免費試用。

定價是您 AWS 每月成本的 1%。 每個月都會根據您上個月的已開發票成本向您收費。

存取 AWS API 可能會增加 AWS 的額外成本。

## <a name="aws-integration-limitations"></a>AWS 整合的限制

- 成本管理中的預算不支援包含多種貨幣的管理群組。 包含多種貨幣的管理群組不會看到預算評估。 建立預算時，如果您選取的管理群組包含多種貨幣，則會顯示錯誤訊息。
- 雲端連接器不支援 AWS GovCloud (US)、AWS Gov 或 AWS China。
- Azure 成本管理只會顯示 AWS _使用量成本_ 。 尚不支援稅金、支持、退款、RI、點數或任何其他費用類型。

## <a name="troubleshooting-aws-integration"></a>AWS 整合的疑難排解

使用下列疑難排解資訊來解決常見問題。

### <a name="no-permission-to-aws-linked-accounts"></a>沒有 AWS 連結帳戶的許可權

**錯誤碼：** Unauthorized

有兩種方式可取得存取 AWS 連結帳戶成本的許可權：

- 取得 AWS 連結帳戶的管理群組存取權。
- 讓某人授與您 AWS 連結帳戶的許可權。

根據預設，AWS 連接器建立者是連接器所建立之所有物件的擁有者。 包括 AWS 合併帳戶和 AWS 連結帳戶。

為了能夠確認連接器設定，您至少需要一個參與者角色，讀取者無法確認連接器設定

### <a name="collection-failed-with-assumerole"></a>AssumeRole 收集失敗

**錯誤碼：** FailedToAssumeRole

此錯誤表示 Azure 成本管理無法呼叫 AWS AssumeRole API。 此問題可能是因為角色定義發生問題所造成。 請確認符合下列所有條件：

- 外部識別碼與角色定義和連接器定義中的識別碼相同。
- 角色類型設定為 **屬於您或第三方的另一個 AWS 帳戶** 。
- 已清除 **需要 MFA** 選擇。
- AWS 角色中的受信任 AWS 帳戶是 432263259397  。

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>收集因拒絕存取而失敗 - CUR 報表定義

**錯誤碼：** AccessDeniedReportDefinitions

此錯誤表示 Azure 成本管理無法看到成本和使用量報告的定義。 這個許可權是用來確認 CUR 是否已如 Azure 成本管理預期的定義。 請參閱[在 AWS 中建立成本和使用量報告](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)。

### <a name="collection-failed-with-access-denied---list-reports"></a>收集因拒絕存取而失敗 - 列出報告

**錯誤碼：** AccessDeniedListReports

此錯誤表示 Azure 成本管理無法列出 CUR 所在 S3 貯體中的物件。 AWS IAM 原則需要貯體的許可權和貯體中的物件。 請參閱[在 AWS 中建立角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)。

### <a name="collection-failed-with-access-denied---download-report"></a>收集因拒絕存取而失敗 - 下載報告

**錯誤碼：** AccessDeniedDownloadReport

此錯誤表示 Azure 成本管理無法存取和下載儲存在 Amazon S3 貯體中的 CUR 檔案。 請確定附加至角色的 AWS JSON 原則類似[在 AWS 中建立角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)一節最後所示的範例。

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>收集失敗，因為我們找不到成本和使用量報告

**錯誤碼：** FailedToFindReport

此錯誤表示 Azure 成本管理找不到連接器中定義的成本和使用量報告。 請確定報告沒被刪除，且附加至角色的 AWS JSON 原則類似[在 AWS 中建立角色和原則](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)一節最後所示的範例。

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>因為成本和使用量報告的定義不符，無法建立或確認連接器

**錯誤碼：** ReportIsNotValid

此錯誤與 AWS 成本和使用量報告的定義有關，我們需要這份報告的特定設定，請參閱[在 AWS 中建立成本和使用量報告](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)中的需求。

### <a name="internal-error-when-creating-connector"></a>建立連接器時發生內部錯誤

**錯誤碼：** _建立連接器 - 無法建立連接器 &lt;ConnectorName&gt;。理由：內部錯誤。請驗證已提供正確的 AWS 屬性。_

當您的 AWS 連接器和訂用帳戶位於不同的管理群組時，就會發生此錯誤。 AWS 連接器和訂用帳戶必須位於相同的管理群組。

## <a name="next-steps"></a>後續步驟

- 如果您尚未使用管理群組設定 Azure 環境，請參閱[管理群組的初始設定](../../governance/management-groups/overview.md#initial-setup-of-management-groups)。
