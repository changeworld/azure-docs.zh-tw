---
title: 概念-Azure 春季雲端服務的安全性控制項
description: 使用內建在 Azure 春季雲端服務中的安全性控制項。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 25636611795a18761a1fc7135efd4abba5af5fe3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501102"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud 服務的安全性控制

**本文適用於：** ✔️ Java ✔️ C#

安全性控制項內建在 Azure 春季雲端服務中。

安全性控制是一項 Azure 服務的品質或功能，可提供服務預防、偵測及回應安全性弱點的能力。  針對每個控制項，我們使用 *[是] 或 [* *否* ] 來指出目前是否已在服務中進行。  針對不適用於服務的控制項，我們會使用 *N/A* 。 

**資料保護安全性控制措施**

| 安全性控制 | 是/否 | 注意 | 文件 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 在持續性儲存體中，使用者上傳的來源和成品、設定伺服器設定、應用程式設定和資料會儲存在 Azure 儲存體中，這會自動加密待用內容。<br><br>Config server cache、建立自上傳來源的執行時間二進位檔，以及應用程式存留期間內的應用程式記錄檔都會儲存到 Azure 受控磁片，這會自動加密待用內容。<br><br>從使用者上傳來源建立的容器映射會儲存在 Azure Container Registry 中，以自動加密靜止的影像內容。 | [待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)<br><br>[Azure 受控磁碟的伺服器端加密](../virtual-machines/disk-encryption.md)<br><br>[Azure Container Registry 中的容器映像儲存體](../container-registry/container-registry-storage.md) |
| 暫時性加密 | 是 | 依預設，使用者應用程式公用端點會針對輸入流量使用 HTTPS。 |  |
| API 呼叫加密 | 是 | 設定 Azure 春季雲端服務的管理呼叫會透過 HTTPS 的 Azure Resource Manager 呼叫進行。 | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**網路存取安全性控制措施**

| 安全性控制 | 是/否 | 注意 | 文件 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 服務標記 | 是 | 使用 **AzureSpringCloud** 服務標籤來定義 [網路安全性群組](../virtual-network/network-security-groups-overview.md#security-rules) 或 [azure 防火牆](../firewall/service-tags.md)上的輸出網路存取控制，以允許對 Azure 春季雲端應用程式的流量。<br><br>*注意：* 目前只有在2020/07/14 之後建立的新 Azure 春季雲端服務實例支援 **AzureSpringCloud** 服務標記。 | [服務標籤](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>後續步驟

* [快速入門：部署您的第一個 Azure Spring Cloud 應用程式](spring-cloud-quickstart.md)