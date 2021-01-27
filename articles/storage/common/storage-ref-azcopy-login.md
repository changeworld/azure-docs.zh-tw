---
title: azcopy 登入 |Microsoft Docs
description: 本文提供 azcopy login 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878353"
---
# <a name="azcopy-login"></a>azcopy 登入

登入 Azure Active Directory 以存取 Azure 儲存體資源。

## <a name="synopsis"></a>概要

登入 Azure Active Directory 以存取 Azure 儲存體資源。

若要獲得 Azure 儲存體帳戶的授權，您必須在儲存體帳戶、父資源群組或父訂用帳戶的內容中，將 **儲存體 Blob 資料參與者** 角色指派給您的使用者帳戶。

此命令會使用 OS 內建機制，為目前的使用者快取加密的登入資訊。

> [!IMPORTANT]
> 如果您使用命令列設定環境變數，該變數將可在您的命令列歷程記錄中讀取。 請考慮清除包含命令列歷程記錄之認證的變數。 若要讓變數不會出現在歷程記錄中，您可以使用腳本提示使用者輸入其認證，並設定環境變數。

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](./storage-use-azcopy-v10.md#transfer-data)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

以互動方式登入預設的 AAD 租使用者識別碼設定為 common：

```azcopy
azcopy login
```

使用指定的租使用者識別碼以互動方式登入：

```azcopy
azcopy login --tenant-id "[TenantID]"
```

使用虛擬機器的系統指派身分識別登入 (VM) ：

```azcopy
azcopy login --identity
```

使用 VM 的使用者指派身分識別和服務身分識別的用戶端識別碼來登入：
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
使用 VM 的使用者指派身分識別和服務身分識別的物件識別碼來登入：

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

使用 VM 的使用者指派身分識別和服務身分識別的資源識別碼來登入：
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

使用用戶端密碼以服務主體的形式登入：將環境變數 AZCOPY_SPA_CLIENT_SECRET 設定為以密碼為基礎的服務主體驗證的用戶端密碼。

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

使用憑證和密碼登入為服務主體：

針對以憑證為基礎的服務主體驗證，將環境變數 AZCOPY_SPA_CERT_PASSWORD 設定為憑證的密碼：

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

視為 `/path/to/my/cert` PEM 或 PKCS12 檔案的路徑。 AzCopy 不會到達系統憑證存放區以取得您的憑證。

`--certificate-path` 進行以憑證為基礎的服務主體驗證時，是必要的。

## <a name="options"></a>選項。

**--aad-** 要使用 Azure Active Directory 端點的端點字串。 https://login.microsoftonline.com)全域 Azure 雲端的預設 (是正確的。 在國家雲端中進行驗證時，請設定此參數。 受控服務識別不需要。

--使用者指派之身分識別的 **應用程式識別碼** 字串應用程式識別碼。 服務主體驗證的必要。

**--憑證-** 憑證的路徑字串路徑以進行 SPN 驗證。 以憑證為基礎的服務主體驗證所需。

**--**   命令的說明說明 `azcopy login` 。

**--identity**   使用虛擬機器的身分識別登入，也稱為「受控服務識別」 (MSI) 。

**--身分識別-** 使用者指派之身分識別的用戶端識別碼字串用戶端識別碼。

**--身分識別-物件** 識別碼字串物件識別碼：使用者指派的身分識別。

**--identity-資源-識別碼** 字串資源識別碼（使用者指派的身分識別）。

**--服務-主體**   使用憑證或密碼，透過服務主體名稱 (SPN) 登入。 用戶端密碼或憑證密碼必須放在適當的環境變數中。 輸入 AzCopy env 以查看環境變數的名稱和描述。

**--租** 使用者識別碼字串：用於 OAuth 裝置互動式登入的 Azure Active Directory 租使用者識別碼。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps float|以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。|
|--output 類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--受信任-microsoft-尾碼字串   |指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
