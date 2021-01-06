---
title: 針對 Azure NetApp Files 的 Nfsv4.1 4.1 Kerberos 大量問題進行疑難排解 |Microsoft Docs
description: 描述可協助您針對 Azure NetApp Files 的 Nfsv4.1 4.1 Kerberos 大量問題進行疑難排解的錯誤訊息和解決方式。
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
ms.date: 01/05/2020
ms.author: b-juche
ms.openlocfilehash: 930e5f976e98aed4e34ff1b8fa967dd14979ea26
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937587"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>針對 Nfsv4.1 4.1 Kerberos 大量問題進行疑難排解 

本文說明您在建立或管理 Nfsv4.1 4.1 Kerberos 磁片區時可能會遇到的錯誤狀況的解決方式。 

## <a name="error-conditions-and-resolutions"></a>錯誤狀況和解決方式

|     錯誤條件    |     解決方式    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files 不支援 NFSv3 磁片區的 Kerberos。 只有 Nfsv4.1 4.1 通訊協定支援 Kerberos。  |
|`This NetApp account has no configured Active Directory   connections`  |  為具有欄位 **KDC IP** 和 **AD 伺服器名稱** 的 NetApp 帳戶設定 Active Directory。 如需相關指示，請參閱 [設定 Azure 入口網站](configure-kerberos-encryption.md#configure-the-azure-portal) 。 |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files 不支援將純 Nfsv4.1 4.1 磁片區轉換成 Kerberos Nfsv4.1 4.1 磁片區，反之亦然。 |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  範例： `smb-test-64d9.xyz.com:/nfs41-vol101` | <ol><li> 請確認 A/PTR 記錄已正確設定，而且存在於伺服器名稱的 Active Directory 中 `smb-test-64d9.xyz.com` 。 <br> 在 NFS 用戶端中， `nslookup` 如果 `smb-test-64d9.xyz.com` 解析為 IP 位址 IP1 (也就是 `10.1.1.68`) ，則 `nslookup` IP1 必須只解析為一筆記錄 (也就是 `smb-test-64d9.xyz.com`) 。 `nslookup` IP1 *不得* 解析為多個名稱。 </li>  <li>使用 PowerShell 或 UI，為 AD 類型的 NFS 電腦帳戶設定 AES-256 `NFS-<Smb NETBIOS NAME>-<few random characters>` 。 <br> 範例命令： <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>確定 NFS 用戶端、AD 和 Azure NetApp Files 儲存體軟體的時間已同步處理，而且在五分鐘的誤差範圍內。 </li>  <li>使用命令取得 NFS 用戶端上的 Kerberos 票證 `kinit <administrator>` 。</li> <li>將 NFS 用戶端主機名稱減少為少於15個字元，然後再次執行領域聯結。 </li><li>重新開機 NFS 用戶端和服務，如下所 `rpcgssd` 示。 此命令可能會依作業系統而有所不同。<br> RHEL 7： <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8： <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu： <br>  (重新開機 `rpc-gssd` 服務。 )  <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | 問題可能與 NFS 用戶端問題有關。 重新開機 NFS 用戶端。    |
|`Hostname lookup failed`   | 您必須在 DNS 伺服器上建立反向對應區域，然後在該反向對應區域中新增 AD 主機電腦的 PTR 記錄。 <br> 例如，假設 AD 機器的 IP 位址是 `10.1.1.4` ，ad 機器的主機名稱 (如使用 hostname 命令) 找到 `AD1` ，而功能變數名稱為 `myDomain.com` 。 加入至反向對應區域的 PTR 記錄應該是 `10.1.1.4 -> AD1.myDomain.com` 。 |
|`Volume creation fails due to unreachable DNS server`  | 有兩種可能的解決方案可用： <br> <ul><li> 此錯誤表示無法連線到 DNS。 原因可能是 DNS IP 不正確或網路問題。 檢查 AD 連線中輸入的 DNS IP，並確定 IP 正確無誤。 </li> <li> 請確定 AD 和磁片區位於相同的區域和相同的 VNet 中。 如果它們是在不同的 Vnet 中，請確定兩個 Vnet 之間已建立 VNet 對等互連。 </li></ul> |
|Nfsv4.1 4.1 Kerberos 磁片區建立失敗，並出現類似下列範例的錯誤： <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP 錯誤，而且已建立 Kerberos 磁片區。 以正確的位址更新 KDC IP。 <br> 更新 KDC IP 之後，錯誤將不會消失。 您必須重新建立磁片區。 |

## <a name="next-steps"></a>後續步驟  

* [為 Azure NetApp Files 設定 Nfsv4.1 4.1 Kerberos 加密](configure-kerberos-encryption.md)
