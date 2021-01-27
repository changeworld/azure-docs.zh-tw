---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: c29c7abf0964edb7a5427d8c2cfe62e066d13bfb
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900809"
---
在您開始前，請確定：

* 已針對 Azure Stack Edge 資源啟用您的 Microsoft Azure 訂用帳戶。 確定您所使用的是受支援的訂用帳戶，例如 [Microsoft Enterprise 合約 (EA)](https://azure.microsoft.com/overview/sales-number/)、[雲端方案提供者 (CSP)](/partner-center/azure-plan-lp) 或 [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)。
* 您在 Azure Stack Edge Pro/Azure 儲存體閘道、IoT 中樞及 Azure 儲存體資源的資源群組層級上，具有擁有者或參與者存取權限。

  * 若要建立任何 Azure Stack Edge 資源，您應該要有以資源群組層級作為範圍的參與者權限 (或更高權限)。 您也需要確定已註冊 `Microsoft.DataBoxEdge` 提供者。 如需有關如何註冊的資訊，請移至[註冊資源提供者](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)。
  * 若要建立任何 IoT 中樞資源，請確定已註冊該 Microsoft.Devices 提供者。 如需有關如何註冊的資訊，請移至[註冊資源提供者](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)。
  * 同樣的，若要建立儲存體帳戶資源，您需要以資源群組層級作為範圍的參與者存取權限 (或更高權限)。 根據預設，Azure 儲存體是已註冊的資源提供者。
* 您有 Azure Active Directory 圖形 API 的管理員或使用者存取權。 如需詳細資訊，請參閱 [Azure Active Directory 圖形 API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。
* 您擁有的 Microsoft Azure 儲存體帳戶具有存取認證。