---
title: 從多個容器複製檔案
description: 瞭解如何使用解決方案範本，利用 Azure Data Factory 從多個容器複製檔案。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414826"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>使用 Azure Data Factory 從多個容器複製檔案
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明可用來從檔案存放區之間的多個容器複製檔案的解決方案範本。 例如，您可以使用它將您的 data lake 從 AWS S3 遷移至 Azure Data Lake 存放區。 或者，您可以使用範本，將所有專案從一個 Azure Blob 儲存體帳戶複寫到另一個。

> [!NOTE]
> 如果您想要從單一容器複製檔案，使用[資料複製工具](copy-data-tool.md)來建立具有單一複製活動的管線會更有效率。 這篇文章中的範本比您所需要的簡單案例還多。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會列舉來源儲存體存放區中的容器。 然後，它會將這些容器複製到目的地存放區。

範本包含三個活動：
- **GetMetadata**會掃描您的來源儲存體存放區，並取得容器清單。
- **ForEach**會從**GetMetadata**活動取得容器清單，然後逐一查看清單，並將每個容器傳遞至複製活動。
- [**複製**] 會將每個容器從來源儲存體存放區複製到目的地存放區。

範本會定義下列參數：
- *SourceFileFolder*是您的資料來源存放區的資料夾路徑，您可以在其中取得容器清單。 路徑是根目錄，其中包含多個容器檔案夾。 此參數的預設值為 `sourcefolder`。
- *SourceFileDirectory*是您的資料來源存放區根目錄下的子資料夾路徑。 此參數的預設值為 `subfolder`。
- *DestinationFileFolder*是要將檔案複製到目的地存放區中的資料夾路徑。 此參數的預設值為 `destinationfolder`。
- *DestinationFileDirectory*是要將檔案複製到目的地存放區中的子資料夾路徑。 此參數的預設值為 `subfolder`。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至 [在檔案**存放區之間複製多個**檔案] 範本。 建立與您的來源儲存體存放區的**新**連線。 來源儲存體存放區是您想要從多個容器複製檔案的位置。

    ![建立與來源的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 建立連至目的地儲存體存放區的**新**連線。

    ![建立與目的地的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 選取 [**使用此範本**]。

    ![使用此範本](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 您會看到管線，如下列範例所示：

    ![顯示管線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 選取 [ **Debug**]，輸入**參數**，然後選取 **[完成]**。

    ![執行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>後續步驟

- [使用具有 Azure Data Factory 的控制資料表，從資料庫進行大量複製](solution-template-bulk-copy-with-control-table.md)

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
