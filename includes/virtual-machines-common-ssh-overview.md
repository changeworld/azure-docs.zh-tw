---
title: 包含檔案
description: 包含檔案
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "71168613"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 和金鑰的概觀

[SSH](https://www.ssh.com/ssh/)是一種加密的連線通訊協定，允許透過不安全的連接進行安全登入。 SSH 是 Azure 中裝載 Linux VM 的預設連線通訊協定。 雖然 SSH 本身提供加密的連線，但使用密碼搭配 SSH 連線仍會讓 VM 容易遭受暴力密碼破解攻擊或密碼猜測。 使用公開/私密金鑰組 (也就是 *SSH 金鑰*) 連線至 VM 是更安全且慣用的方法。 

* *公開金鑰*會置於您的 Linux VM 或任何您要搭配公開金鑰加密使用的其他服務上。

* 「私密金鑰」** 會保留在您的本機系統上。 保護此私密金鑰。 不要共用它。

當您使用 SSH 用戶端連線到您的 Linux VM (其有公開金鑰) 時，遠端 VM 會測試用戶端以確保它擁有私密金鑰。 如果用戶端具有私密金鑰，則會獲得 VM 的存取權。 

根據組織的安全性原則，您可以重複使用單一公用/私密金鑰組來存取多個 Azure VM 和服務。 您想要存取的每個 VM 或服務都不需要各有一對金鑰。 

公開金鑰可以與任何人共用；但應該只有您 (或您的本機安全性基礎結構) 擁有私密金鑰。