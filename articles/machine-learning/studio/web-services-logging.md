---
title: Web 服務日誌記錄 - Azure 機器學習工作室（經典） |微軟文檔
description: 瞭解如何為機器學習工作室（經典）Web 服務啟用日誌記錄。 記錄提供可協助疑難排解 API 的其他資訊。
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217850"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>為 Azure 機器學習工作室（經典）Web 服務啟用日誌記錄

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

本文檔提供有關機器學習工作室（經典）Web 服務的日誌記錄功能的資訊。 日誌記錄提供了其他資訊，而不僅僅是錯誤號和消息，可説明您對機器學習工作室（經典）API 的呼叫進行故障排除。  

## <a name="how-to-enable-logging-for-a-web-service"></a>如何為 Web 服務啟用記錄

從[Azure 機器學習工作室（經典）Web 服務](https://services.azureml.net)門戶啟用日誌記錄。 

1. 登錄到 Azure[https://services.azureml.net](https://services.azureml.net)機器學習工作室（經典）Web 服務門戶。 對於經典 Web 服務，您還可以通過按一下 Studio 中的機器學習工作室（經典）Web 服務頁面（經典）上的 **"新 Web 服務體驗**"來訪問門戶。

   ![新的 Web 服務體驗連結](./media/web-services-logging/new-web-services-experience-link.png)

2. 在頂端的功能表列上，按一下 [Web 服務]**** 以取得新的 Web 服務，或按一下 [傳統 Web 服務]**** 以取得傳統 Web 服務。

   ![選取新的或傳統 Web 服務](./media/web-services-logging/select-web-service.png)

3. 對於新的 Web 服務，按一下 Web 服務名稱。 對於傳統 Web 服務，按一下 Web 服務名稱，然後在下一個頁面中按一下適當的端點。

4. 按一下頂端功能表列上的 [設定]****。

5. 將 **[啟用記錄]** 選項設定為 *[錯誤]* \(僅記錄錯誤) 或 *[所有]* \(適用於完整記錄)。

   ![選取記錄等級](./media/web-services-logging/enable-logging.png)

6. 按一下 [儲存]****。

7. 對於傳統 Web 服務，請建立 **ml-診斷**容器。

   所有的 Web 服務記錄會保存在與 Web 服務相關聯的儲存體帳戶中名為 **ml-診斷**的 blob 容器中。 對於新的 Web 服務，此容器會在您第一次存取 Web 服務時建立。 對於傳統 Web 服務，如果容器不存在，則您需要建立容器。 

   1. 在 [Azure 入口網站](https://portal.azure.com)中，移至與 Web 服務相關聯的儲存體帳戶。

   2. 在 [Blob 服務]**** 下，按一下 [容器]****。

   3. 如果容器 **ml-診斷**不存在，按一下 [+ 容器]****，將容器命名為「ml-診斷」，[存取型別]**** 選取「Blob」。 按一下 [確定]****。

      ![創建新容器以存儲診斷日誌](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> 對於經典 Web 服務，機器學習工作室中的 Web 服務儀表板（經典）也有一個開關來啟用日誌記錄。 不過，由於記錄現在是透過 Web 服務入口網站所管理，您需要透過入口網站來啟用記錄，如本文所述。 如果已在 Studio 中啟用了日誌記錄（經典），則在 Web 服務門戶中禁用日誌記錄並再次啟用它。


## <a name="the-effects-of-enabling-logging"></a>啟用記錄的效果
啟用日誌記錄後，Web 服務終結點的診斷和錯誤將記錄在與使用者工作區連結的 Azure 存儲帳戶中的**ml 診斷**blob 容器中。 這個容器針對所有與此儲存體帳戶相關聯的工作區，存放所有 Web 服務端點的診斷資訊。

記錄可使用任何可用於探索 Azure 儲存體帳戶的多種工具來檢視。 最簡單的方法就是瀏覽至 Azure 入口網站中的儲存體帳戶，按一下 [容器]****，然後按一下 [ml-診斷]**** 容器。  

## <a name="log-blob-detail-information"></a>記錄檔 blob 詳細資訊
在容器中的每個 blob，只會存放下列其中一項動作的診斷資訊：

* 批次執行方法的執行  
* 要求-回應方法的執行  
* 要求-回應容器的初始化

每個 blob 的名稱具有下列形式的前置詞︰ 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


其中的_記錄型別_是下列其中一個值：  

* 批次  
* 分數/要求  
* 分數/初始  

