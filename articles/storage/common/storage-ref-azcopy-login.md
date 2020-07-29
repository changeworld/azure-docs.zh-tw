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
ms.openlocfilehash: 98f8554d6313147c03d4a0bec74e36043cdce342
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285266"
---
# <a name="azcopy-login"></a>azcopy 登入

登入以存取 Azure 儲存體資源 Azure Active Directory。

## <a name="synopsis"></a>概要

登入 Azure Active Directory 以存取 Azure 儲存體資源。

若要獲得 Azure 儲存體帳戶的授權，您必須在儲存體帳戶、父資源群組或父訂用帳戶的內容中，將**儲存體 Blob 資料參與者**角色指派給您的使用者帳戶。

此命令會使用作業系統內建機制，為目前的使用者快取加密的登入資訊。

> [!IMPORTANT]
> 如果您使用命令列設定環境變數，該變數將可在您的命令列歷程記錄中讀取。 請考慮從您的命令列歷程記錄中清除包含認證的變數。 若要讓變數不會出現在歷程記錄中，您可以使用腳本來提示使用者輸入其認證，並設定環境變數。

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

以互動方式使用預設 AAD 租使用者識別碼設定為 [一般] 來登入：

```azcopy
azcopy login
```

使用指定的租使用者識別碼，以互動方式登入：

```azcopy
azcopy login --tenant-id "[TenantID]"
```

使用虛擬機器（VM）的系統指派身分識別登入：

```azcopy
azcopy login --identity
```

使用 VM 的使用者指派身分識別和服務識別的用戶端識別碼來登入：
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
使用 VM 的使用者指派身分識別和服務身分識別的物件識別碼來登入：

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

使用 VM 的使用者指派身分識別和服務識別的資源識別碼來登入：
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

使用用戶端密碼以服務主體身分登入：將環境變數 AZCOPY_SPA_CLIENT_SECRET 設定為以密碼為基礎的服務主體驗證的用戶端密碼。

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

使用憑證和其密碼登入為服務主體：

針對以憑證為基礎的服務主體驗證，將環境變數 AZCOPY_SPA_CERT_PASSWORD 設定為憑證的密碼：

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

視為 `/path/to/my/cert` PEM 或 PKCS12 檔的路徑。 AzCopy 不會到達系統憑證存放區來取得您的憑證。

`--certificate-path`在執行以憑證為基礎的服務主體驗證時，是強制的。

## <a name="options"></a>選項。

**--aad-** 要使用 Azure Active Directory 端點的端點字串。 預設值（適用 https://login.microsoftonline.com) 于全域 Azure 雲端。 在國家雲端中進行驗證時，請設定此參數。 受控服務識別不需要。

--使用者指派之身分識別的**應用程式識別碼**字串應用程式識別碼。 服務主體驗證所需。

**--憑證-** SPN 驗證憑證的路徑字串路徑。 以憑證為基礎的服務主體驗證所需。

**--** 命令的說明協助 `azcopy login` 。

**--identity**  使用虛擬機器的身分識別登入，也稱為受控服務識別（MSI）。

**--identity-用戶端識別碼**字串用戶端識別碼，其為使用者指派的身分識別。

**--identity-物件識別碼**字串物件識別碼，使用者指派的身分識別。

**--身分識別-** 使用者指派身分識別的資源識別碼字串資源識別碼。

**--服務-主體**  使用憑證或秘密，透過服務主體名稱（SPN）登入。 用戶端密碼或憑證密碼必須放在適當的環境變數中。 輸入 AzCopy env 以查看環境變數的名稱和描述。

**--租使用者識別碼**字串要用於 OAuth 裝置互動式登入的 Azure Active Directory 租使用者識別碼。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|說明|
|---|---|
|--cap-mbps 浮點數|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--trusted-microsoft-尾碼字串   |指定可在其中傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
