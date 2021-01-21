---
title: 自動將敏感度標籤套用至您的資料
description: 瞭解如何建立敏感度標籤，並在掃描期間自動將它們套用至您的資料。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/19/2021
ms.openlocfilehash: b376883ab7d8ef0ffd57a271e74862b684788ebd
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630271"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>在 Azure 範疇中自動標示您的資料

本文說明如何建立 Microsoft 資訊保護 (MIP) 敏感度標籤，並將其自動套用至 Azure 範疇中的 Azure 資產。

## <a name="what-are-sensitivity-labels"></a>什麼是敏感度標籤？ 

若要完成工作，您組織中的人員會與組織內外的其他人共同作業。 資料不一定會留在您的雲端，而且通常會在各種裝置、應用程式和服務之間漫遊。 

當您的資料漫遊時，您想要以安全、受保護的方式來達成此目的，以符合您組織的商務和合規性政策。

套用敏感度標籤可讓您陳述組織中特定資料的敏感程度。 例如，特定的專案名稱在您的組織內可能會有高度機密，但該相同的詞彙不是其他組織的機密。 

### <a name="sensitivity-labels-in-azure-purview"></a>Azure 範疇中的敏感度標籤

在範疇中，分類類似于主旨標記，可用來標記和識別在掃描期間于您的資料資產內找到的特定類型資料。

範疇會使用相同的分類，也稱為敏感性資訊類型，Microsoft 365。  MIP 敏感度標籤是在 Microsoft 365 的安全性與合規性中心 (SCC) 建立。 這可讓您在 Azure 範疇資產之間擴充現有的敏感度標籤。

**分類** 會直接比對，例如社會安全號碼，其具有身分證 **號碼** 的分類。 

相反地，當同時找到一或多個分類和條件時，便會套用 **敏感度標籤** 。 在此內容中， [條件](/microsoft-365/compliance/apply-sensitivity-label-automatically) 會參考您可以為非結構化資料定義的所有參數，例如與 *另一個分類的相近度*，以及 *% 信賴度*。 

Azure 範疇中的敏感度標籤可以用來自動將標籤套用至檔案和資料庫資料行。

如需詳細資訊，請參閱

