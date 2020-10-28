---
title: 使用部署在 Azure 中的 SSIS 套件來開啟和儲存檔案
description: 了解當您將使用本機檔案系統的 SSIS 套件隨即轉移至 Azure 中的 SSIS 時，如何在內部部署與 Azure 中開啟和儲存檔案
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: a2b95e0de642dc17865643ec569e7220fe5581b2
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637491"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>在 SSIS 套件部署於 Azure 的情況下，於內部部署與 Azure 中開啟和儲存檔案

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文描述當您將使用本機檔案系統的 SSIS 套件隨即轉移至 Azure 中的 SSIS 時，如何在內部部署與 Azure 中開啟和儲存檔案。

## <a name="save-temporary-files"></a>儲存暫存檔案

如果您需要在單一套件執行期間儲存及處理暫存檔案，套件可以使用目前的工作目錄 (`.`) 或 Azure-SSIS Integration Runtime 節點的暫存資料夾 (`%TEMP%`)。

## <a name="use-on-premises-file-shares"></a>使用內部部署檔案共用

當您將使用本機檔案系統的套件隨即轉移至 Azure 中的 SSIS 時，若要繼續使用 **內部部署檔案共用** ，請執行下列動作：

1. 將檔案從本機檔案系統轉送至內部部署檔案共用。

2. 將內部部署檔案共用加入 Azure 虛擬網路。

3. 將您的 Azure-SSIS IR 加入相同的虛擬網路。 如需詳細資訊，請參閱[將 Azure-SSIS 整合執行階段加入虛擬網路](./join-azure-ssis-integration-runtime-virtual-network.md)。

4. 透過設定使用 Windows 驗證的存取認證，將您的 Azure-SSIS IR 連線至相同虛擬網路中的內部部署檔案共用。 如需詳細資訊，請參閱[使用 Windows 驗證連線至資料和檔案共用](ssis-azure-connect-with-windows-auth.md)。

5. 將您套件中的本機檔案路徑更新為指向內部部署檔案共用的 UNC 路徑。 例如，將 `C:\abc.txt` 更新為 `\\<on-prem-server-name>\<share-name>\abc.txt`。

## <a name="use-azure-file-shares"></a>使用 Azure 檔案共用

當您將使用本機檔案系統的套件隨即轉移至 Azure 中的 SSIS 時，若要使用 **Azure 檔案服務** ，請執行下列動作：

1. 將檔案從本機檔案系統轉送至 Azure 檔案服務。 如需詳細資訊，請參閱 [Azure 檔案服務](https://azure.microsoft.com/services/storage/files/)。

2. 透過設定使用 Windows 驗證的存取認證，將您的 Azure-SSIS IR 連線到 Azure 檔案服務。 如需詳細資訊，請參閱[使用 Windows 驗證連線至資料和檔案共用](ssis-azure-connect-with-windows-auth.md)。

3. 將您套件中的本機檔案路徑更新為指向 Azure 檔案服務的 UNC 路徑。 例如，將 `C:\abc.txt` 更新為 `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`。

## <a name="next-steps"></a>後續步驟

- 部署您的套件。 如需詳細資訊，請參閱[使用 SSMS 將 SSIS 專案部署到 Azure](/sql/integration-services/ssis-quickstart-deploy-ssms)。
- 執行您的套件。 如需詳細資訊，請參閱[在 Azure 中使用 SSMS 執行 SSIS 套件](/sql/integration-services/ssis-quickstart-run-ssms)。
- 排程您的套件。 如需詳細資訊，請參閱 [在 Azure 中排程 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)。