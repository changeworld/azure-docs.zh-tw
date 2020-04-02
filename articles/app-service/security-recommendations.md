---
title: 安全性建議
description: 實施安全建議,以説明履行我們共同責任模型中所述的安全義務。 提高應用的安全性。
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 50e2666aa533a5111055a095c612b58bfe6f9db4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546709"
---
# <a name="security-recommendations-for-app-service"></a>套用服務的安全建議

本文包含 Azure 應用服務的安全建議。 實施這些建議將説明您履行我們共同責任模型中所述的安全義務,並將提高 Web 應用解決方案的整體安全性。 有關 Microsoft 如何履行服務提供者職責的詳細資訊,請閱讀 Azure[基礎結構安全性](../security/fundamentals/infrastructure.md)。

## <a name="general"></a>一般

| 建議 | 註解 |
|-|-|----|
| 隨時掌握最新資訊 | 使用受支援的平臺、程式設計語言、協定和框架的最新版本。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 |
|-|----|
| 停用匿名存取 | 除非需要支援匿名請求,否則請禁用匿名訪問。 有關 Azure 應用服務認證選項的詳細資訊,請參閱[Azure 應用服務 的身份驗證與授權](overview-authentication-authorization.md)。|
| 需要認證 | 盡可能使用應用服務身份驗證模組,而不是編寫代碼來處理身份驗證和授權。 請參考[Azure 的服務的身份認證與授權](overview-authentication-authorization.md)。 |
| 使用經過身份驗證的存取保護後端資源 | 您可以使用使用者的身份或使用應用程式標識對後端資源進行身份驗證。 當您選擇使用應用程式識別時,請使用[託管識別](overview-managed-identity.md)。
| 需要客戶端憑證認證 | 用戶端憑證身份驗證僅允許來自可以使用您提供的證書進行身份驗證的用戶端的連接,從而提高安全性。 |

## <a name="data-protection"></a>資料保護

| 建議 | 註解 |
|-|-|
| 將 HTTP 重定向到 HTTP | 預設情況下,用戶端可以使用 HTTP 或 HTTPS 連接到 Web 應用。 我們建議將 HTTP 重定向到 HTTPTP,因為 HL/TLS 協定來提供安全連線,該連接既經過加密又經過身份驗證。 |
| 加密與 Azure 資源的通訊 | 當應用連接到 Azure 資源(如[SQL 資料庫](https://azure.microsoft.com/services/sql-database/)或[Azure 儲存](/azure/storage/))時,連接將保留在 Azure 中。 由於連接通過 Azure 中的共享網路,因此應始終加密所有通信。 |
| 可能需要最新的 TLS 版本 | 自 2018 年起,新的 Azure 應用服務應用使用 TLS 1.2。 較新版本的 TLS 包括對舊協定版本的安全改進。 |
| 使用 FTPS | App Service 同時支援使用 FTP 和 FTPS 來部署您的檔案。 盡可能使用 FTPS 而不是 FTP。 如有其中一個或兩個通訊協定都不在使用中，您應該[停用它們](deploy-ftp.md#enforce-ftps)。 |
| 保護應用程式資料 | 不要在代碼或配置檔中儲存應用程式機密,例如資料庫認證、API權杖或私密金鑰。 普遍接受的方法是使用您所選語言的標準模式，將它們當作[環境變數](https://wikipedia.org/wiki/Environment_variable)存取。 在 Azure 應用服務中,可以通過[應用設定](web-sites-configure.md)和[連接字串](web-sites-configure.md)定義環境變數。 應用設定和連接字串在 Azure 中加密儲存。 應用設置僅在應用啟動時注入應用的進程記憶體之前進行解密。 加密金鑰會定期輪替。 或者,您可以將 Azure 應用服務應用與[Azure 密鑰保管庫](/azure/key-vault/)整合,以便進行進階機密管理。 藉由[利用受控識別存取 Key Vault](../key-vault/tutorial-web-application-keyvault.md)，App Service 應用程式便可安全地存取您所需的祕密。 |

## <a name="networking"></a>網路功能

| 建議 | 註解 |
|-|-|
| 使用靜態 IP 限制 | Windows 上的 Azure 應用服務允許您定義允許存取應用的 IP 位址清單。 允許清單可以包含個別 IP 位址，或以子網路遮罩所定義的 IP 位址範圍。 如需詳細資訊，請參閱 [Azure App Service 靜態 IP 限制](app-service-ip-restrictions.md)。  |
| 使用隔離的定價層 | 除了隔離的定價層外,所有層都在 Azure 應用服務中的共用網路基礎結構上運行應用。 隔離層通過在專用[應用服務環境中](environment/intro.md)運行應用,為您提供完整的網路隔離。 App Service 環境就會在您擁有的 [Azure 虛擬網路](/azure/virtual-network/)執行個體中執行。|
| 存取本地資源時使用安全連線 | 您可以使用[混合連線](app-service-hybrid-connections.md)、[虛擬網路整合](web-sites-integrate-with-vnet.md)或[應用服務環境](environment/intro.md)連接到本地資源。 |
| 限制對入站網路流量的暴露 | 網路安全組允許您限制網路訪問並控制暴露終結點的數量。 有關詳細資訊,請參閱[如何控制到應用服務環境的入站流量](environment/app-service-app-service-environment-control-inbound-traffic.md)。 |

## <a name="monitoring"></a>監視

| 建議 | 註解 |
|-|-|
|使用 Azure 安全中心標準層 | [Azure 安全中心](../security-center/security-center-app-services.md)與 Azure 應用服務本機集成。 它可以運行評估並提供安全建議。 |

## <a name="next-steps"></a>後續步驟

請諮詢應用程式供應商,瞭解是否有其他安全要求。 有關開發安全應用程式的詳細資訊,請參閱[安全開發文件](../security/fundamentals/abstract-develop-secure-apps.md)。
