---
title: 在 vnet 中執行 Azure 春季 Cloud 的客戶責任
description: 本文說明在 vnet 中執行 Azure 春季 Cloud 的客戶責任。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5ecf9e49887eb584269f724d5199cbfb014351e0
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986848"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>在 VNET 中執行 Azure 春季 Cloud 的客戶責任
本檔包含在虛擬網路中使用 Azure 春季 Cloud 的規格。

當您的虛擬網路中部署 Azure 春季雲端時，它會對虛擬網路外部的服務有輸出相依性。 基於管理和操作目的，Azure 春季雲端必須)  (Fqdn 來存取特定埠和完整功能變數名稱。 需要這些端點才能與 Azure 春季雲端管理平面通訊，以及下載並安裝核心 Kubernetes 叢集元件和安全性更新。

根據預設，Azure 春季雲端具有不受限制的輸出 (輸出) 的網際網路存取。 這種網路存取層級可讓您執行的應用程式視需要存取外部資源。 如果您想要限制輸出流量，則必須可存取有限數目的埠和位址以進行維護工作。 保護輸出位址最簡單的解決方案是使用防火牆裝置，該裝置可以根據功能變數名稱來控制輸出流量。 例如，Azure 防火牆可以根據目的地的 FQDN 限制輸出 HTTP 和 HTTPS 流量。 您也可以設定慣用的防火牆和安全性規則，以允許這些必要的埠和位址。

## <a name="azure-spring-cloud-resource-requirements"></a>Azure 春季雲端資源需求 

以下是 Azure 春季雲端服務的資源需求清單。 一般需求是，您不應該修改 Azure 春季雲端和基礎網路資源所建立的資源群組。
- 請勿修改 Azure 春季雲端建立和擁有的資源群組。
  - 根據預設，這些資源群組會命名為 ap-.svc-rt_ [服務實例名稱]_[區域] * 和 ap_[服務實例名稱] _ [區域] *。
- 請勿修改 Azure 春季雲端使用的子網。
- 請勿在相同的子網中建立一個以上的 Azure 春季雲端服務實例。
- 使用防火牆來控制流量時， *請勿* 將下列輸出流量封鎖到 Azure 春季 Cloud 元件，以操作、維護及支援服務實例。

## <a name="azure-spring-cloud-network-requirements"></a>Azure 春季雲端網路需求

  | 目的地端點 | 連接埠 | 使用 | 注意 |
  |------|------|------|
  | *： 1194 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud：1194 | UDP：1194 | 基礎 Kubernetes 叢集管理。 | |
  | *： 443 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud：443 | TCP：443 | Azure 春季雲端服務管理。 | 服務實例 "requiredTraffics" 的資訊可在資源裝載的 "networkProfile" 區段中得知。 |
  | *： 9000 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud：9000 | TCP：9000 | 基礎 Kubernetes 叢集管理。 |
  | *： 123 *或* ntp.ubuntu.com:123 | UDP:123 | Linux 節點上的 NTP 時間同步處理。 | |
  | *. azure.io:443 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureContainerRegistry：443 | TCP：443 | Azure Container Registry。 | 可以 [在虛擬網路中啟用 Azure Container Registry 服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)來取代。 |
  | *. core.windows.net:443 和 *. core.windows.net:445 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -Storage： 443 and Storage：445 | TCP：443、TCP：445 | Azure 檔案儲存體 | 可以 [在虛擬網路中啟用 Azure 儲存體服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)來取代。 |
  | *. servicebus.windows.net:443 *或* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -EventHub：443 | TCP：443 | Azure 事件中樞。 | 可以 [在虛擬網路中啟用 Azure 事件中樞服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)來取代。 |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Azure 春季雲端 FQDN 需求/應用程式規則

Azure 防火牆會提供完整的功能變數名稱 (FQDN) 標記 **AzureKubernetesService** ，以簡化下列設定。

  | 目的地 FQDN | 連接埠 | 使用 |
  |------|------|------|
  | *. azmk8s.io | HTTPS:443 | 基礎 Kubernetes 叢集管理。 |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft Container Registry (MCR) 。 |
  | *.cdn.mscr.io | HTTPS:443 | Azure CDN 支援的 MCR 儲存體。 |
  | *.data.mcr.microsoft.com | HTTPS:443 | Azure CDN 支援的 MCR 儲存體。 |
  | <i>management.azure.com</i> | HTTPS:443 | 基礎 Kubernetes 叢集管理。 |
  | <i>login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory 驗證。 |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Microsoft 套件存放庫。 |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | 安裝必要二進位檔（例如 kubenet 和 Azure CNI）所需的存放庫。 |
  | *mscrl.microsoft.com* | HTTPS：80 | 必要的 Microsoft 憑證鏈路徑。 |
  | *crl.microsoft.com* | HTTPS：80 | 必要的 Microsoft 憑證鏈路徑。 |
  | *crl3.digicert.com* | HTTPS：80 | 協力廠商 SSL 憑證鏈路徑。 |

## <a name="see-also"></a>另請參閱
* [在私人網路中存取您的應用程式](spring-cloud-access-app-virtual-network.md)
* [使用應用程式閘道和 Azure 防火牆公開應用程式](spring-cloud-expose-apps-gateway-azure-firewall.md) 