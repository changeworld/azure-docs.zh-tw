---
title: 排除 Azure 堡壘故障 |微軟文件
description: 在本文中,瞭解如何對 Azure 堡壘進行故障排除。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619176"
---
# <a name="troubleshoot-azure-bastion"></a>對 Azure Bastion 進行疑難排解

本文介紹如何排除 Azure 堡壘的故障。

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>無法建立 Azure Bastion Subnet 建立 NSG

**問:** 當我嘗試在 Azure 堡壘子網上創建 NSG 時,我得到以下錯誤:"*網路安全組<NSG name>沒有 Azure 堡壘子網 Azure Bastion Subnet 的必要規則"。*

**答:** 如果建立 NSG 並將其應用於*AzureBastionSubnet,* 請確保在 NSG 中添加了以下規則。 如果不添加這些規則,NSG 創建/更新將失敗。

1. 控制平面連線 – 從閘道管理器 443 上進入
2. 診斷日誌記錄和其他 — 443 到 AzureCloud 上的出站(此服務標記中的區域標記尚不支援。
3. 目標 VM = 3389 與 22 到虛擬網路的出站

NSG 規則的範例可在[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)中參考。
有關詳細資訊,請參閱[Azure 堡壘的 NSG 指南](bastion-nsg.md)。

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>無法使用我的 SSH 金鑰與 Azure 堡壘

**問:** 當我嘗試瀏覽我的SSH金鑰檔時,我得到以下錯誤 *:'SSH私鑰必須從-----BEGIN RSA私密金鑰開始-----以-----END RSA私鑰-----"結尾*。

**答:** 此時,Azure 堡壘僅支援 RSA SSH 密鑰。 請確保瀏覽為 SSH 的 RSA 私密金鑰檔,在目標 VM 上預配公開金鑰。 

例如,可以使用以下指令建立新的 RSA SSH 金鑰:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

輸出：

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>無法登入您的 Windows 網域加入的虛擬機器

**問:** 我無法連接到加入域的 Windows 虛擬機器。

**答:** Azure Bastion 支援加入網域的 VM 登錄,僅支援基於使用者名密碼的網域登錄。 在 Azure 門戶中指定網域認證時,請使用username@domainUPN () 格式而不是*網域使用者名*格式登錄。 對於域加入或混合聯接(域加入和 Azure AD 聯接)虛擬機器,支援此功能。 Azure AD 加入虛擬機器不支援它。

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>檔案傳輸問題

**問:** Azure 堡壘是否支援檔案傳輸?

**答:** 目前不支援檔案傳輸。 我們正在努力增加支援。

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure 門戶中的黑屏

**問:** 當我嘗試使用 Azure 堡壘進行連接時,我在 Azure 門戶中得到一個黑屏。

**答:** 當 Web 瀏覽器和 Azure 堡壘之間存在網路連接問題時(用戶端 Internet 防火牆可能阻止 WebSocket 流量或類似流量)或 Azure 堡壘和目標 VM 之間,就會發生這種情況。 大多數案例包括應用於 AzureBastionSubnet 的 NSG,或在目標 VM 子網上應用的 NSG,該子網阻止虛擬網路中的 RDP/SSH 流量。 允許 WebSocket 在用戶端 Internet 防火牆上進行流量,並檢查目標 VM 子網上的 NSG。

## <a name="next-steps"></a>後續步驟

有關詳細資訊,請參閱[堡壘常見問題解答](bastion-faq.md)。