---
title: 開始使用適用于 Azure NetApp Files 的 Azure 應用程式一致快照集工具 |Microsoft Docs
description: 提供安裝您可以搭配 Azure NetApp Files 使用之 Azure 應用程式一致快照集工具的指南。
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736357"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>開始使用 Azure 應用程式一致的快照集工具 (預覽) 

本文提供的指南說明如何安裝 Azure 應用程式一致的快照集工具，以搭配 Azure NetApp Files 使用。

## <a name="getting-the-snapshot-tools"></a>取得快照集工具

建議客戶從 Microsoft 取得最新版本的 [AzAcSnap 安裝程式](https://aka.ms/azacsnapdownload) 。

自我安裝檔案有相關聯的 [AzAcSnap 安裝程式](https://aka.ms/azacsnapdownloadsignature) 簽章檔案，該檔案會使用 Microsoft 的公開金鑰進行簽署，以允許針對下載的安裝程式進行 GPG 驗證。

完成這些下載之後，請遵循本指南中的步驟來安裝。

### <a name="verifying-the-download"></a>驗證下載

安裝程式（每個以上可下載）有相關聯的 PGP 簽章檔案， `.asc` 副檔名為副檔名。 您可以使用這個檔案來確保下載的安裝程式是經過驗證的 Microsoft 所提供的檔案。 用來簽署 Linux 套件的 Microsoft PGP 公開金鑰可在此處 (<https://packages.microsoft.com/keys/microsoft.asc>) ，並已用來簽署簽章檔案。

Microsoft PGP 公開金鑰可以匯入使用者的本機，如下所示：

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

下列命令會信任 Microsoft PGP 公開金鑰：

1. 列出存放區中的金鑰。
2. 編輯 Microsoft 金鑰。
3. 檢查指紋 `fpr`
4. 簽署金鑰以信任它。

```bash
gpg --list-keys
```

列出的索引鍵：
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

從互動式 `gpg` 會話簽署 Microsoft 公開金鑰的輸出：
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

您可以依照下列方式檢查安裝程式的 PGP 簽名檔：

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

如需使用 GPG 的詳細資訊，請參閱 [GNU 隱私權手冊](https://www.gnupg.org/gph/en/manual/book1.html)。

## <a name="supported-scenarios"></a>支援的案例

您可以在下列案例中使用快照集工具。

- 單一 SID
- 多個 SID
- 高鐵
- 相應放大
- MDC (僅支援單一租使用者) 
- 單一容器
- SUSE 作業系統
- RHEL 作業系統
- SKU 類型 I
- SKU 類型 II

查看 [適用于 HANA 大型實例的支援案例](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>來自 SAP 的快照集支援矩陣

下表提供 SAP 針對儲存體快照集備份支援哪些 SAP Hana 版本的指導方針。

| 資料庫版本       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|單一容器資料庫| √       | √      | -      | -      | -      | -      |
|MDC 單一租使用者        | -       | -      | √      | √      | √      | √      |
|MDC 多租使用者     | -       | -      | -      | -      | -      | √      |
> √ = <small>SAP 針對儲存體快照集支援</small>

## <a name="important-things-to-remember"></a>重要注意事項

- 在設定快照集工具之後，持續監視可用的儲存空間，如有必要，請定期刪除舊的快照集，以避免儲存體填滿。
- 一律使用最新的快照集工具。
- 在整個環境中使用相同版本的快照集工具。
- 在生產系統中使用之前，請先測試快照集工具，並熟悉命令所需的參數和輸出。
- 當您在本檔) 中設定 HANA 使用者進行備份 (詳細資料時，您需要為每個 HANA 實例設定使用者。 建立 SAP Hana 的使用者帳戶，以在 SYSTEMDB (（而不是在適用于 MDC 的 SID 資料庫) ）中存取 HANA 實例。 在單一容器環境中，您可以在租使用者資料庫下設定它。
- 客戶必須提供 SSH 公開金鑰，才能存取儲存體。 每個節點和執行命令的每個使用者都必須執行一次此動作。
- 每個磁片區的快照集數目限制為250。
- 如果手動編輯設定檔，請一律使用 Linux 文字編輯器，例如 "vi"，而非 Windows 編輯器（例如 [記事本]）。 使用 Windows 編輯器可能會損毀檔案格式。
  - 設定 `hdbuserstore` 讓 SAP Hana 使用者與 SAP Hana 進行通訊。
- DR：在設定 DR 之前，快照集工具必須在 DR 節點上進行測試。
- 定期監視磁碟空間，自動刪除記錄會使用 `--trim`   `azacsnap -c backup` SAP Hana 2 和更新版本的選項來管理。
- **未採用快照** 集的風險-快照集工具只會與設定檔中指定 SAP Hana 系統的節點互動。  如果這個節點變得無法使用，就不會有自動開始與另一個節點通訊的機制。  
  - 針對 **具有待命案例的 SAP Hana Scale-Out** ，通常是在主要節點上安裝和設定快照集工具。 但是，如果主要節點變成無法使用，待命節點將接管主要節點角色。 在此情況下，實團隊應該在兩個節點上設定快照集工具 (Master 和) ，以避免遺漏任何快照集。 在正常狀態下，主要節點將採用 crontab 起始的 HANA 快照集，但在主要節點容錯移轉之後，必須從另一個節點執行這些快照集，例如新的主要節點 (先前的待命) 。 為了達成此結果，待命節點需要安裝快照工具、啟用存放裝置通訊、hdbuserstore 設定、 `azacsnap.json` 設定，以及在容錯移轉前分段的 crontab 命令。
  - 針對 **SAP HANA HSR HA** 案例，建議您在 (主要和次要) 節點上安裝、設定和排程快照集工具。 然後，如果主要節點變得無法使用，次要節點將會接管在次要資料庫上建立快照集的動作。 在正常狀態下，主要節點將會取得以 crontab 起始的 HANA 快照集，而次要節點會嘗試建立快照集，但因為主要複本正常運作，所以失敗。  但在主要節點容錯移轉之後，就會從次要節點執行這些快照集。 若要達成此結果，次要節點需要安裝快照工具、啟用、設定的儲存體通訊、 `hdbuserstore` 設定 azacsnap.js，以及在容錯移轉前啟用的 crontab。

## <a name="guidance-provided-in-this-document"></a>本檔中提供的指導方針

下列指導方針是為了說明快照集工具的使用方式而提供。

### <a name="taking-snapshot-backups"></a>拍攝快照集備份

- [儲存體快照集的必要條件](azacsnap-installation.md#prerequisites-for-installation)
  - [啟用與存放裝置的通訊](azacsnap-installation.md#enable-communication-with-storage)
  - [啟用與 SAP Hana 的通訊](azacsnap-installation.md#enable-communication-with-sap-hana)
- [如何手動拍攝快照集](azacsnap-tips.md#take-snapshots-manually)
- [如何設定自動快照集備份](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [如何監視快照集](azacsnap-tips.md#monitor-the-snapshots)
- [如何刪除快照集？](azacsnap-tips.md#delete-a-snapshot)
- [如何還原快照集](azacsnap-tips.md#restore-a-snapshot)
- [如何還原快照集 `boot`](azacsnap-tips.md#restore-a-boot-snapshot)
- [關於快照集的重要事實](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> 快照集會針對單一 SID 和多重 SID 進行測試。

### <a name="performing-disaster-recovery"></a>執行嚴重損壞修復

- [DR 設定的必要條件](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [如何設定嚴重損壞修復](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [如何監視從主要到 DR 網站的資料複寫](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [如何執行容錯移轉至 DR 網站？](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>後續步驟

- [安裝 Azure 應用程式一致的快照集工具](azacsnap-installation.md)