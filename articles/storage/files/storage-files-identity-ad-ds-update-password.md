---
title: 更新 AD DS 儲存體帳戶密碼
description: 瞭解如何更新代表您的儲存體帳戶之 Active Directory Domain Services 帳戶的密碼。 這可防止在密碼過期時清除儲存體帳戶，以防止驗證失敗。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d16f11a85c6b370b0187975cce965bf3e1b5ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510249"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>在 AD DS 中更新儲存體帳戶身分識別的密碼

如果您已在會強制執行密碼到期時間的組織單位或網域中，註冊代表儲存體帳戶的 Active Directory Domain Services （AD DS）身分識別/帳戶，您必須在密碼最長使用期限之前變更密碼。 您的組織可能會執行自動清除腳本，在帳戶密碼過期後將其刪除。 因此，如果您在密碼到期前未變更它，您的帳戶可能會遭到刪除，這會導致您無法存取 Azure 檔案共用。

若要觸發密碼輪替，您可以 `Update-AzStorageAccountADObjectPassword` 從[AzFilesHybrid 模組](https://github.com/Azure-Samples/azure-files-samples/releases)執行命令。 此命令必須在內部部署 AD DS 聯結環境中執行，其使用具有儲存體帳戶之擁有者許可權的混合式使用者，並 AD DS 許可權來變更代表儲存體帳戶的身分識別密碼。 命令會執行類似于儲存體帳戶金鑰輪替的動作。 具體而言，它會取得儲存體帳戶的第二個 Kerberos 金鑰，並使用它來更新 AD DS 中已註冊之帳戶的密碼。 然後，它會重新產生儲存體帳戶的目標 Kerberos 金鑰，並在 AD DS 中更新已註冊帳戶的密碼。 您必須在已加入內部部署 AD DS 的環境中執行此命令。

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
