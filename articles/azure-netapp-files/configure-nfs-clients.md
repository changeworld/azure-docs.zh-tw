---
title: 設定適用于 Azure NetApp Files 的 NFS 用戶端 |Microsoft Docs
description: 說明如何將 NFS 用戶端設定成與 Azure NetApp Files 搭配使用。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2020
ms.author: b-juche
ms.openlocfilehash: bdcffcea8d695b6a3d49272ffa135187d77aae1e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535342"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>設定適用于 Azure NetApp Files 的 NFS 用戶端

有各種 Linux 散發套件可與 Azure NetApp Files 搭配使用。 本文說明兩個較常用環境的設定： RHEL 8 和 Ubuntu 18.04。

無論您使用的是哪一種 Linux 類別，都需要下列設定：
* 設定 NTP 用戶端，以避免發生時間扭曲的問題。
* 設定 Linux 用戶端的 DNS 專案以進行名稱解析。  
    此設定包含「A」（轉寄）記錄和 PTR （反向）記錄。 
* 若要加入網域，請在目標 Active Directory 中建立電腦帳戶（在領域聯結命令期間建立）。 
    > [!NOTE] 
    > `$SERVICEACCOUNT`下列命令中使用的變數應該是具有許可權或委派的使用者帳戶，以在目標組織單位中建立電腦帳戶。
* 讓用戶端能夠掛接 NFS 磁片區和其他相關的監視工具。

## <a name="rhel-8-configuration"></a>RHEL 8 設定 

1. 安裝套件：   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. 設定 NTP 用戶端：  
    RHEL 8 預設會使用 `chrony` 。  遵循[使用 Chrony suite 設定 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)中的設定指導方針。

3. 加入 Active Directory 網域：  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu 設定 

1. 安裝套件：  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. 設定 NTP 用戶端。  
    Ubuntu 18.04 預設會使用 `chrony` 。  遵循 Ubuntu Bionic 中的[設定指導方針：使用 chrony 來設定 NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)。

3. 加入 Active Directory 網域：  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>後續步驟  

* [建立適用於 Azure NetApp Files 的 NFS 磁碟區](azure-netapp-files-create-volumes.md)
* [建立適用于 Azure NetApp Files 的雙重通訊協定磁片區](create-volumes-dual-protocol.md)

