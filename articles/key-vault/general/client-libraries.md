---
title: Azure Key Vault 的用戶端程式庫 | Microsoft Docs
description: Azure Key Vault 的用戶端程式庫
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 2c48917e7205bca03380267c1c97469acc245fa6
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628022"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault 的用戶端程式庫

Azure Key Vault 的用戶端程式庫可讓您以程式設計方式存取各種語言的 Key Vault 功能，包括 .NET、Python、Java 和 JavaScript。

## <a name="client-libraries-per-language-and-object"></a>每個語言和物件的用戶端程式庫

根據下表，每個 SDK 各有不同的用戶端程式庫可用於金鑰保存庫、祕密、金鑰和憑證。

| Language | 密碼 | 索引鍵 | 憑證 | 金鑰保存庫 (管理平面)
|--|--|--|--|
| .NET | - [API 參考](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet 套件](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [快速入門](../secrets/quick-create-net.md) | - [API 參考](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet 套件](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API 參考](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet 套件](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) | - [API 參考](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API 參考](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pypi 套件](https://pypi.org/project/azure-keyvault-secrets/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [快速入門](../secrets/quick-create-python.md) |- [API 參考](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pypi 套件](https://pypi.org/project/azure-keyvault-keys/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [快速入門](../keys/quick-create-python.md) | - [API 參考](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pypi 套件](https://pypi.org/project/azure-keyvault-certificates/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [快速入門](../certificates/quick-create-python.md) | - [API 參考](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [Pypi 套件](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [快速入門](../secrets/quick-create-java.md) |- [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |- [API 參考](/java/api/com.microsoft.azure.management.keyvault?view=azure-java-stable)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API 參考](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [NPM 套件](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [快速入門](../secrets/quick-create-node.md) |- [API 參考](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [NPM 套件](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API 參考](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [NPM 套件](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |  - [API 參考](/javascript/api/@azure/arm-keyvault/?view=azure-node-latest)<br>- [NPM 套件](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>後續步驟

- 參閱 [Azure Key Vault 開發人員指南](developers-guide.md)
- 深入了解[向金鑰保存庫進行驗證](authentication.md)
- 深入了解[保護金鑰保存庫的存取權](secure-your-key-vault.md)
