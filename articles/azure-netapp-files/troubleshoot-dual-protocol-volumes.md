---
title: 針對 Azure NetApp Files 的雙重通訊協定磁片區進行疑難排解 |Microsoft Docs
description: 描述可協助您針對 Azure NetApp Files 的雙重通訊協定問題進行疑難排解的錯誤訊息和解決方式。
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
ms.topic: troubleshooting
ms.date: 10/01/2020
ms.author: b-juche
ms.openlocfilehash: cc046a27fec1b9e361ff840c7ae0f077e2987b67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654149"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>針對雙重通訊協定磁碟區進行疑難排解

本文說明建立或管理雙重通訊協定磁片區時可能發生之錯誤狀況的解決方式。

## <a name="error-conditions-and-resolutions"></a>錯誤狀況和解決方式

|     錯誤條件    |     解決方案    |
|-|-|
| 雙通訊協定磁片區建立失敗，並出現錯誤 `This Active Directory has no Server root CA Certificate` 。    |     如果您在建立雙重通訊協定磁片區時發生此錯誤，請確定您的 NetApp 帳戶中已上傳根 CA 憑證。    |
| 雙通訊協定磁片區建立失敗，並出現錯誤 `Failed to validate LDAP configuration, try again after correcting LDAP configuration` 。    |  請考慮下列解決方案：   <ul><li>當您將 Active Directory (AD) 加入至 NetApp 帳戶時，請確定已新增必要的根憑證。 請參閱 [上傳 Active Directory 憑證授權單位單位公用根憑證](create-volumes-dual-protocol.md#upload-active-directory-certificate-authority-public-root-certificate)。   </li><li>DNS 伺服器上可能遺失 AD 主機電腦的指標 (PTR) 記錄。 您必須在 DNS 伺服器上建立反向對應區域，然後在該反向對應區域中新增 AD 主機電腦的 PTR 記錄。 <br> 例如，假設 AD 機器的 IP 位址是 `1.1.1.1` 、ad 機器的主機名稱 (如使用 `hostname` 命令) 找到的 `AD1` ，且功能變數名稱為 `myDomain.com` 。  加入至反向對應區域的 PTR 記錄應該是 `1.1.1.1`  ->  `AD1.myDomain.com` 。 </li></ul>  |
| 雙通訊協定磁片區建立失敗，並出現錯誤 `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` 。 |  此錯誤表示當 Active Directory 加入 NetApp 帳戶時，AD 密碼不正確。 請使用正確的密碼更新 AD 連線，然後再試一次。 |
| 雙通訊協定磁片區建立失敗，並出現錯誤 `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` 。 |   此錯誤表示無法連線到 DNS。 原因可能是 DNS IP 不正確，或有網路問題。 檢查 AD 連線中輸入的 DNS IP，並確定 IP 正確無誤。 <br> 此外，請確定 AD 和磁片區位於相同的區域和相同的 VNet 中。 如果它們是在不同的 Vnet 中，請確定兩個 Vnet 之間已建立 VNet 對等互連。|
| 掛接雙重通訊協定磁片區時，拒絕許可權錯誤。 | 雙協定磁片區支援 NFS 和 SMB 通訊協定。  當您嘗試存取 UNIX 系統上裝載的磁片區時，系統會嘗試將您使用的 UNIX 使用者對應到 Windows 使用者。 如果找不到對應，就會發生「許可權拒絕」錯誤。 <br> 當您使用「根」使用者進行存取時，這種情況也適用。 <br> 若要避免「許可權被拒」的問題，請 `pcuser` 在存取掛接點之前，確定 Windows Active Directory 包含。 如果您在 `pcuser` 遇到「許可權拒絕」問題之後新增，請等候24小時讓快取專案清除，然後再嘗試存取。 |

## <a name="next-steps"></a>後續步驟  

* [建立雙重通訊協定磁碟區](create-volumes-dual-protocol.md)
* [設定 Azure NetApp Files 的 NFS 用戶端](configure-nfs-clients.md)
