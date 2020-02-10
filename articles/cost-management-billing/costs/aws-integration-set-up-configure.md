---
title: 設定 AWS 與 Azure 成本管理的整合
description: 本文會逐步引導您設定 AWS 成本和使用量報告與 Azure 成本管理的整合。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 5dbc6ca836c8d1c8b717fd4bf23eab5aa360a288
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988691"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>設定 AWS 成本和使用量報告的整合

透過 Amazon Web Services (AWS) 成本和使用量報告 (CUR) 的整合，您可以在 Azure 成本管理中監視並控制 AWS 支出。 此整合可讓您在 Azure 入口網站的單一位置，同時監視和控制 Azure 與 AWS 的費用。 本文會說明如何設定整合，以便您可以使用 Azure 成本管理功能來分析成本及檢閱預算。

成本管理會使用 AWS 存取認證來取得報告定義和下載報告 GZIP CSV 檔案，以處理儲存在 S3 貯體中的 AWS 成本和使用量報告。

## <a name="create-a-cost-and-usage-report-in-aws"></a>在 AWS 中建立成本和使用量報告

AWS 建議使用成本和使用量報告來收集和處理 AWS 成本。 如需詳細資訊，請參閱 [AWS 成本和使用量報告](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)文件。

在 AWS 中使用「計費和成本管理」主控台的 [成本和使用量報告]  頁面，即可透過下列步驟建立成本和使用量報告：

