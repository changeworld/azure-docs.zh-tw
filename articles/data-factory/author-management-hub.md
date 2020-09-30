---
title: 管理中樞
description: 在 Azure Data Factory 管理中樞內管理您的連線、原始檔控制設定和全域撰寫屬性
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.date: 06/02/2020
ms.openlocfilehash: df5a13b3d7c3fea98cd571a241b9d25e36ffeea6
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570628"
---
# <a name="management-hub-in-azure-data-factory"></a>Azure Data Factory 中的管理中樞

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

管理中樞（由 Azure Data Factory UX 中的 [ *管理* ] 索引標籤所存取）是裝載資料處理站全域管理動作的入口網站。 您可以在這裡管理資料存放區和外部計算、原始檔控制設定和觸發程式設定的連接。

## <a name="manage-connections"></a>管理連接

### <a name="linked-services"></a>連結的服務

連結的服務會定義連接資訊，讓 Azure Data Factory 連接到外部資料存放區和計算環境。 如需詳細資訊，請參閱 [連結服務概念](concepts-linked-services.md)。 連結服務的建立、編輯和刪除作業是在管理中樞中完成。

![管理連結服務](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>整合執行時間

整合執行時間是 Azure Data Factory 所使用的計算基礎結構，可提供跨不同網路環境的資料整合功能。 如需詳細資訊，請瞭解 [整合執行時間概念](concepts-integration-runtime.md)。 在管理中樞內，您可以建立、刪除和監視您的整合執行時間。

![管理整合執行時間](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>管理原始檔控制

### <a name="git-configuration"></a>Git 設定

在管理中樞中，查看並編輯已設定的 git 存放庫設定。 如需詳細資訊，請瞭解 [Azure Data Factory 中的原始檔控制](source-control.md)。

![管理 git 存放庫](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>參數化範本

若要在從共同作業分支發行時覆寫產生的 Resource Manager 範本參數，您可以產生或編輯自訂參數檔案。 如需詳細資訊，請瞭解如何 [在 Resource Manager 範本中使用自訂參數](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)。 只有在 git 存放庫中工作時，才可以使用參數化範本。 如果工作分支中的 *arm-template-parameters-definition.js* 檔案不存在，則編輯預設範本將會產生該檔案。

![管理自訂參數](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>管理撰寫

### <a name="triggers"></a>觸發程序

觸發程式會決定何時應該開始執行管線。 目前的觸發程式可以是時鐘排程、依定期間隔操作，或取決於事件。 如需詳細資訊，請瞭解觸發程式的 [執行](concepts-pipeline-execution-triggers.md#trigger-execution)。 在管理中樞內，您可以建立、編輯、刪除或查看觸發程式的目前狀態。

![顯示如何建立、編輯、刪除或查看觸發程式目前狀態的螢幕擷取畫面。](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>全域參數

全域參數是在資料處理站中的常數，可供任何運算式中的管線使用。 如需詳細資訊，請瞭解 [全域參數](author-global-parameters.md)。

![建立全域參數](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>後續步驟

瞭解如何 [設定 git 存放庫](source-control.md) 至 ADF


