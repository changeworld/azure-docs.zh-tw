---
title: Azure 受控 HSM 的完整備份/還原和選擇性還原
description: 本文件說明完整備份/還原和選擇性還原
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e051a36b3c91fadc0c3b602cb4ba8e3dbcff1294
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367126"
---
# <a name="full-backup-and-restore"></a>完整備份和還原

> [!NOTE]
> 此功能僅適用於資源類型受控 HSM。

受控 HSM 支援建立所有 HSM 內容的完整備份，包括所有金鑰、版本、屬性、標籤和角色指派。 備份會使用與 HSM 的安全性網域相關聯的密碼編譯金鑰進行加密。

備份是一項資料平面作業。 起始備份作業的呼叫者必須具有執行 dataAction **Microsoft.KeyVault/managedHsm/backup/start/action** 的權限。

只有下列內建角色才具有執行完整備份的權限：
- 受控 HSM 管理員
- 受控 HSM 備份

您必須提供下列資訊，才能執行完整備份：
- HSM 名稱或 URL
- 儲存體帳戶名稱
- 儲存體帳戶 Blob 儲存體容器
- 具有權限 `crdw` 的儲存體容器 SAS 權杖

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>完整備份

備份是長時間執行的作業，但會立即傳回作業識別碼。 您可以使用此作業識別碼來檢查備份程序的狀態。 備份程序會使用下列命名模式 **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** 在指定的容器內建立資料夾，其中，HSM_NAME 是要備份的受控 HSM 名稱，而 YYYY、MM、DD、HH、MM、mm、SS 依序是收到備份命令時的日期/時間 (UTC) 中的年、月、日、小時、分鐘和秒。

在備份進行時，HSM 可能無法以完整輸送量運作，因為有些 HSM 分割區會忙於執行備份作業。

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>完整還原

完整還原可讓您使用先前的備份完整還原 HSM 的內容，包括所有金鑰、版本、屬性、標籤和角色指派。 目前儲存在 HSM 中的所有項目都將抹除，且會回復為建立來源備份時的相同狀態。

> [!IMPORTANT]
> 完整還原是具破壞性和干擾性的作業。 因此，必須在執行 `restore` 作業前的 30 分鐘內完成完整備份。

還原是一項資料平面作業。 啟動還原作業的呼叫者必須具有執行 dataAction **Microsoft.KeyVault/managedHsm/restore/start/action** 的權限。 備份建立所在的來源 HSM 和將執行還原的目的地 HSM **必須** 具有相同的安全性網域。 請參閱[關於受控 HSM 安全性網域](security-domain.md)以了解詳細資訊。

您必須提供下列資訊，才能執行完整還原：
- HSM 名稱或 URL
- 儲存體帳戶名稱
- 儲存體帳戶 Blob 容器
- 具有權限 `rl` 的儲存體容器 SAS 權杖
- 來源備份儲存所在的儲存體容器資料夾名稱

還原是長時間執行的作業，但會立即傳回作業識別碼。 您可以使用此作業識別碼來檢查還原程序的狀態。 在還原程序進行時，HSM 會進入還原模式，並停用所有資料平面命令 (檢查還原狀態除外)。

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="backup-hsm"></a>備份 HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>後續步驟
- 請參閱[使用 Azure CLI 管理受控 HSM](key-management.md)。
- 深入了解[受控 HSM 安全性網域](security-domain.md)