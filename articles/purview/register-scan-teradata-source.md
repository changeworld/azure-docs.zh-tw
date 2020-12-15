---
title: 註冊 Teradata 來源和設定掃描 (預覽)
description: 本文概述如何在 Azure Purview 中註冊 Teradata 來源並設定掃描。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841624"
---
# <a name="register-and-scan-teradata-source-preview"></a>註冊並掃描 Teradata 來源 (預覽)

本文概述如何在 Purview 中註冊 Teradata 來源並設定掃描。

> [!IMPORTANT]
> 此資料來源目前為預覽狀態。 您可以親身體驗並提供意見反應。

## <a name="supported-capabilities"></a>支援的功能

Teradata 來源支援 **完整掃描** 以從 Teradata 資料庫擷取中繼資料，並擷取資料資產之間的 **譜系**。

## <a name="prerequisites"></a>必要條件

- 連接器僅支援位於內部部署網路、Azure 虛擬網路或 Amazon 虛擬私人雲端中的資料存放區。 因此您需要設定[自我裝載整合執行階段](manage-integration-runtimes.md)才能連線。

- 請確定已在安裝自我裝載整合執行階段的虛擬機器上安裝 Java Runtime Environment (JRE)。
 
- 請確定自我裝載整合執行階段電腦上已安裝「C++ 可轉散發套件 2012 Update 4」。 如果尚未安裝，請從[這裡](https://www.microsoft.com/download/details.aspx?id=30679)下載。

- 您必須在內部部署虛擬機器上手動安裝名為 Teradata JDBC Driver 的驅動程式。 可以從 [Teradata 網站](https://downloads.teradata.com/)下載可執行檔 JAR 檔案。

    > [!Note] 
    > 該驅動程式應可供 VM 中的所有帳戶存取。 請不要在使用者帳戶中安裝。

- 支援的 Teradata 資料庫版本為 **12.x 到 16.x**。 您必須具備要掃描之 Teradata 來源的讀取權限。

### <a name="feature-flag"></a>功能旗標

您可在功能旗標後方註冊和掃描 Teradata 來源。 將下列內容附加至您的 URL：?feature.ext.datasource=%7b"teradata":"true"%7d 

例如，完整的 URL [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>設定掃描驗證
若要設定 Teradata 來源的驗證，唯一支援的方法是 **基本資料庫** 驗證

## <a name="register-a-teradata-source"></a>註冊 Teradata 來源

若要在您的資料目錄中註冊新的 Teradata 來源，請：

1. 瀏覽至您的 Purview 帳戶
2. 在左側導覽列中選取 [來源]
3. 選取 [註冊]
4. 在 **註冊來源** 上，選取 [Teradata]
5. 選取 [繼續]

在 **註冊來源 (Teradata)** 畫面上，執行下列動作：

1. 輸入會在目錄中列出的資料來源 **名稱**。
2. 輸入 **主機** 名稱以連線到 Teradata 來源。 此名稱也可以是 IP 位址，或是伺服器的完整連接字串。
3. 選取集合或建立新的集合 (選擇性)
4. **完成** 以註冊資料來源。

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="註冊來源選項" border="true":::

## <a name="creating-and-running-a-scan"></a>建立和執行掃描

若要建立並執行新的掃描，請執行下列動作：
1. 在管理中心 中，按一下 [整合執行階段]。 確定已設定自我裝載的整合執行階段。 如果未設定，請使用[此處](manage-integration-runtimes.md)所述的步驟建立自我裝載整合執行階段，以便在可存取內部部署網路的內部部署或 Azure VM 上掃描。

2. 接下來，瀏覽 **來源**

3. 選取已註冊的 Teradata 來源。

4. 選取 [+ 新增掃描]
 
5. 提供下列詳細資料：

    - 名稱：掃描的名稱

    - 透過整合執行階段連線：選取已設定的自我裝載整合執行階段

    - 驗證方法：資料庫驗證是目前唯一支援的選項。 預設通常會選取此選項

    - 使用者名稱：要連線到資料庫伺服器的使用者名稱。 此使用者名稱具有伺服器的讀取權限

    - 密碼：用來連線到資料庫伺服器的使用者密碼

    - 結構描述：要匯入之結構描述的清單子集，以分號分隔的清單表示。 例如 schema1; schema2。 如果清單是空的，則會匯入所有使用者結構描述。 預設會忽略所有系統結構描述 (例如 SysAdmin) 和物件。

        可接受的結構描述名稱模式 (使用 SQL LIKE 運算式語法) 包括使用 %，例如 A%; %B; %C%; D
        - 以 A 開頭或    
        - 以 B 結尾或    
        - 包含 C 或    
        - 等於 D

        不允許使用 NOT 和特殊字元

    - 驅動程式位置：客戶 VM 上 Teradata 驅動程式位置的完整路徑。 Teradata JDBC 驅動程式名稱必須是：com.teradata.jdbc.TeraDriver

    - 可用記憶體上限：客戶的 VM 中可由掃描流程使用的記憶體上限 ( GB 為單位)。 這取決於要掃描的 Teradata 來源大小。

    > [!Note] 
    > 作為 Thumb 規則，請為每1000 個資料表提供 2GB 的記憶體

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="設定掃描" border="true":::

6. 按一下 [繼續]。

7. 選擇您的掃描觸發程序。 您可以設定排程或執行一次掃描。

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="掃描觸發程序" border="true":::

8. 檢閱您的掃描，然後按一下 [儲存並執行]。

## <a name="viewing-your-scans-and-scan-runs"></a>檢視掃描和掃描執行

1. 瀏覽至管理中心。 在 [來源和掃描] 區段底下選取 [資料來源]。

2. 選取所需的資料來源。 您會看到該資料來源上的現有掃描清單。

3. 選取您有興趣檢視其結果的掃描。

4. 此頁面會顯示所有先前的掃描執行，以及每個掃描執行的計量和狀態。 也會顯示您的掃描是已排程或手動、有多少資產已套用分類、總共探索到多少資產、掃描的開始和結束時間，以及掃描持續時間總計。

## <a name="manage-your-scans"></a>管理掃描

若要管理或刪除掃描，請執行下列動作：

1. 瀏覽至管理中心。 選取 [來源和掃描] 區段底下的 [資料來源]，然後選取所需的資料來源。

2. 選取您想要管理的掃描。 您可以選取 [編輯] 來編輯掃描。

3. 您可以選取 [刪除] 來刪除掃描。

## <a name="next-steps"></a>後續步驟

- [瀏覽 Azure Purview 資料目錄](how-to-browse-catalog.md)
- [搜尋 Azure Purview 資料目錄](how-to-search-catalog.md)
