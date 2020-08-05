---
title: 取得加密金鑰資訊
titleSuffix: Azure Cognitive Search
description: 抓取索引或同義字對應中使用的加密金鑰名稱和版本，以便您可以在 Azure Key Vault 中管理金鑰。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: f6e356f868cdb2107a19084070a85a0388ab4af7
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554804"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>從索引和同義字對應取得客戶管理的金鑰資訊

在「Azure 認知搜尋」中，會在 Azure Key Vault 中建立、儲存和管理客戶管理的加密金鑰。 如果您需要判斷物件是否已加密，或所使用的索引鍵名稱或版本，請使用 REST API 或 SDK，從索引或同義字對應定義抓取**encryptionKey**屬性。 

建議您在 Key Vault 上[啟用記錄功能](../key-vault/general/logging.md)，以便監視金鑰使用方式。

## <a name="get-the-admin-api-key"></a>取得管理 API 金鑰

若要從搜尋服務取得物件定義，您必須使用系統管理員許可權進行驗證。 取得系統管理員 API 金鑰的最簡單方式是透過入口網站。

1. 登入[Azure 入口網站](https://portal.azure.com/)並開啟 [搜尋服務] [總覽] 頁面。

1. 在左側按一下 [**金鑰**]，然後複製管理 API。 索引和同義字對應的抓取需要管理金鑰。

針對其餘步驟，請切換至 PowerShell 和 REST API。 入口網站不會顯示同義字對應，也不會顯示索引的加密金鑰屬性。

## <a name="use-powershell-and-rest"></a>使用 PowerShell 和 REST

執行下列命令來設定變數，並取得物件定義。

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>後續步驟

既然您知道使用了哪一個加密金鑰和版本，您可以在 Azure Key Vault 中管理金鑰，或檢查其他設定。

+ [快速入門：使用 PowerShell 從 Azure Key Vault 設定及擷取祕密](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)

+ [在 Azure 認知搜尋中設定客戶管理的金鑰以進行資料加密](search-security-manage-encryption-keys.md)