- 瞭解 Microsoft 365 檔中的[敏感度標籤](/microsoft-365/compliance/sensitivity-labels)
- [什麼是 pdf 規則？](#what-are-autolabeling-rules)
- [Azure 範疇中的敏感度標籤支援的資料類型](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [SQL database 資料行的標籤](#labeling-for-sql-database-columns)

#### <a name="what-are-autolabeling-rules"></a>什麼是 pdf 規則？

您的資料不斷成長和變更。 追蹤目前未標記的資料，並採取動作來手動套用標籤並不只是麻煩的工作，而且也是不必要的麻煩。 

Pdf 規則是您指定的條件，說明何時應該套用特定標籤。 當符合這些條件時，系統會自動將標籤指派給資料，以大規模保留資料的一致敏感度標籤。

當您建立標籤時，請務必為檔案和[資料庫資料行](#define-autolabeling-rules-for-database-columns)定義 pdf[規則，以](#define-autolabeling-rules-for-files)在每次資料掃描時自動套用標籤。 

掃描範疇中的資料之後，您可以在範疇類別目錄和見解報表中，看到自動套用的標籤。
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Azure 範疇中的敏感度標籤支援的資料類型

Azure 範疇中支援下列資料類型的敏感度標籤：

|資料類型  |來源  |
|---------|---------|
|檔案的自動標記     |      - Azure Blob 儲存體  </br>-Azure Data Lake Storage Gen 1 和 Gen 2  |
|資料庫資料行的自動標籤     |  -SQL server </br>-Azure SQL database </br>-Azure SQL Database 受控執行個體   <br> -Azure Synapse  <br> - Azure Cosmos DB <br><br>如需詳細資訊，請參閱下方 [SQL database 資料行](#labeling-for-sql-database-columns) 的標籤。  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>SQL database 資料行的標籤

除了適用于資料庫資料行的範疇標籤之外，Microsoft 也支援在 [SQL Server Management Studio (SSMS) ](/sql/ssms/sql-server-management-studio-ssms)中使用 sql 資料分類來標記 sql database 資料行。 當範疇使用全域 [MIP 敏感度標籤](/microsoft-365/compliance/sensitivity-labels)時，SSMS 只會使用在本機定義的標籤。

在 SSMS 中範疇和標記的標記，是目前不會彼此互動的個別進程。 因此，在 SSMS 中套用的標籤不會顯示于範疇中，反之亦然。 我們建議使用 Azure 範疇來標記 SQL 資料庫，因為它會使用可跨多個平臺套用的全域 MIP 標籤。

如需詳細資訊，請參閱 [SQL 資料探索和分類檔](/sql/relational-databases/security/sql-data-discovery-and-classification)。

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>如何在 Microsoft 365 中建立敏感度標籤

如果您還沒有敏感度標籤，您必須建立它們，並使其可供 Azure 範疇使用。 您也可以修改現有的敏感度標籤，使其可供 Azure 範疇使用。

如需詳細資訊，請參閱

- [授權需求](#licensing-requirements)
- [將敏感度標籤擴充至 Azure 範疇](#extending-sensitivity-labels-to-azure-purview)
- [建立新的敏感度標籤或修改現有標籤](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>授權需求

MIP 敏感度標籤是在 Microsoft 365 安全性與合規性中心建立和管理。 若要建立要在 Azure 範疇中使用的敏感度標籤，您必須擁有 active Microsoft 365 E5 授權。

如果您還沒有必要的授權，您可以註冊 [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion)的試用版。

### <a name="extending-sensitivity-labels-to-azure-purview"></a>將敏感度標籤擴充至 Azure 範疇

根據預設，MIP 敏感度標籤只適用于 Microsoft 365 中的資產，您可以在其中將它們套用至檔案和電子郵件。

若要在 Azure 範疇中將 MIP 敏感度標籤套用至 Azure 資產，您必須明確地同意擴充標籤，並選取您想要在範疇中使用的特定標籤。

藉由使用 Azure 範疇來擴充 MIP 的敏感度標籤，組織現在可以探索、分類及深入瞭解範圍廣泛的資料來源，並將合規性風險降至最低。

> [!NOTE]
> 由於 Microsoft 365 和 Azure 範疇是不同的服務，因此可能會將它們部署在不同的區域中。 標籤名稱和自訂敏感性資訊類型名稱會被視為客戶資料，並預設保留在相同的地理位置，以保護您資料的機密性，並避免 GDPR 的法律。
>
> 基於這個理由，預設不會將標籤和自訂敏感性資訊類型共用至 Azure 範疇，並需要您的同意才能在 Azure 範疇中使用它們。

**若要將敏感度標籤延伸至範疇：**

在 Microsoft 365 中，流覽至 [ **資訊保護** ] 頁面。 在 [將卷 **標擴充至 Azure 中的資產] 範疇** 中，選取 [ **開啟** ] 按鈕，然後在出現的確認對話方塊中選取 **[是** ]。

例如：

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="選取 [開啟] * * 以將敏感度標籤延伸至範疇" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
將標籤延伸至 Azure 範疇中的資產之後，您可以選取要在範疇中使用的標籤。 如需詳細資訊，請參閱 [建立新的敏感度標籤或修改現有的標籤](#creating-new-sensitivity-labels-or-modifying-existing-labels)。
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>建立新的敏感度標籤或修改現有標籤

1. 開啟 [Microsoft 365 的安全性與合規性中心](https://protection.office.com/homepage)。 

1. 在 [ **方案**] 底下，選取 [ **資訊保護**]，然後選取 [ **建立標籤**]。 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="在 Microsoft 365 安全性與合規性中心建立敏感度標籤" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. 命名標籤。 然後，在 [ **定義此標籤的範圍**] 下，選取 [檔案 **和電子郵件** ] 和 [ **Azure 範疇資產**]。
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="在 Microsoft 365 安全性與合規性中心建立您的標籤" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. 遵循嚮導中的其餘提示，以取得標籤設定。 

    具體而言，請定義檔案和資料庫資料行的 pdf 規則：

    - [定義檔案的 pdf 規則](#define-autolabeling-rules-for-files)
    - [定義資料庫資料行的 pdf 規則](#define-autolabeling-rules-for-database-columns)

    如需有關 wizard 選項的詳細資訊，請參閱 Microsoft 365 檔中的 [敏感度標籤](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) 。

1. 重複上面所列的步驟，以建立更多標籤。 

    若要建立子標籤，請選取父標籤 **> ...**  > **更多動作**  > **新增子標籤**。

1. 若要修改現有的標籤，請流覽至 [**資訊保護**]  >  **卷** 標，然後選取您的標籤。 

    然後選取 [ **編輯標籤** ]，再次開啟 [ **編輯敏感度標籤** ] 嚮導，並使用您在建立標籤時定義的所有設定。

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="編輯現有的敏感度標籤" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. 當您完成所有標籤的建立時，請務必查看您的標籤順序，並視需要重新排序。 

    若要變更標籤的順序，請選取 **...** **> 更多動作**  >  **上移** 或 **下移。** 

    如需詳細資訊，請參閱 Microsoft 365 檔中的 [標籤優先順序 (順序重要) ](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) 。

> [!IMPORTANT]
> 除非您瞭解對使用者的影響，否則請勿刪除標籤。 
>
> 如需詳細資訊，請參閱 Microsoft 365 檔中的 [移除和刪除標籤](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) 。

繼續 [掃描您的資料以自動套用標籤](#scan-your-data-to-apply-labels-automatically)，然後：

- [查看資產上的標籤](#view-labels-on-assets)
- [查看分類和敏感度標籤的深入解析報表](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-autolabeling-rules-for-files"></a>定義檔案的 pdf 規則

當您建立或編輯標籤時，請在嚮導中定義檔案的 pdf 規則。 

在 [ **office 應用程式的自動標籤** ] 頁面上， **為 office 應用程式啟用自動標記，** 然後定義您要將標籤自動套用至資料的條件。

例如：

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="在 Microsoft 365 安全性與合規性中心中定義檔案的 pdf 規則" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
如需詳細資訊，請參閱 Microsoft 365 檔中的 [自動將敏感度標籤套用至資料](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) 。 

#### <a name="define-autolabeling-rules-for-database-columns"></a>定義資料庫資料行的 pdf 規則

當您建立或編輯標籤時，請為 wizard 中的資料庫資料行定義 pdf 規則。 

在 [ **Azure 範疇資產] (預覽])** 選項：

1. 選取 [ **資料庫資料行的自動標籤** ] 滑杆。

1. 選取 [ **檢查敏感性資訊類型** ]，選擇您想要套用至標籤的敏感性資訊類型。

例如：
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="在 Microsoft 365 安全性與合規性中心定義 SQL 資料行的 pdf 規則" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>掃描您的資料以自動套用標籤

掃描 Azure 範疇中的資料，根據您所定義的 pdf 規則自動套用您所建立的標籤。 

如需有關如何在 Azure 範疇中設定各種資產掃描的詳細資訊，請參閱：

|來源  |參考  |
|---------|---------|
|**Azure Blob 儲存體**     |[註冊並掃描 Azure Blob 儲存體](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake 儲存體**     |[註冊並掃描 Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[註冊並掃描 Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Azure SQL Database**|[註冊和掃描 Azure SQL Database](register-scan-azure-sql-database.md) </br>[註冊和掃描 Azure SQL Database 受控執行個體](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>查看資產上的標籤

在 Microsoft 365 中定義標籤的 pdf 規則，並在 Azure 範疇中掃描您的資料之後，標籤就會自動套用至您的資產。 

**若要在 Azure 範疇目錄中查看套用至資產的標籤：**

在 Azure 範疇目錄中，使用 **標籤** 篩選選項來只顯示具有特定標籤的檔案。 例如： 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="依標籤搜尋資產" lightbox="media/create-sensitivity-label/filter-search-results.png":::

例如：

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="在 Azure Blob 儲存體中，查看檔案上的敏感度標籤" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>查看分類和敏感度標籤的深入解析報表

使用 **分類** 和 **敏感度標籤** 報表，在 Azure 範疇中尋找已分類和標示資料的見解。

> [!div class="nextstepaction"]
> [分類見解](./classification-insights.md)

> [!div class="nextstepaction"]
> [敏感度標籤見解](sensitivity-insights.md)


