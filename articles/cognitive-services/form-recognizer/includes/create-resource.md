---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996785"
---
移至 Azure 入口網站，並<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="建立新的表單辨識器資源" target="_blank">建立新的表單辨識器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 在 [建立] 窗格中，提供下列資訊：

|    |    |
|--|--|
| **名稱** | 資源的描述性名稱。 建議您使用描述性名稱，例如 *MyNameFormRecognizer*。 |
| **訂用帳戶** | 選取已獲存取權的 Azure 訂用帳戶。 |
| **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
| **定價層** | 資源的成本取決於您選擇的定價層和您的使用量。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
| **資源群組** | 將包含您的資源的 [Azure 資源群組](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 您可以建立新的群組，或將群組新增到既有的群組。 |

> [!NOTE]
> 一般而言，當您在 Azure 入口網站中建立認知服務資源時，您可以選擇建立多服務的訂用帳戶金鑰 (可跨多個認知服務使用) 或單一服務的訂用帳戶金鑰 (僅用於特定認知服務)。 不過，由於表單辨識器是預覽版本，而並未納入多服務的訂用帳戶中，因此您無法建立單一服務的訂用帳戶，除非您使用「歡迎使用」電子郵件中提供的連結。

當您的表單辨識器資源完成部署後，您可以從入口網站中的 [所有資源] 清單尋找並選取該項資源。 您可以在資源的 [金鑰和端點] 頁面，在 [資源管理] 下找到金鑰和端點。 在繼續進行之前，請先將這兩項儲存到暫存位置。
