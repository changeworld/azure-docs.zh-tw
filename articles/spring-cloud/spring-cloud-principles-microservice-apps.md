---
title: 適用于 Azure 春季 Cloud 微服務 apps 的 JAVA 和基底 OS
description: 為 Azure 春季 Cloud 微服務 apps 維持狀況良好的 JAVA 和基本作業系統的原則
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8cda46a011ae92f26a15a4e9a918559801ce299c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906871"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>適用於 Spring 微服務應用程式的 Java 和基底 OS

**本文適用於：** ✔️ Java

以下是針對春季微服務應用程式維護狀況良好的 JAVA 和基本作業系統的原則。
## <a name="principles-for-healthy-java-and-base-os"></a>狀況良好的 JAVA 和基底 OS 原則
* 必須是跨層的相同基底作業系統-基本 |標準 |溢價。
    * 目前，Azure 上的應用程式春季雲端使用混合的 Debian 10 和 Ubuntu 18.04。
    * VMware build service 使用 Ubuntu 18.04。
* 無論部署起始點為何，都應該是相同的基底作業系統-來源 |Jar
    * 目前，Azure 上的應用程式春季雲端使用混合的 Debian 10 和 Ubuntu 18.04。
* 基礎作業系統應該是沒有安全性弱點。
    * Debian 10 基本作業系統有147的開放 Cve。
    * Ubuntu 18.04 基本作業系統有132開啟 Cve。
* 應使用 JRE-無周邊。
    * 目前，Azure 上的應用程式春季 Cloud 使用 JDK。 JRE-無周邊是較小的映射。
* 應使用最新的 JAVA 組建。
    * 目前，Azure 上的應用程式春季 Cloud 使用 JAVA 8 組建242。 這是過期的組建。
 
Azul 系統將會持續掃描基本作業系統的變更，並讓最後一個預先建立的映射保持最新狀態。 Azure 春季 Cloud 會尋找映射的變更，並在部署期間持續更新。
 
## <a name="faq-for-azure-spring-cloud"></a>Azure Spring Cloud 的常見問題集

* 支援的 JAVA 版本為何？ 主要版本和組建編號。
    * 支援 LTS 版本-JAVA 8 和11。
    * 使用最新的組建，例如，目前的 JAVA 8 組建252和 JAVA 11 組建7。
* 哪些人建立了這些 JAVA 執行時間？
    * Azul 系統。
* 映射的基本作業系統是什麼？
    * Ubuntu 20.04 LTS (焦距 Fossa) 。 應用程式將繼續保持最新的 Ubuntu LTS 版本。
    * 請參閱 [Ubuntu 20.04 LTS (焦距 Fossa) ](http://releases.ubuntu.com/focal/)
* 如何下載適用于本機開發的支援 JAVA 執行時間？ 
    * 請參閱 [安裝適用于 azure 和 Azure Stack 的 JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* 如何在 JAVA 執行時間層級取得問題的支援？
    * 使用 Azure 支援服務來開啟支援票證。
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Azure 春季雲端上的預設部署

> ![預設部署](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>後續步驟

* [快速入門：部署您的第一個 Azure Spring Cloud 應用程式](spring-cloud-quickstart.md)
* [適用于 Azure 和 Azure Stack 的 JAVA 長期支援](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
