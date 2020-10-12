---
title: 瞭解 Azure 春季雲端中的應用程式和部署
description: Azure 春季雲端中的應用程式和部署之間的差異。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: c053a7830e02eb7c460bd030ca3c6a10c00ea78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323632"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>瞭解 Azure 春季雲端中的應用程式和部署

**本文適用於：** ✔️ Java ✔️ C#

**應用程式** 和 **部署** 是 Azure 春季雲端資源模型中的兩個重要概念。 在 Azure 春季雲端中， *應用程式* 是一個商務應用程式或一個微服務的抽象概念。  部署時所部署的一或多個版本的*程式*代碼或二進位*檔。*  應用程式會在 *Azure 春季雲端服務實例*中執行，或只在 *服務實例*中執行，如下所示。

 ![應用程式和部署](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

單一 Azure 訂用帳戶內可以有多個服務實例，但當組成商務應用程式或微服務的所有應用程式都位於單一服務實例內時，Azure 春季雲端服務最容易使用。

Azure 春季雲端標準層可讓一個應用程式有一個生產部署和一個預備部署，讓您可以輕鬆地在其上進行 blue/綠部署。

## <a name="app"></a>應用程式
以下是在應用層級上定義的功能/屬性。

| 特性 | 描述 |
|:--|:----------------|
| 公用</br>端點 | 用來存取應用程式的 URL |
| 自訂</br>網域 | 保護自訂網域的 CNAME 記錄 |
| 服務</br>繫結 | 與其他 Azure 服務的現成連接 |
| 受管理</br>身分識別 | Azure Active Directory 的受控識別可讓您的應用程式輕鬆存取其他受 Azure AD 保護的資源，例如 Azure Key Vault |
| 持續性</br>儲存體 | 設定，可讓資料在應用程式重新開機後持續保存 |

## <a name="deployment"></a>部署

下列功能/屬性是在部署層級上定義，並會在交換生產/預備部署時交換。

| 特性 | 說明 |
|:--|:----------------|
| CPU | 每個應用程式實例的虛擬核心數目 |
| Memory | 每個應用程式實例的記憶體 Gb|
| 執行個體</br>Count | 應用程式實例的數目，手動或自動設定 |
| 自動調整 | 根據預先定義的規則和排程自動調整實例計數 |
| Jvm</br>選項。 | 設定 JVM 選項  |
| 環境</br>變數 | 設定環境變數 |
| 執行階段</br>版本 | JAVA 8/JAVA 11|

## <a name="restrictions"></a>限制

* **應用程式必須有一個生產部署**： API 已封鎖刪除生產部署。 您應該先將它交換到預備環境，然後再刪除。
* **應用程式最多可以有兩個部署**： API 已封鎖建立兩個以上的部署。 將新的二進位檔部署至現有的生產或預備部署。
* **基本層中不提供部署管理**： Blue-Green 部署功能使用標準層。

## <a name="see-also"></a>另請參閱
* [在 Azure 春季雲端中設定預備環境](spring-cloud-howto-staging-environment.md)
