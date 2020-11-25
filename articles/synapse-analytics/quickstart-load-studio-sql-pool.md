---
title: 快速入門：使用專用 SQL 集區大量載入資料
description: 使用 Synapse Studio，將資料大量載入 Azure Synapse Analytics 中的專用 SQL 集區。
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 11/16/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 3b32e7a1df0dbbf4d43a73f1e3e409a904ab88a3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660078"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>快速入門：使用 Synapse SQL 進行大量載入

在 Synapse Studio 中使用「大量載入」精靈時，載入資料的作業變得輕鬆無比。 此精靈會引導您使用 [COPY 陳述式](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)來建立 T-SQL 指令碼，以大量載入資料。 

## <a name="entry-points-to-the-bulk-load-wizard"></a>大量載入精靈的進入點

您現在只要在 Synapse Studio 內的下欄區域按一下滑鼠右鍵，即可使用專用 SQL 集區輕鬆地大量載入資料：

- 連結到工作區的 Azure 儲存體帳戶所擁有的檔案或資料夾 ![以滑鼠右鍵按一下儲存體帳戶中的檔案或資料夾](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>必要條件

- 此精靈會產生使用 Azure AD 傳遞來進行驗證的 COPY 陳述式。 [Azure AD 使用者必須可存取](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication)至少具有 ADLS Gen2 帳戶儲存體 Blob 資料參與者 Azure 角色的工作區。 

- 如果您要建立要作為載入目的地的新資料表，則必須擁有所需的 [COPY 陳述式使用權限](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions)和建立資料表權限。

- 與 ADLS Gen2 帳戶相關聯的連結服務 **必須能夠存取所要載入的檔案**/**資料夾**。 例如，如果連結的服務驗證機制是受控識別，則工作區受控識別必須至少有儲存體帳戶的儲存體 Blob 讀者權限。

- 如果您的工作區已啟用 VNet，請確定與來源資料和錯誤檔案位置的 ADLS Gen2 帳戶連結服務相關聯的整合式執行階段已啟用互動式撰寫。 自動結構描述偵測、預覽來源檔案內容，以及在精靈內瀏覽 ADLS Gen2 儲存體帳戶，都需要互動式撰寫功能。

### <a name="steps"></a>步驟

1. 在 [來源儲存位置] 面板中，選取儲存體帳戶和您要載入的檔案或資料夾。 精靈會自動嘗試偵測 Parquet 檔案。 如果無法確認 Parquet 檔案類型，則依預設會使用分隔符號文字 (CSV)。 
   ![選取來源位置](./sql/media/bulk-load/bulk-load-source-location.png)

2. 選取檔案格式設定，包括想要在其中寫入遭拒絕資料列 (錯誤檔案) 的儲存體帳戶。 目前僅支援 CSV 和 Parquet 檔案。

    ![選取檔案格式設定](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. 您可以選取 [預覽資料] 來查看 COPY 陳述式會如何剖析檔案，以協助您設定檔案格式設定。 每當您變更檔案格式設定時，請選取 [預覽資料] 以查看 COPY 陳述式會如何使用更新後的設定來剖析檔案：![預覽資料](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - 大量載入精靈不支援預覽具有多字元欄位結束字元的資料。 指定了多字元欄位結束字元時，大量載入精靈將會在單一資料行中預覽資料。 
> - COPY 陳述式支援指定多字元資料列結束字元；不過，在擲回錯誤的大量載入精靈中不提供此支援。

4. 選取您要用來載入的專用 SQL 集區，包括載入的適用對象是現有資料表還是新資料表：![選取目標位置](./sql/media/bulk-load/bulk-load-target-location.png)

5. 選取 [設定資料行對應]，以確定您具有適當的資料行對應。 請注意，如果已啟用「推斷資料行名稱」，則會自動偵測資料行名稱。 在新的資料表中，設定資料行對應對於更新目標資料行資料類型很重要：![設定資料行對應](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. 選取 [開啟指令碼] 就會產生 T-SQL 指令碼，其中包含要從資料湖載入的 COPY 陳述式：![開啟 SQL 指令碼](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>後續步驟

- 如需 COPY 功能的詳細資訊，請參閱 [COPY 陳述式](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)一文
- 參閱[資料載入概觀](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)一文