1. 登入 AWS 管理主控台，並開啟[計費和成本管理主控台](https://console.aws.amazon.com/billing)。
2. 在瀏覽窗格中，選取 [成本和使用量報告]  。
3. 選取 [建立報告]  。
4. 在 [報告名稱]  中輸入報告的名稱。
5. 在 [其他報告詳細資料]  底下，選取 [包含資源識別碼]  。
6. 在 [資料重新整理設定]  中，選取是否要在完成帳單之後，AWS 將退款、信用額度或支援費用套用到您的帳戶時，讓 AWS 成本和使用量報告重新整理。 當報告重新整理時，便會有新的報告上傳到 Amazon S3。 建議您讓此設定保留選取狀態。
7. 選取 [下一步]  。
8. 在 [S3 貯體]  中，選擇 [設定]  。
9. 在 [設定 S3 貯體] 對話方塊中，執行下列其中一項工作：
    1. 從下拉式清單中選取現有的貯體，然後選擇 [下一步]  。
    2. 輸入貯體名稱和您要在其中建立新貯體的區域，然後選擇 [下一步]  。
10. 選取 [我已確認此原則正確無誤]  ，然後按一下 [儲存]  。
11. (選擇性) 在 [報告路徑前置詞] 中，輸入您想要在報告名稱前面加上的報告路徑前置詞。
如果您未指定前置詞，則預設前置詞會是您為報告指定的名稱。 日期範圍具有 `/report-name/date-range/` 格式。
12. 在 [時間單位]  中，選擇 [每小時]  。
13. 在 [報告版本設定]  中，選擇是要讓報告的每個版本覆寫先前的版本，還是要建立其他新的報告。
14. 在 [為下列項目啟用資料整合]  中，不需要選取任何項目。
15. 在 [壓縮]  中，選取 [GZIP]  。
16. 選取 [下一步]  。
17. 在檢閱過報告的設定之後，選取 [檢閱並完成]  。

    記下報告名稱。 您將在稍後的步驟中用到此名稱。

最多可能需要 24 小時的時間，AWS 才會開始將報告傳遞至 Amazon S3 貯體。 傳遞開始之後，AWS 一天至少會更新 AWS 成本和使用量報告檔案一次。 您可以繼續設定 AWS 環境，而不需要等待傳遞開始。

## <a name="create-a-role-and-policy-in-aws"></a>在 AWS 中建立角色和原則

Azure 成本管理一天會存取成本和使用量報告儲存所在的 S3 貯體數次。 服務需要存取認證才能檢查是否有新的資料。 請在 AWS 中建立角色和原則，以允許成本管理存取該認證。

若要在成本管理中啟用 AWS 帳戶的角色型存取，請在 AWS 主控台中建立角色。 您必須從 AWS 主控台取得「角色 ARN」  和「外部識別碼」  。 稍後，您會在成本管理的 [建立 AWS 連接器]  頁面上用到這些值。

使用「建立新角色」精靈：

1. 登入 AWS 主控台，然後選取 [服務]  。
2. 在服務清單中，選取 [IAM]  。
3. 選取 [角色]  ，然後選取 [建立角色]  。
4. 在下一個頁面上，選取 [另一個 AWS 帳戶]  。
5. 在 [帳戶識別碼]  中，輸入 **432263259397**。
6. 在 [選項]  中，選取 [需要外部識別碼 (這是第三方會擔任此角色時的最佳做法)]  。
7. 在 [外部識別碼]  中輸入外部識別碼，這是 AWS 角色與 Azure 成本管理所共用的密碼。 相同的外部識別碼也會用於成本管理中的 [新增連接器]  頁面上。 Microsoft 建議您在輸入外部識別碼時使用強式密碼原則。

    > [!NOTE]
    > 請勿變更 [需要 MFA]  的選取狀態。 其應該保持清除狀態。
8. 完成時，選取 [下一步:**權限]** 。
9. 選取 [建立原則]  。 隨即開啟新的瀏覽器索引標籤。 您將會在此建立原則。
10. 選取 [選擇服務]  。

設定成本和使用量報告的權限：

1. 輸入**成本和使用量報告**。
2. 選取 [存取層級]   > [閱讀]   > [DescribeReportDefinitions]  。 此步驟可讓成本管理讀取所定義的 CUR 報告，並判斷其是否符合報告定義的必要條件。
3. 選取 [新增其他權限]  。

設定 S3 貯體和物件的權限：

1. 選取 [選擇服務]  。
2. 輸入 **S3**。
3. 選取 [存取層級]   > [清單]   > [ListBucket]  。 此動作會取得 S3 貯體中的物件清單。
4. 選取 [存取層級]   > [讀取]   > [GetObject]  。 此動作可下載計費檔案。
5. 選取 [資源]  。
6. 選取 [貯體 – 新增 ARN]  。
7. 在 [貯體名稱]  中，輸入用來儲存 CUR 檔案的貯體。
8. 選取 [物件 – 新增 ARN]  。
9. 在 [貯體名稱]  中，輸入用來儲存 CUR 檔案的貯體。
10. 在 [物件名稱]  中，選取 [任何]  。
11. 選取 [新增其他權限]  。

設定成本總管的權限：

1. 選取 [選擇服務]  。
2. 輸入**成本總管服務**。
3. 選取 [所有成本總管服務動作 (ce:\*)]  。 此動作會驗證集合是否正確。
4. 選取 [新增其他權限]  。

新增 AWS 組織的權限：

1. 輸入**組織**。
2. 選取 [存取層級]   > [清單]   > [ListAccounts]  。 此動作會取得帳戶的名稱。
3. 在 [檢閱原則]  中，輸入新原則的名稱。 請確認您輸入的資訊正確無誤，然後選取 [建立原則]  。
4. 回到上一個索引標籤，然後重新整理瀏覽器的網頁。 在搜尋列中，搜尋您的新原則。
5. 完成時，選取 下一步: **:** 。
6. 輸入新角色的名稱。 請確認您輸入的資訊正確無誤，然後選取 [建立角色]  。

    請記下您在建立角色時，用於上述步驟的角色 ARN 和外部識別碼。 稍後當您在設定 Azure 成本管理連接器時，將會用到這些值。

原則 JSON 應該會像下列範例。 將 bucketname  取代為您的 S3 貯體名稱。

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>在 Azure 中設定新的 AWS 連接器

使用下列資訊來建立 AWS 連接器，並開始監視您的 AWS 成本：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至 [成本管理 + 計費]   > [成本管理]  。
3. 在 [設定]  底下，選取 [雲端連接器 (預覽)]  。  
    ![顯示雲端連接器 (預覽) 設定的範例](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)。
4. 選取頁面頂端的 [+新增]  以建立連接器。
5. 在 [建立 AWS 連接器]  頁面上的 [顯示名稱]  中，輸入連接器的名稱。  
    ![用於建立 AWS 連接器的頁面範例](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. (選擇性) 選取 [預設管理群組]。 其會儲存所有已探索到的連結帳戶。 您可以稍後再設定。
7. 如果您想要確保預覽版到期時可繼續作業，請在 [計費]  區段中選取 [正式推出後自動收取 1% 費用]  。 如果您選取 [自動] 選項，則必須選取計費訂用帳戶。
8. 在 [角色 ARN]  中，輸入您在 AWS 中設定角色時所使用的值。
9. 在 [外部識別碼]  中，輸入您在 AWS 中設定角色時所使用的值。
10. 在 [報告名稱]  中，輸入您在 AWS 中建立的名稱。
11. 選取 [下一步]  ，然後選取 [建立]  。

新的 AWS 範圍、AWS 合併帳戶、AWS 連結帳戶及其成本資料可能需要幾個小時才會出現。

建立連接器之後，建議您對其指派存取控制。 系統會將所探索到新範圍的權限指派給使用者：AWS 合併帳戶和 AWS 連結帳戶。 建立連接器的使用者將會是連接器、合併帳戶及所有連結帳戶的擁有者。

在探索之後將連接器權限指派給使用者時，並不會指派現有 AWS 範圍的權限。 相反地，會指派的只有新連結帳戶的權限。

## <a name="take-additional-steps"></a>採取其他步驟

- [設定管理群組](../../governance/management-groups/overview.md#initial-setup-of-management-groups) (如果您還未擁有)。
- 確認已將新的範圍新增至範圍選擇器。 選取 [重新整理]  以檢視最新資料。
- 在 [雲端連接器]  頁面上選取您的連接器，然後選取 [移至計費帳戶]  將連結帳戶指派給管理群組。

## <a name="manage-cloud-connectors"></a>管理雲端連接器

當您選取 [雲端連接器]  頁面上的連接器時，您可以：

- 選取 [移至計費帳戶]  以檢視 AWS 合併帳戶的資訊。
- 選取 [存取控制]  來管理連接器的角色指派。
- 選取 [編輯]  以更新連接器。 您無法變更 AWS 帳戶號碼，因為其出現在角色 ARN 中。 但是，您可以建立新的連接器。
- 選取 [確認]  來重新執行驗證測試，以確定成本管理可以使用連接器設定來收集資料。

![已建立的 AWS 連接器範例清單](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>設定 Azure 管理群組

將 Azure 訂用帳戶和 AWS 連結帳戶放在相同的管理群組中，以建立可查看跨雲端提供者資訊的單一位置。 如果您尚未使用管理群組設定 Azure 環境，請參閱[管理群組的初始設定](../../governance/management-groups/overview.md#initial-setup-of-management-groups)。

如果您想要區分成本，則可以建立一個只保存 AWS 連結帳戶的管理群組。

## <a name="set-up-an-aws-consolidated-account"></a>設定 AWS 合併帳戶

AWS 合併帳戶會結合多個 AWS 帳戶的計費和付款。 其也會作為 AWS 連結帳戶。

![AWS 合併帳戶的範例詳細資料](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

您可以從該頁面執行下列動作：

- 選取 [更新]  以大量更新 AWS 連結帳戶與管理群組的關聯。
- 選取 [存取控制]  以設定範圍的角色指派。

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 合併帳戶的權限

根據預設，AWS 合併帳戶的權限會根據 AWS 連接器權限，在建立帳戶時加以設定。 連接器建立者就是擁有者。

您可以使用 AWS 合併帳戶的 [存取層級]  頁面來管理存取層級。 不過，AWS 連結帳戶不會繼承 AWS 合併帳戶的權限。

## <a name="set-up-an-aws-linked-account"></a>設定 AWS 連結帳戶

AWS 連結帳戶是用來建立和管理 AWS 資源的地方。 連結帳戶也可作為安全性界限。

您可以從這個頁面執行下列動作：

- 選取 [更新]  以更新 AWS 連結帳戶與管理群組的關聯。
- 選取 [存取控制]  以設定範圍的角色指派。

![AWS 連結帳戶頁面的範例](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS 連結帳戶的權限

根據預設，AWS 連結帳戶的權限會根據 AWS 連接器權限，在建立帳戶時加以設定。 連接器建立者就是擁有者。 您可以使用 AWS 連結帳戶的 [存取層級]  頁面來管理存取層級。 AWS 連結帳戶不會繼承 AWS 合併帳戶的權限。

AWS 連結帳戶一律會繼承其所屬管理群組的權限。

## <a name="next-steps"></a>後續步驟

- 現在您已設定好 AWS 成本和使用量報告的整合，接下來請繼續[管理 AWS 成本和使用量](aws-integration-manage.md)。
- 如果您不熟悉成本分析，請參閱[使用成本分析探索及分析成本](quick-acm-cost-analysis.md)快速入門。
- 如果您不熟悉 Azure 中的預算，請參閱[建立和管理 Azure 預算](tutorial-acm-create-budgets.md)。
