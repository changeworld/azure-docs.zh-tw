---
title: Azure Batch 中的檔案和目錄
description: 從開發觀點了解檔案和目錄，以及如何在 Azure Batch 工作流程中使用。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: e7babb7e2cfdbbe78f61be766c549c1e80cacf98
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790865"
---
# <a name="files-and-directories-in-azure-batch"></a>Azure Batch 中的檔案和目錄

在 Azure Batch 中，每個工作會在其工作目錄下建立零個或多個檔案和目錄。 此工作目錄可用來儲存工作所執行的程式、工作所處理的資料，以及工作所執行之處理的輸出。 工作使用者擁有工作的所有檔案和目錄。

Batch 服務會在節點上公開檔案系統的一部分作為「根目錄」 。 工作可藉由參考 `AZ_BATCH_NODE_ROOT_DIR` 環境變數來存取根目錄。 如需有關如何使用環境變數的詳細資訊，請參閱 [工作的環境設定](jobs-and-tasks.md#environment-settings-for-tasks)。

根目錄包含下列目錄結構：

![Compute node directory structure][media\files-and-directories\node-folder-structure.png]

- **applications**：包含在計算節點上所安裝應用程式套件的詳細資訊。 工作可藉由參考 `AZ_BATCH_APP_PACKAGE` 環境變數來存取這個目錄。

- **fsmounts**：此目錄包含裝載於計算節點上的任何檔案系統。 工作可藉由參考 `AZ_BATCH_NODE_MOUNTS_DIR` 環境變數來存取這個目錄。 如需詳細資訊，請參閱[在 Batch 集區上裝載虛擬檔案系統](virtual-file-mount.md)。

- **共用**：此目錄允許對「所有」在節點上執行的工作進行讀取/寫入存取。 任何在節點上執行的工作都可以建立、讀取、更新和刪除此目錄中的檔案。 工作可藉由參考 `AZ_BATCH_NODE_SHARED_DIR` 環境變數來存取這個目錄。

- **啟動**：啟動工作使用這個目錄做為它的工作目錄。 由啟動工作下載到的節點所有檔案都會儲存在這裡。 啟動工作可以建立、讀取、更新和刪除此目錄下的檔案。 工作可藉由參考 `AZ_BATCH_NODE_STARTUP_DIR` 環境變數來存取這個目錄。

- **volatile**：此目錄適用於內部用途。 不保證此目錄中的任何檔案或目錄本身未來將會存在。

- **workitems**：此目錄包含計算節點上的作業及其工作的目錄。

    在 **workitems** 目錄中，會針對在節點上執行的每個工作建立 **Tasks** 目錄。 可藉由參考 `AZ_BATCH_TASK_DIR` 環境變數來存取這個目錄。
    
    在每個 **Tasks** 目錄中，Batch 服務會建立由 `AZ_BATCH_TASK_WORKING_DIR` 環境變數指定唯一路徑的工作目錄 (`wd`)。 這個目錄可供讀取/寫入工作。 工作可以建立、讀取、更新和刪除此目錄下的檔案。 此目錄會根據工作指定的「RetentionTime」  條件約束而保留。

    `stdout.txt` 和 `stderr.txt` 檔案會在工作執行期間寫入至 **Tasks** 資料夾。

> [!IMPORTANT]
> 當節點從集區移除時，也會移除所有儲存在節點上的檔案。

## <a name="next-steps"></a>後續步驟

- 了解 Azure Batch 中的[錯誤處理和偵測](error-handling.md)。