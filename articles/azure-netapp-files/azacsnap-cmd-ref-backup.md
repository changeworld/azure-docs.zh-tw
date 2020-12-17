---
title: 使用適用于 Azure NetApp Files Azure 應用程式一致的快照集工具進行備份 |Microsoft Docs
description: 提供執行 Azure 應用程式一致快照集工具（可搭配 Azure NetApp Files 使用）備份命令的指南。
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
ms.openlocfilehash: 1051859d514c77bad1aa5f14becc2218a923df44
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632648"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 應用程式一致的快照集工具進行備份 (預覽) 

本文提供的指南說明如何執行 Azure 應用程式一致的快照集工具（可搭配 Azure NetApp Files 使用）的 backup 命令。

## <a name="introduction"></a>簡介

以儲存體快照集為基礎的備份會使用 `azacsnap -c backup` 命令執行。  此命令會在資料磁片區上執行資料庫一致儲存體快照集的協調流程，並 (儲存體快照集，而不會在其他磁片區) 任何資料庫一致性設定。  

針對資料磁片 `azacsnap` 區會為儲存體快照集準備資料庫，然後它會針對所有已設定的磁片區取得儲存體快照集，最後會建議資料庫完成快照集。  它也會記錄管理快照集備份活動的任何資料庫專案， (例如 SAP Hana 備份目錄) 。

## <a name="command-options"></a>命令選項

此 `-c backup` 命令會採用下列引數：

- `--volume=` 要快照集的磁片區類型，此參數可能包含 `data` 或 `other`
  - `data` 將設定檔的 dataVolume stanza 內的磁片區快照集。
  - `other` 將設定檔的 otherVolume stanza 內的磁片區快照集。
  
  > [!NOTE]
  > 藉由以開機磁碟區作為 otherVolume 來建立個別的設定檔案，就可以 `boot` 採用完全不同的排程來建立快照集 (例如，每日) 。

- `--prefix=` 快照集名稱的客戶快照集前置詞。 這個參數有兩個用途。 首先，要提供快照集群組的唯一名稱。 第二個 `--retention` 是決定針對指定的保留的儲存體快照集數目 `--prefix` 。

    > [!IMPORTANT]
    > 只有英數位元 ( 「a-z、a-z、0-9」 ) 、底線 ( "_" ) 和虛線 ( "-" ) 字元。

- `--retention` 所定義要保留的快照集數目 `--prefix` 。 建立新的快照集之後，會移除任何其他快照集 `--prefix` 。

- `--trim` 適用于 SAP Hana v2 和更新版本，此選項會維護備份類別目錄和磁碟目錄和記錄備份。 備份類別目錄中保留的專案數取決於 `--retention` 上面的選項，並會從備份類別目錄中刪除已定義之首碼 () 的較舊專案 `--prefix` ，以及相關的實體記錄備份。 它也會刪除比最舊的非記錄備份專案還舊的任何記錄備份專案。 這種作業有助於防止記錄備份使用所有可用的磁碟空間。

  > [!NOTE]
  > 下列範例命令會保留9個儲存體快照集，並確保持續修剪備份類別目錄，以符合所保留的9個儲存體快照集。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` 選擇性參數，定義用來與 SAP Hana （或是）進行通訊的加密 `openssl` 方法 `commoncrypto` 。 如果已定義，則 `azacsnap -c backup` 命令預期會在相同的目錄中找到兩個檔案，這些檔案必須以對應的 SID 命名。 請參閱 [使用 SSL 與 SAP Hana 進行通訊](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)。 下列範例會使用 `hana` 具有前置詞的類型快照集 `hana_TEST` ，並 `9` 使用 SSL () ，讓它們與 SAP Hana 進行通訊 `openssl` 。

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` 是選擇性參數，允許自訂設定檔案名稱。

## <a name="snapshot-backups-are-fast"></a>快照集備份很快

快照集備份的持續時間與磁片區大小無關，且 10 TB 的磁片區會在與 10 GB 磁片區相同的大約時間內進行貼齊。  

影響整體執行時間的主要因素是要進行快照集的磁片區數目，以及參數中的任何變更 `--retention` (其中減少在) 移除多餘的快照時，會增加執行時間。

在上述的範例設定中 (針對 **Azure 大型實例**) ，兩個磁片區的快照集會花費少於5秒的時間來完成。 針對 **Azure NetApp Files**，這兩個磁片區的快照集會需要大約60秒的時間。

> [!NOTE]
> 如果在 `--retention` 上一次執行時大幅降低 `azacsnap` (例如，從 `--retention 50` 到 `--retention 5`) ，則所需的時間會隨著 `azacsnap` 需要移除額外的快照而增加。

## <a name="example-with-data-parameter"></a>使用參數的範例 `data`

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

此命令不會輸出到主控台，但會寫入記錄檔、結果檔和 `/var/log/messages` 。

*記錄* 檔是由命令名稱 +-c 選項 + 設定檔案名所組成。 依預設，會使用預設設定檔案名來執行的記錄檔檔案名 `-c backup` `azacsnap-backup-azacsnap.log` 。

*結果* 檔案的基底名稱與記錄檔具有相同的基底名稱， `.result` 例如 `azacsnap-backup-azacsnap.result` 包含下列輸出的尾碼：

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

檔案 `/var/log/messages` 包含與檔案相同的輸出 `.result` 。 請參閱下列範例 (以根) 執行：

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>使用參數的範例 `other`

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

此命令不會輸出到主控台，而只會寫入記錄檔。  它不 _會寫入結果_ 檔或 `/var/log/messages` 。

*記錄* 檔是由命令名稱 +-c 選項 + 設定檔案名所組成。 依預設，會使用預設設定檔案名來執行的記錄檔檔案名 `-c backup` `azacsnap-backup-azacsnap.log` 。

## <a name="example-with-other-parameter-to-backup-host-os"></a>`other` (備份主機 OS) 的參數範例

> [!NOTE]
> 使用另一個設定檔 (`--configfile bootVol.json`) ，其中只包含開機磁碟區。

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

此命令不會輸出到主控台，而只會寫入記錄檔。  它不 _會寫入結果_ 檔或 `/var/log/messages` 。

此範例中的 *記錄* 檔名稱是 `azacsnap-backup-bootVol.log` 。

> [!NOTE]
> 記錄檔名稱是由 [ (命令名稱- (`-c` 選項) - (設定檔案名) ] 所組成。  例如，如果使用記錄檔 `-c backup` 名稱的選項 `h80.json` ，則會通話記錄檔 `azacsnap-backup-h80.log` 。  或者，如果使用 `-c test` 具有相同設定檔的選項，則會通話記錄檔 `azacsnap-test-h80.log` 。

- HANA 大型實例類型：有兩個有效的值， `TYPEI` 或 `TYPEII` 相依于 HANA 大型實例單位。
- 請參閱適用 [于 HANA 大型實例的可用 sku](/azure/virtual-machines/workloads/sap/hana-available-skus) 來確認可用的 sku。

## <a name="next-steps"></a>下一步

- [取得快照詳細資料](azacsnap-cmd-ref-details.md)
- [刪除快照集](azacsnap-cmd-ref-delete.md)
