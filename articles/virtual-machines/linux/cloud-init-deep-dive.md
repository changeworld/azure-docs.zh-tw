---
title: 瞭解雲端-init
description: 深入瞭解如何使用雲端 init 來布建 Azure VM。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042065"
---
# <a name="diving-deeper-into-cloud-init"></a>深入探索雲端-init
若要深入瞭解[雲端 init](https://cloudinit.readthedocs.io/en/latest/index.html) ，或在更深入的層級進行疑難排解，您必須瞭解其運作方式。 本檔將重點放在重要的部分，並說明 Azure 的細節。

當雲端 init 包含在一般化映射中，而且從該映射建立 VM 時，它會處理設定，並在初始開機期間執行5個階段。 這些階段很重要，因為它會告訴您，cloud init 將套用設定的位置。 


## <a name="understand-cloud-init-configuration"></a>瞭解雲端初始化設定
將 VM 設定為在平臺上執行，表示雲端 init 必須套用多個設定，做為映射取用者，您要與之互動的主要設定是 `User data` （customData），其支援多種格式，[此處](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats)會記載這些設定。 您也可以新增和執行腳本（/var/lib/cloud/scripts）以進行其他設定，以下將更詳細地討論此功能。

有些設定已內建到雲端 init 隨附 Azure Marketplace 映射中，例如：

1. **雲端資料來源**-雲端-init 包含可以與雲端平臺互動的程式碼，這些稱為「資料來源」。 從[Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)中的雲端初始化映射建立 VM 時，cloud init 會載入 azure datasource，這會與 azure 中繼資料端點互動，以取得 VM 特定設定。
2. **運行**時間設定（/run/cloud-init）
3. **影像**設定（/etc/cloud），例如 `/etc/cloud/cloud.cfg` ， `/etc/cloud/cloud.cfg.d/*.cfg` 。 在 Azure 中使用這種方式的範例，Linux OS 映射與雲端 init 通常會有 Azure datasource 指示詞，這會告訴雲端初始化它應該使用的資料來源，這會節省雲端初始化時間：

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>雲端初始開機階段（處理設定）

以雲端 init 進行布建時，有5個階段的開機，會處理設定，並顯示在記錄中。

1. 產生器[階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator)：雲端初始化 systemd 產生器會啟動，並判斷雲端 init 應該包含在開機目標中，如果是，則會啟用雲端 init。 

2. [雲端初始化本機階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local)：這裡的 cloud init 會尋找本機 "Azure" 資料來源，這將可讓雲端初始化與 Azure 互動，並套用網路設定，包括回溯。

3. [雲端初始化初始階段（網路）](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network)：網路功能應該在線上，而且應該產生 NIC 和路由表資訊。 在此階段中， `cloud_init_modules` 將會執行/etc/cloud/cloud.cfg 中所列的模組。 Azure 中的 VM 將會掛接、暫時磁片已格式化、主機名稱已設定，以及其他工作。

   以下是其中一些 `cloud_init_modules` ：
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   在此階段之後，雲端初始化會向 Azure 平臺發出通知，表示已成功布建 VM。 某些模組可能已失敗，而並非所有模組失敗都會導致布建失敗。

4. [雲端初始設定階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config)：在此階段中， `cloud_config_modules` 將會執行在/etc/cloud/cloud.cfg 中定義和列出的模組。


5. [雲端初始化的最後階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final)：在此最終階段中，將會執行中所列的模組 `cloud_final_modules` （位於/etc/cloud/cloud.cfg 中）。 這裡需要在開機程式執行的晚期執行的模組，例如套件安裝和執行腳本等。 

   -   在此階段中，您可以將腳本放在下列目錄中來執行 `/var/lib/cloud/scripts` ：
   - `per-boot`-此目錄內的腳本，會在每次重新開機時執行
   - `per-instance`-當第一次啟動新實例時，會執行此目錄中的腳本
   - `per-once`-此目錄內的腳本只會執行一次

## <a name="next-steps"></a>後續步驟

針對[雲端 Init 進行疑難排解](cloud-init-troubleshooting.md)。
