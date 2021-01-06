---
title: 針對 Azure 防禦進行疑難排解 |Microsoft Docs
description: 在本文中，您將瞭解如何針對 Azure 防禦進行疑難排解。
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: d4f5d4b7fed8f14f048794616ee272342d1e8343
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915550"
---
# <a name="troubleshoot-azure-bastion"></a>對 Azure Bastion 進行疑難排解

本文說明如何針對 Azure 防禦進行疑難排解。

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>無法在 AzureBastionSubnet 上建立 NSG

**問：** 當我嘗試在 Azure 防禦子網上建立 NSG 時，出現下列錯誤：「 *網路安全性群組沒有 Azure 防禦 <NSG name> 子網的必要規則 AzureBastionSubnet*」。

**答：** 如果您建立 NSG 並將其套用至 *AzureBastionSubnet*，請確定您已在 NSG 中新增下列規則。 如果您未新增這些規則，NSG 建立/更新將會失敗。

1. 控制平面連接–從 GatewayManager 到443的輸入
2. 診斷記錄和其他專案–在443上的輸出 (此服務標籤內的區域標記尚未受到支援。 ) 
3. 目標 VM –將3389和22輸出至 VirtualNetwork

您可以在 [快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)中參考 NSG 規則的範例。
如需詳細資訊，請參閱 [Azure 防禦的 NSG 指引](bastion-nsg.md)。

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>無法搭配使用 SSH 金鑰與 Azure 防禦

**問：** 當我嘗試流覽 SSH 金鑰檔案時，出現下列錯誤： *「SSH 私密金鑰的開頭必須是-----開始 RSA 私用金鑰-----並以-----END RSA 私密金鑰-----」* 結尾。

**答：** Azure 防禦在此時間點僅支援 RSA SSH 金鑰。 請確定您流覽的金鑰檔是適用于 SSH 的 RSA 私密金鑰，並在目標 VM 上布建公開金鑰。 

例如，您可以使用下列命令來建立新的 RSA SSH 金鑰：

**ssh-keygen-t rsa-b 4096-C " email@domain.com "**

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

**答：** Azure 防禦僅支援已加入網域的 VM 登入，以進行使用者名稱密碼型網域登入。 在 Azure 入口網站中指定網域認證時，請使用 UPN (username@domain) 格式，而不是使用網域 *\* 網域格式來登入。 已加入網域或已加入混合式 (已加入網域，以及已加入 Azure AD 的) 虛擬機器都支援此功能。 Azure AD 聯結的虛擬機器不支援此功能。

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>檔案傳輸問題

**問：** Azure 防禦是否支援檔案傳輸？

**答：** 目前不支援檔案傳輸。 我們正致力於新增支援。

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure 入口網站中的黑色畫面

**問：** 當我嘗試使用 Azure 防禦進行連線時，我無法 connnect 至目標 VM，而是在 Azure 入口網站中取得黑色畫面。

**答：** 當您的網頁瀏覽器和 Azure 防禦之間有網路連線問題時，會發生這種情況 (您的用戶端網際網路防火牆可能會封鎖 Websocket 流量或類似) ，或在 Azure 防禦和您的目標 VM 之間。 大部分的情況包括將 NSG 套用至 AzureBastionSubnet，或在您的目標 VM 子網上封鎖您虛擬網路中的 RDP/SSH 流量。 允許用戶端網際網路防火牆上的 Websocket 流量，並檢查目標 VM 子網上的 Nsg。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱防禦 [常見問題](bastion-faq.md)。
