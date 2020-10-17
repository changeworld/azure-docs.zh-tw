---
title: 安全性建議
description: 依照我們的共同責任模型中所述，實施安全性建議以協助滿足您的安全性義務。 改善您應用程式的安全性。
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 09dbe0fbefb8b90b4c4e1ddef57abf3b13856183
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148082"
---
# <a name="security-recommendations-for-app-service"></a>App Service 的安全性建議

本文包含 Azure App Service 的安全性建議。 實施這些建議可協助您依照共同責任模型中所述的方式滿足您的安全性義務，並且可提升 Web 應用程式解決方案的整體安全性。 如需 Microsoft 如何滿足服務提供者責任的詳細資訊，請參閱 [Azure 基礎結構安全性](../security/fundamentals/infrastructure.md)。

## <a name="general"></a>一般

| 建議 | 註解 |
|-|-|----|
| 隨時掌握最新資訊 | 使用最新版本的支援平臺、程式設計語言、通訊協定和架構。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 |
|-|----|
| 停用匿名存取 | 除非您需要支援匿名要求，否則請停用匿名存取。 如需 Azure App Service 驗證選項的詳細資訊，請參閱 [Azure App Service 中的驗證和授權](overview-authentication-authorization.md)。|
| 需要驗證 | 盡可能使用 App Service authentication 模組，而不是撰寫程式碼來處理驗證和授權。 請參閱 [Azure App Service 中的驗證和授權](overview-authentication-authorization.md)。 |
| 使用已驗證的存取來保護後端資源 | 您可以使用使用者的身分識別，或使用應用程式身分識別來驗證後端資源。 當您選擇使用應用程式身分識別時，請使用 [受控識別](overview-managed-identity.md)。
| 需要用戶端憑證驗證 | 用戶端憑證驗證藉由只允許來自可透過您提供的憑證進行驗證的用戶端進行連線，藉以改善安全性。 |

## <a name="data-protection"></a>資料保護

| 建議 | 註解 |
|-|-|
| 將 HTTP 重新導向至 HTTPs | 根據預設，用戶端可以使用 HTTP 或 HTTPS 來連線至 web 應用程式。 建議您將 HTTP 重新導向至 HTTPs，因為 HTTPS 會使用 SSL/TLS 通訊協定來提供安全連線，這兩者都經過加密及驗證。 |
| 加密 Azure 資源的通訊 | 當您的應用程式連線到 Azure 資源（例如 [SQL Database](https://azure.microsoft.com/services/sql-database/) 或 [Azure 儲存體](../storage/index.yml)）時，連線會保留在 Azure 中。 因為連線是透過 Azure 中的共用網路，所以您應該一律加密所有通訊。 |
| 需要最新的 TLS 版本 | 自2018起，新的 Azure App Service apps 會使用 TLS 1.2。 較新的 TLS 版本包含舊版通訊協定版本的安全性增強功能。 |
| 使用 FTPS | App Service 同時支援使用 FTP 和 FTPS 來部署您的檔案。 盡可能使用 FTPS 而非 FTP。 如有其中一個或兩個通訊協定都不在使用中，您應該[停用它們](deploy-ftp.md#enforce-ftps)。 |
| 保護應用程式資料 | 請勿將應用程式秘密（例如資料庫認證、API 權杖或私密金鑰）儲存在您的程式碼或設定檔中。 普遍接受的方法是使用您所選語言的標準模式，將它們當作[環境變數](https://wikipedia.org/wiki/Environment_variable)存取。 在 Azure App Service 中，您可以透過 [應用程式設定](./configure-common.md) 和 [連接字串](./configure-common.md)來定義環境變數。 應用程式設定和連接字串會以加密方式儲存在 Azure 中。 應用程式設定只會在應用程式啟動時插入您應用程式的進程記憶體之前進行解密。 加密金鑰會定期輪替。 或者，您可以將 Azure App Service 應用程式與 [Azure Key Vault](../key-vault/index.yml) 整合，以進行 advanced 秘密管理。 藉由[利用受控識別存取 Key Vault](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)，App Service 應用程式便可安全地存取您所需的祕密。 |

## <a name="networking"></a>網路功能

| 建議 | 註解 |
|-|-|
| 使用靜態 IP 限制 | Windows 上的 Azure App Service 可讓您定義允許存取應用程式的 IP 位址清單。 允許清單可以包含個別 IP 位址，或以子網路遮罩所定義的 IP 位址範圍。 如需詳細資訊，請參閱 [Azure App Service 靜態 IP 限制](app-service-ip-restrictions.md)。  |
| 使用隔離的定價層 | 除了隔離的定價層之外，所有階層都是在 Azure App Service 的共用網路基礎結構上執行您的應用程式。 隔離層可讓您在專用的 [App Service 環境](environment/intro.md)內執行您的應用程式，以完成網路隔離。 App Service 環境就會在您擁有的 [Azure 虛擬網路](../virtual-network/index.yml)執行個體中執行。|
| 存取內部部署資源時使用安全連線 | 您可以使用 [混合](app-service-hybrid-connections.md)式連線、 [虛擬網路整合](web-sites-integrate-with-vnet.md)或 [App Service 環境](environment/intro.md) 來連線至內部部署資源。 |
| 限制暴露于輸入網路流量 | 網路安全性群組可讓您限制網路存取，並控制公開端點的數目。 如需詳細資訊，請參閱 [如何控制 App Service 環境的輸入流量](environment/app-service-app-service-environment-control-inbound-traffic.md)。 |

## <a name="monitoring"></a>監視

| 建議 | 註解 |
|-|-|
|使用 Azure 資訊安全中心標準層 | [Azure 資訊安全中心](../security-center/defender-for-app-service-introduction.md) 原本就與 Azure App Service 整合。 它可以執行評量並提供安全性建議。 |

## <a name="next-steps"></a>後續步驟

請洽詢您的應用程式提供者，以查看是否有額外的安全性需求。 如需開發安全應用程式的詳細資訊，請參閱[安全開發文件](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)。