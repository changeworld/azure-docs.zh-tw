---
title: 概念-Azure 春季雲端服務的安全性控制項
description: 使用內建在 Azure 春季雲端服務中的安全性控制項。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1472da4c87dc4579a30290460fb7811cf228be47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892477"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud 服務的安全性控制

**本文適用於：** ✔️ Java ✔️ C#

安全性控制項內建在 Azure 春季雲端服務中。

安全性控制是一項 Azure 服務的品質或功能，可提供服務預防、偵測及回應安全性弱點的能力。  針對每個控制項，我們使用 *[是] 或 [* *否* ] 來指出目前是否已在服務中進行。  針對不適用於服務的控制項，我們會使用 *N/A* 。 

**資料保護安全性控制措施**

| 安全性控制 | 是/否 | 注意 | 文件 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 在持續性儲存體中，使用者上傳的來源和成品、設定伺服器設定、應用程式設定和資料會儲存在 Azure 儲存體中，這會自動加密待用內容。<br><br>Config server cache、建立自上傳來源的執行時間二進位檔，以及應用程式存留期間內的應用程式記錄檔都會儲存到 Azure 受控磁片，這會自動加密待用內容。<br><br>從使用者上傳來源建立的容器映射會儲存在 Azure Container Registry 中，以自動加密靜止的影像內容。 | [待用資料的 Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Azure 受控磁碟的伺服器端加密](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Azure Container Registry 中的容器映像儲存體](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| 暫時性加密 | 是 | 依預設，使用者應用程式公用端點會針對輸入流量使用 HTTPS。 |  |
| API 呼叫加密 | 是 | 設定 Azure 春季雲端服務的管理呼叫會透過 HTTPS 的 Azure Resource Manager 呼叫進行。 | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**網路存取安全性控制措施**

| 安全性控制 | 是/否 | 注意 | 文件 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 服務標記 | 是 | 使用 **AzureSpringCloud** 服務標籤來定義 [網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 或 [azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)上的輸出網路存取控制，以允許對 Azure 春季雲端應用程式的流量。<br><br>*注意：* 目前只有在2020/07/14 之後建立的新 Azure 春季雲端服務實例支援 **AzureSpringCloud** 服務標記。 | [服務標籤](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |

## <a name="next-steps"></a>後續步驟

* [快速入門：部署您的第一個 Azure Spring Cloud 應用程式](spring-cloud-quickstart.md)
