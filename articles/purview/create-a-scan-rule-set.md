---
title: 建立掃描規則集
description: 在 Azure 範疇中建立掃描規則集，以快速掃描組織中的資料來源。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552141"
---
# <a name="create-a-scan-rule-set"></a>建立掃描規則集

在 Azure 範疇目錄中，您可以建立掃描規則集，讓您能夠快速掃描組織中的資料來源。

掃描規則集是將一組掃描規則群組在一起的容器，讓您可以輕鬆地將它們與掃描產生關聯。 例如，您可以為每個資料來源類型建立預設掃描規則集，然後根據預設，針對公司內的所有掃描使用這些掃描規則集。 您可能也希望具有適當許可權的使用者，可以根據業務需求，使用不同的設定來建立其他掃描規則集。

## <a name="steps-to-create-a-scan-rule-set"></a>建立掃描規則集的步驟

若要建立掃描規則集：

1. 從您的 Azure 範疇目錄中，選取 [ **管理中心**]。

1. 從左窗格中選取 [ **掃描規則集** ]，然後選取 [ **新增**]。

1. 從 [ **新的掃描規則集** ] 頁面的 [ **來源類型** ] 下拉式清單中，選取目錄掃描器所支援的資料來源。 您可以針對想要掃描的每個資料來源類型建立掃描規則集。

1. 為您的掃描規則設定 **名稱**。 長度上限為63個字元，不允許空格。 （選擇性）輸入 **描述**。 最大長度是 256 個字元。

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="顯示 [掃描規則集] 頁面的螢幕擷取畫面。" border="true":::

1. 選取 [繼續]。

   [ **選取檔案類型** ] 頁面隨即出現。 請注意，這個頁面上的 [檔案類型] 選項會根據您在前一頁選擇的資料來源類型而有所不同。 預設會啟用所有檔案類型。

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="顯示 [選取檔案類型] 頁面的螢幕擷取畫面。":::

   此頁面上的 [ **檔案類型** ] 選項可讓您包含或排除下列 office 檔案類型：。 doc、docm、.docx、.dot、odp、ods、.pps、.pdf、.pot、.pps、. ppsx、.ppt、. pptm、.pptx、. xlc、.xls、. .xlsb、. xlsm、.xlsx 和 .xlt.。

1. 選取或清除其核取方塊，以啟用或停用 [檔案類型] 磚。 如果您選擇 Data Lake 類型資料來源 (例如 Azure Data Lake Storage Gen2 或 Azure Blob) ，請啟用您想要將架構解壓縮和分類的檔案類型。

1. 針對特定資料來源類型，您也可以 [建立自訂檔案類型](#create-a-custom-file-type)。

1. 選取 [繼續]。

   [ **選取分類規則** ] 頁面隨即出現。 此頁面會顯示選取的 **系統規則** 和 **自訂規則**，以及選取的分類規則總數。 預設會選取所有 **系統規則** 核取方塊

1. 針對您想要包含或排除的規則，您可以依類別選取或清除 [ **系統規則** 分類規則] 核取方塊。

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="顯示 [選取分類規則] 頁面的螢幕擷取畫面。":::

1. 您可以展開 [類別目錄] 節點，然後選取或清除個別的核取方塊。 例如，如果 **阿根廷 DNI 號碼** 的規則有高誤報，您可以清除該特定核取方塊。

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="顯示如何選取系統規則的螢幕擷取畫面。":::

1. 選取 [ **建立** ] 以完成掃描規則集的建立。

### <a name="create-a-custom-file-type"></a>建立自訂檔案類型

Azure 範疇支援新增自訂延伸模組，以及在掃描規則集中定義自訂資料行分隔符號。

若要建立自訂檔案類型：

1. 遵循步驟 1-5 [，以建立掃描規則集](#steps-to-create-a-scan-rule-set) 或編輯現有的掃描規則集。

1. 在 [ **選取檔案類型** ] 頁面上，選取 [ **新增檔案類型** ] 以建立新的自訂檔案類型。

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="顯示如何從 [選取檔案類型] 頁面選取 [新增檔案類型] 的螢幕擷取畫面。":::

1. 輸入 **副檔名** 和選擇性 **描述**。

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="顯示 [新增自訂檔案類型] 頁面的螢幕擷取畫面。" border="true":::

1. **在內** 的檔案內容中，選取下列其中一個選項，以指定檔案中的檔內容類型：

   - 選取 [ **自訂分隔符號** ]，並輸入您自己的 **自訂分隔符號** (單一字元) 。

   - 選取 [ **系統檔案類型** ]，然後從 [ **系統檔案類型** ] 下拉式清單中選擇系統檔案類型 (例如 XML) 。

1. 選取 [ **建立** ] 以儲存自訂檔案。

   系統會返回 [ **選取檔案類型** ] 頁面，並將新的自訂檔案類型插入新的磚。

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="螢幕擷取畫面，顯示 [選取檔案類型] 頁面上的 [新增自訂檔案類型] 磚。":::

1. 如果您想要變更或刪除新的 [檔案類型] 圖格，請選取 [ **編輯** ]。

1. 選取 [ **繼續** ] 以完成掃描規則集的設定。

## <a name="system-scan-rule-sets"></a>系統掃描規則集

系統掃描規則集是針對每個 Azure 範疇目錄自動建立的 Microsoft 定義掃描規則集。 每個系統掃描規則集都與特定資料來源類型相關聯。 當您建立掃描時，可以將它與系統掃描規則集建立關聯。 每次 Microsoft 更新這些系統規則集時，您可以在目錄中更新這些規則集，並將更新套用至所有相關聯的掃描。

1. 若要查看系統掃描規則集的清單，請選取 [**管理中心**] 的 [**掃描規則集**]，然後選擇 [**系統**] 索引標籤。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="顯示系統掃描規則集清單的螢幕擷取畫面。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. 每個系統掃描規則集都有 **名稱**、 **來源類型** 和 **版本**。 如果您在 [ **版本** ] 欄中選取掃描規則集的版本號碼，就會看到與目前版本和舊版相關聯的規則 (如果有任何) 。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="顯示系統掃描規則集頁面的螢幕擷取畫面。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. 如果有可用的系統掃描規則集更新，您可以在 [**版本**] 欄中選取 [**更新**]。 在 [系統掃描規則] 頁面中，從 [ **選取要更新的新版本** ] 下拉式清單中選擇版本。 頁面會提供與新版本和目前版本相關聯的系統分類規則清單。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="顯示如何變更系統掃描規則集版本的螢幕擷取畫面。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>將掃描與系統掃描規則集建立關聯

當您 [建立掃描](tutorial-scan-data.md#scan-data-into-the-catalog)時，可以選擇將它與系統掃描規則集建立關聯，如下所示：

1. 在 [ **選取掃描規則集** ] 頁面上，選取 [系統掃描規則集]。

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="顯示如何針對掃描選取系統掃描規則集的螢幕擷取畫面。" lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. 選取 [ **繼續**]，然後選取 [ **儲存並執行**]。
