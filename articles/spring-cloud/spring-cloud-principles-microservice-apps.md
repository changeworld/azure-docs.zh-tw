---
title: 適用于 Azure 春季雲端微服務應用程式的 JAVA 和基礎作業系統
description: 針對 Azure 春季雲端微服務應用程式維護狀況良好的 JAVA 和基礎作業系統的原則
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 1a8f48ff365ffae4a7013d2bfd537721ba2bc381
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172533"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>適用于春天微服務應用程式的 JAVA 和基礎作業系統
以下是針對春季微服務應用程式維護狀況良好的 JAVA 和基礎作業系統的原則。
## <a name="principles-for-healthy-java-and-base-os"></a>狀況良好的 JAVA 和基礎作業系統的原則
* 應該是跨層的相同基本作業系統-基本 |標準 |進階.
    * 目前，Azure 上的應用程式雲端使用混合 Debian 10 和 Ubuntu 18.04。
    * VMware 組建服務使用 Ubuntu 18.04。
* 應該是相同的基礎作業系統，而不考慮部署的起始點-來源 |餅乾
    * 目前，Azure 上的應用程式雲端使用混合 Debian 10 和 Ubuntu 18.04。
* 基本作業系統不應具備安全性弱點。
    * Debian 10 基礎作業系統有147開啟 Cve。
    * Ubuntu 18.04 基礎作業系統具有 132 open Cve。
* 應使用 JRE-無周邊。
    * 目前，Azure 上的應用程式春季雲端使用 JDK。 JRE-無周邊是較小的映射。
* 應使用最新的 JAVA 組建。
    * 目前，Azure 上的應用程式春季雲端使用 JAVA 8 組建242。 這是過時的組建。
 
Azul 系統會持續掃描基礎作業系統的變更，並將最後建立的映射保持在最新狀態。 Azure 春季雲端會尋找映射的變更，並在部署期間持續更新它們。
 
## <a name="faq-for-azure-spring-cloud"></a>Azure Spring Cloud 的常見問題集

* 支援哪些 JAVA 版本？ 主要版本和組建編號。
    * 支援 LTS 版本-JAVA 8 和11。
    * 會使用最新的組建-例如，目前的 JAVA 8 組建252和 JAVA 11 build 7。
* 誰建立了這些 JAVA 執行時間？
    * Azul 系統。
* 映射的基礎作業系統為何？
    * Ubuntu 20.04 LTS (焦 Fossa) 。 應用程式會繼續保持在最新的 Ubuntu LTS 版本。
    * 請參閱[Ubuntu 20.04 LTS (焦 Fossa) ](http://releases.ubuntu.com/focal/)
* 如何下載適用于本機開發的支援 JAVA 執行時間？ 
    * 請參閱[安裝適用于 Azure 的 JDK 和 Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* 如何在 JAVA 執行時間層級取得問題的支援？
    * 向 Azure 支援服務開啟支援票證。
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Azure 春季雲端上的預設部署

> ![預設部署](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>後續步驟
* [快速入門：使用 Azure 入口網站啟動現有的 Azure 春季雲端應用程式](spring-cloud-quickstart-launch-app-portal.md)
* [適用于 Azure 和 Azure Stack 的 JAVA 長期支援](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
