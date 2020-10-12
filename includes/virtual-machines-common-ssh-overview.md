---
title: 包含檔案
description: 包含檔案
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513176"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 和金鑰的概觀

[SSH](https://www.ssh.com/ssh/) 是一種加密的連接通訊協定，可透過不安全的連線提供安全登入。 SSH 是 Azure 中裝載 Linux VM 的預設連線通訊協定。 雖然 SSH 提供加密連線，但搭配使用密碼與 SSH 連線仍會讓 VM 容易遭受暴力密碼破解攻擊。 建議使用公開/私密金鑰組（也稱為 *SSH 金鑰*）透過 SSH 連線至 VM。 

- *公開金鑰*會放置在您的 Linux VM 上。

- 「私密金鑰」** 會保留在您的本機系統上。 保護此私密金鑰。 不要共用它。

當您使用 SSH 用戶端連線到具有公開金鑰) 的 Linux VM (時，遠端 VM 會測試用戶端，以確保其具有正確的私密金鑰。 如果用戶端具有私密金鑰，則會獲得 VM 的存取權。 

根據組織的安全性原則，您可以重複使用單一公用/私密金鑰組來存取多個 Azure VM 和服務。 您想要存取的每個 VM 或服務都不需要各有一對金鑰。 

您的公開金鑰可以與任何人共用，但只有您 (或您的本機安全性基礎結構) 才能存取您的私密金鑰。