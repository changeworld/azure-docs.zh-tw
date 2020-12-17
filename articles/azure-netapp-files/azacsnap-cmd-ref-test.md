---
title: 針對 Azure NetApp Files 測試 Azure 應用程式一致的快照集工具 |Microsoft Docs
description: 說明如何執行 Azure 應用程式一致的快照集工具（可與 Azure NetApp Files 搭配使用）的測試命令。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632607"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a> (preview) 測試 Azure 應用程式一致的快照集工具

本文說明如何執行 Azure 應用程式一致的快照集工具（可搭配 Azure NetApp Files 使用）的測試命令。

## <a name="introduction"></a>簡介

使用命令完成設定的測試 `azacsnap -c test` 。

## <a name="command-options"></a>命令選項

此 `-c test` 命令有下列選項：

- `--test hana`  測試與 SAP Hana 實例的連接。

- `--test storage` 藉由在所有設定的磁片區上建立暫存儲存體快照集 `data` ，然後將它們移除，來測試與基礎儲存介面的通訊。 

- `--test all` 會依序執行 `hana` 和 `storage` 測試。

- `[--configfile <config filename>]` 是選擇性參數，允許自訂設定檔案名稱。

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>檢查與 SAP Hana 的連線能力 `azacsnap -c test --test hana`

此命令會檢查設定檔中所有 HANA 實例的 HANA 連線能力。 它會使用 HDBuserstore 來連接到 SYSTEMDB 並提取 SID 資訊。

若為 SSL，此命令可採用下列選擇性引數：

- `--ssl=` 強制加密與資料庫的連接，並定義用來與 SAP Hana 進行通訊的加密方法，不論是 `openssl` 或 `commoncrypto` 。 如果已定義，則此命令預期會在相同的目錄中找到兩個檔案，這些檔案必須以對應的 SID 命名。 請參閱 [使用 SSL 與 SAP Hana 進行通訊](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)。

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>命令的輸出 `azacsnap -c test --test hana`

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>檢查儲存體的連線能力 `azacsnap -c test --test storage`

此 `azacsnap` 命令會取得所有設定的資料磁片區的快照集，以確認它對於每個 SAP Hana 實例的磁片區具有正確的存取權。 系統會為每個資料磁片區建立暫存快照集，然後將其移除，以確認每個檔案系統的快照集存取。

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>命令的輸出 `azacsnap -c test --test storage`

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> 針對 Azure 大型實例， `azacsnap -c test --test storage` 命令會據此儲存體世代和過程 SKU。  根據此資訊，它會提供有關設定「開機」快照的指引 (查看以輸出) 開頭的行 `Action:` 。

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>下一步

- [快照集備份與 Azure 應用程式一致的快照集工具](azacsnap-cmd-ref-backup.md)
