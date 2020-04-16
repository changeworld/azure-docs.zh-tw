---
title: 從多個容器複製檔案
description: 瞭解如何使用解決方案範本使用 Azure 數據工廠從多個容器複製檔。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414826"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>使用 Azure Data Factory 從多個容器複製檔案
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介紹了一個解決方案範本,可用於在檔案存儲之間從多個容器複製檔。 例如,可以使用它將數據湖從 AWS S3 遷移到 Azure 數據湖儲存。 或者,您可以使用範本將從一個 Azure Blob 儲存帳戶到另一個帳戶的所有內容複製。

> [!NOTE]
> 如果要從單個容器複製檔,則使用[「複製數據工具](copy-data-tool.md)」創建具有單個複製活動的管道更為高效。 本文中的範本比您所需的簡單方案更需要。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本枚舉源存儲存儲中的容器。 然後,它將這些容器複製到目標存儲。

範本包含三個活動：
- **GetMetadata**掃描來源儲存庫並獲取容器清單。
- **ForEach**從**GetMetadata**活動獲取容器清單,然後遍越清單,並將每個容器傳遞到"複製"活動。
- **將**每個容器從來源儲存儲存複製到目標儲存。

範本會定義下列參數：
- *SourceFileFolder*是資料來源儲存的資料夾路徑,您可以在其中獲取容器的清單。 路徑是根目錄,其中包含多個容器資料夾。 此參數的預設值為 `sourcefolder`。
- *SourceFileDirectory*是數據源存儲根目錄下的子資料夾路徑。 此參數的預設值為 `subfolder`。
- *目標檔案資料夾*是檔案將在目標儲存中複製到的資料夾路徑。 此參數的預設值為 `destinationfolder`。
- *目標檔目錄*是子資料夾路徑,其中檔將在目標存儲中複製到其中。 此參數的預設值為 `subfolder`。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 轉到**檔案儲存範本之間的"複製多個檔案容器**"。 建立到源存儲儲存**的新**連接。 源存儲存儲是從多個容器複製檔案的位置。

    ![建立與來源的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 建立到目標存儲儲存**的新**連接。

    ![建立與目的地的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 選擇**此樣本**。

    ![使用此範本](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 您將看到導管,例如以下範例所示:

    ![顯示管線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 選擇 **「除錯**」,輸入**參數**,然後選擇 **"完成**"。

    ![執行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure 資料工廠控制表從資料庫批次複製](solution-template-bulk-copy-with-control-table.md)

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
