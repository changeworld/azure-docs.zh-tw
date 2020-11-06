---
title: Azure Data Factory 中的資料冗余 |Microsoft Docs
description: 瞭解 Azure Data Factory 中的中繼資料冗余機制
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: f71fdf66624d67939f915f91c2cc1dbe7553cad7
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332048"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure Data Factory 資料冗余**

Azure Data Factory 資料包含中繼資料 (管線、資料集、連結的服務、整合執行時間和觸發程式) 以及監視資料 (管線、觸發程式和活動執行) 。 

在巴西南部和東南亞) 以外的所有區域 (中，Azure Data Factory 資料會儲存並複寫到 [配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#azure-regional-pairs) ，以防止中繼資料遺失。 在區域性資料中心發生失敗時，Microsoft 可能會起始 Azure Data Factory 實例的區域性容錯移轉。 在大部分情況下，您不需要採取任何動作。 當 Microsoft 管理的容錯移轉完成時，您將能夠在容錯移轉區域中存取您的 Azure Data Factory。 

由於巴西南部和東南亞的資料落地需求，Azure Data Factory 資料只會儲存在 [本機區域](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage)中。 針對東南亞，所有資料都會儲存在新加坡。 針對巴西南部，所有資料都會儲存在巴西。 當區域因為嚴重損壞而遺失時，Microsoft 將無法復原您的 Azure Data Factory 資料。  

> [!NOTE]
> 因為此基礎結構通常是由客戶管理，所以 Microsoft 管理的容錯移轉不會套用至自我裝載整合執行時間 (SHIR) 。 如果已在 Azure VM 上設定 SHIR，則建議您利用 [azure site recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 來處理對另一個區域的 [azure VM 容錯移轉](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture) 。



## <a name="using-source-control-in-azure-data-factory"></a>**在 Azure Data Factory 中使用原始檔控制**

為了確保您能夠追蹤和審核對 Azure data factory 中繼資料所做的變更，您應該考慮為您的 Azure Data Factory 設定原始檔控制。 它也可讓您存取管線、資料集、連結服務和觸發程式的中繼資料 JSON 檔案。 Azure Data Factory 可讓您使用不同的 Git 存放庫 (Azure DevOps 和 GitHub) 。 

 瞭解如何 [在 Azure Data Factory 中設定原始檔控制](https://docs.microsoft.com/azure/data-factory/source-control)。 

> [!NOTE]
> 如果發生嚴重損壞 (區域) 遺失，可以手動或以自動化的方式布建新的 data factory。 建立新的 data factory 之後，您就可以從現有的 Git 存放庫還原管線、資料集和連結服務 JSON。 



## <a name="data-stores"></a>**資料存放區**

Azure Data Factory 可讓您在內部部署和雲端中的資料存放區之間移動資料。 為了確保資料存放區的商務持續性，您應該參考每一個資料存放區的商務持續性建議。 

 

## <a name="see-also"></a>另請參閱

- [Azure 區域配對](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Azure 中的資料存放區](https://azure.microsoft.com/global-infrastructure/data-residency/) 
