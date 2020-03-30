---
title: 茲比貝登錄 |微軟文檔
description: 本文為 azcopy 登錄命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295413"
---
# <a name="azcopy-login"></a>azcopy 登入

登錄到 Azure 活動目錄以訪問 Azure 存儲資源。

## <a name="synopsis"></a>概要

登錄到 Azure 活動目錄以訪問 Azure 存儲資源。

要授權到 Azure 存儲帳戶，必須在存儲帳戶、父資源組或父訂閱的上下文中將**存儲 Blob 資料參與者**角色指派給使用者帳戶。

此命令將使用作業系統內置機制緩存當前使用者的加密登錄資訊。

有關詳細資訊，請參閱示例。

> [!IMPORTANT]
> 如果使用命令列設置環境變數，則該變數將在命令列歷史記錄中可讀。 請考慮清除包含命令列歷史記錄中憑據的變數。 要防止變數出現在歷史記錄中，可以使用腳本提示使用者輸入其憑據，並設置環境變數。

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

使用預設 AAD 租戶 ID 設置為公共的互動式登入：

```azcopy
azcopy login
```

使用指定的租戶 ID 以對話模式登錄：

```azcopy
azcopy login --tenant-id "[TenantID]"
```

使用虛擬機器 （VM） 的系統分配標識登錄：

```azcopy
azcopy login --identity
```

使用 VM 的使用者分配標識和服務標識的用戶端 ID 登錄：

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

使用 VM 的使用者分配標識和服務標識的物件識別碼 登錄：

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

使用 VM 的使用者分配標識和服務標識的資源識別碼 登錄：

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

使用用戶端機密作為服務主體登錄。 將環境變數AZCOPY_SPA_CLIENT_SECRET到用戶端機密，以便進行基於機密的服務主體 auth。

```azcopy
azcopy login --service-principal
```

使用證書和密碼作為服務主體登錄。 將環境變數AZCOPY_SPA_CERT_PASSWORD證書的密碼，以便獲得基於證書的服務主體授權。

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

請確保將 /path/to/my/cert 視為 PEM 或 PKCS12 檔的路徑。 AzCopy 不會進入系統憑證存放區以獲取證書。

--在進行基於證書的服務主體 auth 時，憑證路徑是必需的。

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|--aad-端點|要使用的 Azure 活動目錄終結點。 預設 （`https://login.microsoftonline.com`） 對於公共 Azure 雲是正確的。 在國家雲中進行身份驗證時設置此參數。 請參閱[Azure AD 身份驗證終結點](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。
託管服務標識不需要此標誌。|
|--應用程式 id 字串|使用者分配標識的應用程式 ID。 服務主體 auth 所需的。|
|--憑證路徑字串|SPN 身份驗證憑證的路徑。 基於證書的服務主體 auth 需要。|
|-h, --help|顯示登錄命令的説明內容。|
|--身份|使用虛擬機器標識（也稱為託管服務標識 （MSI） 登錄。|
|--身份用戶端-id 字串|使用者分配標識的用戶端 ID。|
|--標識-物件-id 字串|使用者分配標識的物件識別碼。|
|--標識資源-id 字串|使用者分配標識的資源識別碼。|
|--服務主體|使用證書或機密通過 SPN（服務主體名稱）登錄。 用戶端金鑰或證書密碼必須放置在相應的環境變數中。 鍵入`AzCopy env`以查看環境變數的名稱和說明。|
|--租戶 id 字串| 用於 OAuth 設備互動式登入的 Azure 活動目錄租戶 ID。|

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

|選項|描述|
|---|---|
|--蓋-mbps uint32|以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。|
|--輸出類型字串|命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。|

## <a name="see-also"></a>另請參閱

- [阿茲比貝](storage-ref-azcopy.md)
