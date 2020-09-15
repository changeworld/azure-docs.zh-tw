---
title: Azure Key Vault 概觀 - Azure Key Vault | Microsoft Docs
description: Azure Key Vault 是安全的祕密存放區，可提供祕密、金鑰和憑證的管理，並以硬體安全性模組為後盾。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 500648b3037a81b39f474538ec062ef922b6e2df
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421640"
---
# <a name="about-azure-key-vault"></a>關於 Azure Key Vault

Azure Key Vault 可協助您解決下列問題：

- **祕密管理** - Azure Key Vault 可用來安全地儲存權杖、密碼、憑證、API 金鑰和其他祕密，並嚴密控制其存取
- **金鑰管理** - Azure Key Vault 也可作為金鑰管理解決方案。 Azure Key Vault 可讓您輕鬆地建立和控制用來加密資料的加密金鑰。 
- **憑證管理** - Azure Key Vault 也是一項服務，可讓您輕鬆地佈建、管理及部署 Azure 和您內部連線的資源所使用的公用和私人傳輸層安全性/安全通訊端層 (TLS/SSL) 憑證。 
- **儲存硬體安全性模組所支援的祕密** - 存放區中的祕密、金鑰和憑證會使用軟體金鑰 (標準層) 或受 FIPS 140-2 Level 2 驗證的 HSM 金鑰 (進階層) 加密 

## <a name="why-use-azure-key-vault"></a>為何使用 Azure Key Vault？

### <a name="centralize-application-secrets"></a>集中儲存應用程式祕密

在 Azure Key Vault 中集中儲存應用程式祕密，可讓您控制其散發。 Key Vault 可大幅降低不小心洩露祕密的風險。 使用 Key Vault 時，應用程式開發人員不再需要在其應用程式中儲存安全性資訊。 不需將安全性資訊儲存在應用程式中，就不需要讓此資訊成為程式碼的一部分。 例如，應用程式可能需要連線到資料庫。 您可以在 Key Vault 中妥善儲存連接字串，而不用在應用程式程式碼中儲存連接字串。

您的應用程式可以使用 URI 安全地存取其所需的資訊。 這些 URI 可讓應用程式擷取特定版本的祕密。 您不需要撰寫自訂程式碼來保護任何儲存在 Key Vault 中的祕密資訊。

### <a name="securely-store-secrets-and-keys"></a>安全地儲存秘密和金鑰

Azure 使用會業界標準演算法、金鑰長度和硬體安全性模組 (HSM) 來保護秘密和金鑰。 使用的 HSM 經過美國聯邦資訊處理標準 (FIPS) 140-2 Level 2 驗證。

金鑰保存庫的存取權需要先經過適當的驗證和授權，呼叫者 (使用者或應用程式) 才能取得存取權。 驗證會建立呼叫者的身分識別，授權則會判斷呼叫者可以執行的作業。

驗證會透過 Azure Active Directory 進行。 授權可以透過角色型存取控制 (RBAC) 或 Key Vault 存取原則進行。 在處理保存庫的管理時會使用 RBAC，而在嘗試存取保存庫中儲存的資料時會使用金鑰保存庫存取原則。

Azure Key Vault 可能受軟體或硬體 HSM 保護。 在您需要加強保證的情況下，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。 Microsoft 使用 nCipher 硬體安全性模型。 您可以使用 nCipher 工具將金鑰從您的 HSM 移至 Azure Key Vault。

最後，Azure Key Vault 依設計會使 Microsoft 無法看見或擷取您的資料。

### <a name="monitor-access-and-use"></a>監視存取和使用

在您建立一些 Key Vault 之後，您會想要監視存取金鑰和祕密的方式和時機。 您可以藉由啟用保存庫的記錄來監視活動。 您可以如下設定 Azure Key Vault：

- 封存至儲存體帳戶。
- 串流至事件中樞。
- 將記錄傳送至 Azure 監視器記錄。

您可以控制您的記錄，藉由限制存取權來保護它們，也可以刪除您不再需要的記錄。

### <a name="simplified-administration-of-application-secrets"></a>簡化應用程式祕密的管理

在儲存珍貴資料時，您必須採取幾個步驟。 安全性資訊必須受到保護，其必須遵循生命週期，而且必須保持高可用性。 Azure Key Vault 可藉由下列功能，簡化符合這些需求的程序：

- 無須具備硬體安全性模組的內部知識。
- 在短時間內相應增加，以符合組織的使用尖峰。
- 將區域內的 Key Vault 內容複寫到次要區域。 資料複寫可確保高可用性，並可讓管理員無須執行任何動作來觸發容錯移轉。
- 透過入口網站、Azure CLI 和 PowerShell 提供標準 Azure 系統管理選項。
- 自動對向公開 CA 購買的憑證執行一些作業，例如註冊或續約。

此外，Azure Key Vault 可讓您隔離應用程式祕密。 應用程式只能存取其有權存取的保存庫，且可能限定為只能執行特定作業。 您可以為每個應用程式建立 Azure Key Vault，並將 Key Vault 中儲存的祕密限制於特定應用程式和開發人員小組。

### <a name="integrate-with-other-azure-services"></a>與其他 Azure 服務整合

Key Vault 是 Azure 中的安全存放區，常用來簡化如下的案例：
-  [Azure 磁碟加密](../../security/fundamentals/encryption-overview.md)
-  SQL 伺服器和 Azure SQL Database 中的[一律加密]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)和[透明資料加密]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15)功能
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate)。 

Key Vault 本身可與儲存體帳戶、事件中樞和記錄分析整合。

## <a name="next-steps"></a>後續步驟

- 深入了解[金鑰、祕密與憑證](about-keys-secrets-certificates.md)
- [快速入門：使用 CLI 建立 Azure Key Vault](../secrets/quick-create-cli.md)
- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
