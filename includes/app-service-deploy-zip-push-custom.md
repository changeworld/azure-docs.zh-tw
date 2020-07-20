---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050398"
---
## <a name="deployment-customization"></a>部署自訂

部署程序假設您所推送的 .zip 檔案包含隨時可執行的應用程式。 根據預設，系統不會執行任何自訂。 若要啟用您在持續整合所獲得的相同建置程序，請將下列內容新增至您的應用程式設定：

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

當您使用 .zip 推送部署時，這項設定的預設值是 **false**。 持續整合部署的預設值則是 **true**。 設定為 **true** 時，系統會在部署期間使用您的部署相關設定。 您可以將這些設定設定為應用程式設定或設定於 .deployment 設定檔 (位於 .zip 檔案的根目錄) 中。 如需詳細資訊，請參閱部署參考中的[存放庫和部署相關設定](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)。