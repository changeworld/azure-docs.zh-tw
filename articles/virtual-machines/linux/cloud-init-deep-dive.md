---
title: 瞭解雲端初始化
description: 深入瞭解如何使用雲端初始化來布建 Azure VM。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86042065"
---
# <a name="diving-deeper-into-cloud-init"></a>深入探索雲端初始化
若要深入瞭解 [雲端初始](https://cloudinit.readthedocs.io/en/latest/index.html) ，或在更深入的層級進行疑難排解，您必須瞭解它的運作方式。 本檔著重于重要的部分，並說明 Azure 細節。

當雲端 init 包含在一般化映射中，並從該映射建立 VM 時，它將會處理設定，並在初始開機期間透過5個階段執行。 這些階段很重要，因為它會說明雲端初始會套用設定的時間點。 


## <a name="understand-cloud-init-configuration"></a>瞭解 Cloud-Init 設定
將 VM 設定為在平臺上執行，表示雲端初始需要將多個設定套用至映射取用者，而您要與之互動的主要設定是 `User data` (customData) （支援多種格式），其記載于 [此處](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats)。 您也可以新增和執行腳本 (/var/lib/cloud/scripts) 以進行其他設定，以下將更詳細討論這一點。

某些設定已內建至雲端 init 隨附 Azure Marketplace 映射，例如：

1. **雲端資料來源** --雲端初始包含可與雲端平臺互動的程式碼，這些程式碼稱為「資料來源」。 從 [azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)中的雲端初始映射建立 VM 時，雲端 init 會載入 azure 資料來源，這會與 azure 中繼資料端點互動以取得 VM 專屬設定。
2. **運行** 時間設定 (/run/cloud-init) 
3. **映射** 設定 (/etc/cloud) ，例如 `/etc/cloud/cloud.cfg` `/etc/cloud/cloud.cfg.d/*.cfg` 。 這是在 Azure 中使用此功能的範例，在具有雲端初始化的 Linux OS 映射中，通常會有 Azure datasource 指示詞，這會告訴雲端初始化其應使用的資料來源，這可節省雲端初始時間：

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>雲端初始開機階段 (處理設定) 

使用雲端初始布建時，有5個開機階段，可處理設定並顯示在記錄中。

1. 產生器[階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator)：雲端初始 systemd 產生器會啟動，並判斷是否應將雲端初始化納入開機目標中，如果是，則會啟用雲端初始化。 

2. [雲端初始設定階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local)：這裡的 cloud init 將尋找本機 "Azure" 資料來源，這會讓雲端初始化與 Azure 進行介面，並套用網路設定，包括回溯。

3. [雲端初始初始階段 (網路) ](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network)：網路應該在線上，而且應該產生 NIC 和路由表資訊。 在這個階段中， `cloud_init_modules` 會執行/etc/cloud/cloud.cfg 中所列的模組。 Azure 中的 VM 將會掛接、暫時磁片會格式化、設定主機名稱，以及其他工作。

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
   
   在這個階段之後，雲端初始化會向 Azure 平臺發出 VM 已成功布建的信號。 某些模組可能已失敗，並非所有模組失敗都會導致布建失敗。

4. [雲端初始設定階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config)：在這個階段中， `cloud_config_modules` 會執行已定義並列在/etc/cloud/cloud.cfg 中的模組。


5. [雲端初始階段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final)：在此最後一個階段，將會執行/etc/cloud/cloud.cfg 中所列的模組 `cloud_final_modules` 。 在此課程模組中，需要在開機程式執行時順延強制，例如封裝安裝和執行腳本等。 

   -   在這個階段中，您可以藉由將腳本放在下列目錄中，來執行腳本 `/var/lib/cloud/scripts` ：
   - `per-boot` -此目錄內的腳本，會在每次重新開機時執行
   - `per-instance` -第一次啟動新的實例時，此目錄內的腳本會執行
   - `per-once` -此目錄內的腳本只執行一次

## <a name="next-steps"></a>接下來的步驟

[疑難排解 cloud init](cloud-init-troubleshooting.md)。
