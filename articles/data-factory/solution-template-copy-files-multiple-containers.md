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
ms.openlocfilehash: f78d0b02c9790234a63ef64200dcab72bc64c033
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629420"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>使用 Azure Data Factory 複製多個資料夾

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明解決方案範本，您可以使用多個複製活動，在檔案型存放區之間複製容器或資料夾，其中每個複製活動都應該複製單一容器或資料夾。 

> [!NOTE]
> 如果您想要從單一容器複製檔案，使用 [資料複製工具](copy-data-tool.md) 來建立具有單一複製活動的管線會更有效率。 本文中的範本比您在這個簡單的案例中所需的還多。

## <a name="about-this-solution-template"></a>關於此解決方案範本

此範本會列舉來源儲存體存放區上指定父資料夾的資料夾。 然後，它會將每個資料夾複製到目的地存放區。

範本包含三個活動：
- **GetMetadata** 會掃描您的來源儲存體存放區，並從指定的父資料夾中取得子資料夾清單。
- **ForEach** 會從 **GetMetadata** 活動取得子資料夾清單，然後逐一查看清單，並將每個資料夾傳遞給複製活動。
- **複製** 會將每個資料夾從來源儲存體存放區複製到目的地存放區。

範本會定義下列參數：
- *SourceFileFolder* 是資料來源存放區的父資料夾路徑的一部分： *SourceFileFolder/SourceFileDirectory* ，您可以在其中取得子資料夾的清單。 
- *SourceFileDirectory* 是資料來源存放區的父資料夾路徑的一部分： *SourceFileFolder/SourceFileDirectory* ，您可以在其中取得子資料夾的清單。 
- *DestinationFileFolder* 是父資料夾路徑的一部分： *DestinationFileFolder/DestinationFileDirectory* ，檔案將會複製到目的地存放區。 
- *DestinationFileDirectory* 是父資料夾路徑的一部分： *DestinationFileFolder/DestinationFileDirectory* ，檔案將會複製到目的地存放區。 

如果您想要在儲存體存放區之間的根資料夾下複製多個容器，您可以將這四個參數輸入為 */* 。 如此一來，您就可以複寫儲存體存放區之間的所有專案。

## <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至 [ **複製檔案存放區之間的多個** 檔案] 範本。 建立與您的來源儲存體存放區的 **新** 連接。 來源儲存體存放區是您想要從多個容器複製檔案的位置。

    ![建立與來源的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 建立與目的地儲存體存放區的 **新** 連接。

    ![建立與目的地的新連線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 選取 [使用此範本]。

    ![使用此範本](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 您會看到管線，如下列範例所示：

    ![顯示管線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 選取 [偵錯]，輸入 [參數]，然後選取 [完成]。

    ![執行管線](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 檢閱結果。

    ![檢閱結果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>後續步驟

- [使用具有 Azure Data Factory 的控制資料表從資料庫進行大量複製](solution-template-bulk-copy-with-control-table.md)

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
