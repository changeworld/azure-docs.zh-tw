---
title: 針對 Azure 防禦進行疑難排解 |Microsoft Docs
description: 在本文中，您將瞭解如何針對 Azure 防禦進行疑難排解。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619176"
---
# <a name="troubleshoot-azure-bastion"></a>對 Azure Bastion 進行疑難排解

本文說明如何針對 Azure 防禦進行疑難排解。

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>無法在 AzureBastionSubnet 上建立 NSG

**問：** 當我嘗試在 Azure 防禦子網上建立 NSG 時，收到下列錯誤：「*網路安全性群組<NSG name>沒有 Azure 防禦子網的必要規則 AzureBastionSubnet*」。

**答：** 如果您建立 NSG 並將其套用至*AzureBastionSubnet*，請確定您已在 NSG 中新增下列規則。 如果您未新增這些規則，則 NSG 建立/更新將會失敗。

1. 控制平面連線能力–443上從 GatewayManager 的輸入
2. 診斷記錄和其他專案–443到 AzureCloud 的輸出（尚不支援此服務標籤內的地區標記）。
3. 目標 VM –3389和22的輸出至 VirtualNetwork

NSG 規則的範例可在[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)中參考。
如需詳細資訊，請參閱[Azure 防禦的 NSG 指引](bastion-nsg.md)。

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>無法搭配 Azure 防禦使用我的 SSH 金鑰

**問：** 當我嘗試流覽我的 SSH 金鑰檔案時，收到下列錯誤： *「SSH 私密金鑰必須以-----開始 RSA 私密金鑰-----開頭，並以-----結束 RSA 私密金鑰-----' 結束*。

**答：** Azure 防禦在此時間點僅支援 RSA SSH 金鑰。 請確定您流覽的金鑰檔是 SSH 的 RSA 私密金鑰，並已在目標 VM 上布建公開金鑰。 

例如，您可以使用下列命令來建立新的 RSA SSH 金鑰：

**ssh-keygen-t rsa-b 4096-C "email@domain.com"**

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

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>無法登入已加入網域的 Windows 虛擬機器

**問：** 我無法連接到已加入網域的 Windows 虛擬機器。

**答：** Azure 防禦僅支援以使用者名稱-密碼為基礎的網域登入加入網域的 VM 登入。 在 Azure 入口網站中指定網域認證時，請使用 UPN （）username@domain格式，而不是可登入的網域*\* （&）格式。 這適用于已加入網域或已加入混合式（已加入網域以及已加入 Azure AD 的虛擬機器）。 不支援僅限已加入 Azure AD 的虛擬機器。

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>檔案傳輸問題

**問：** Azure 防禦是否支援檔案傳輸？

**答：** 目前不支援檔案傳輸。 我們正努力加入支援。

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure 入口網站中的黑色畫面

**問：** 當我嘗試使用 Azure 防禦進行連接時，會在 Azure 入口網站中看到黑色畫面。

**答：** 當您的網頁瀏覽器與 Azure 防禦（您的用戶端網際網路防火牆可能會封鎖 Websocket 流量或類似），或在 Azure 防禦與您的目標 VM 之間發生網路連線問題時，就會發生這種情況。 大部分的情況包括套用至 AzureBastionSubnet 的 NSG，或在您的目標 VM 子網上，會封鎖您虛擬網路中的 RDP/SSH 流量。 允許用戶端網際網路防火牆上的 Websocket 流量，並檢查目標 VM 子網上的 Nsg。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱防禦[常見問題](bastion-faq.md)。