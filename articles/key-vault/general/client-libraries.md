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
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263778"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault 的用戶端程式庫

Azure Key Vault 的用戶端程式庫可讓您以程式設計方式存取各種語言的 Key Vault 功能，包括 .NET、Python、Java 和 Javascript。

## <a name="client-libraries-per-language-and-object"></a>每個語言和物件的用戶端程式庫

根據下表，每個 SDK 各有不同的用戶端程式庫可用於祕密、金鑰和憑證。

| Language | 密碼 | 索引鍵 | 憑證 |
|--|--|--|--|
| .NET | - [API 參考](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet 套件](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [快速入門](../secrets/quick-create-net.md) | - [API 參考](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet 套件](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API 參考](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet 套件](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [API 參考](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pypi 套件](https://pypi.org/project/azure-keyvault-secrets/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [快速入門](../secrets/quick-create-python.md) |- [API 參考](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pypi 套件](https://pypi.org/project/azure-keyvault-keys/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [快速入門](../keys/quick-create-python.md) | - [API 參考](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pypi 套件](https://pypi.org/project/azure-keyvault-certificates/)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [快速入門](../certificates/quick-create-python.md) |
| Java | - [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [快速入門](../secrets/quick-create-java.md) |- [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [API 參考](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [NPM 套件](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [快速入門](../secrets/quick-create-node.md) |- [API 參考](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [NPM 套件](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API 參考](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [NPM 套件](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>後續步驟

- 參閱 [Azure Key Vault 開發人員指南](developers-guide.md)
- 深入了解[適用於 Azure Key Vault 的受控識別](managed-identity.md)