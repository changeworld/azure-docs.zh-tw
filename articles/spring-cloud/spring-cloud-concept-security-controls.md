---
title: 概念-Azure 春季雲端服務的安全性控制項
description: 使用內建于 Azure 春季雲端服務的安全性控制。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594979"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud 服務的安全性控制
安全性控制內建于 Azure 春季雲端服務中。

安全性控制是 Azure 服務的品質或功能，可提供服務預防、偵測及回應安全性弱點的能力。  針對每個控制項，我們使用 *[是] 或 [* *否*] 來表示它目前是否為服務的位置。  針對不適用於服務的控制項，我們使用*N/A* 。 

**資料保護安全性控制**

| 安全性控制 | 是/否 | 備忘錄 | 文件 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | 是 | 使用者上傳的來源和成品、設定伺服器設定、應用程式設定，以及持續性儲存體中的資料會儲存在 Azure 儲存體中，這會自動將待用內容加密。<br><br>設定伺服器快取、從上傳來源建立的執行時間二進位檔，以及應用程式存留期期間的應用程式記錄會儲存至 Azure 受控磁片，這會自動將待用內容加密。<br><br>從使用者上傳來源建立的容器映射會儲存在 Azure Container Registry 中，這會自動加密待用影像內容。 | [待用資料的 Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Azure 受控磁片的伺服器端加密](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Azure Container Registry 中的容器映像儲存體](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| 暫時性中的加密 | 是 | 根據預設，使用者應用程式的公用端點會針對輸入流量使用 HTTPS。 |  |
| API 呼叫加密 | 是 | 設定 Azure 春季雲端服務的管理呼叫會透過 HTTPS Azure Resource Manager 呼叫進行。 | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

