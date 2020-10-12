---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86230956"
---
- 不支援 ultra 磁片。
- 如果您的 Vm/虛擬機器擴展集上已啟用 Azure 磁碟加密 (使用 bitlocker/VM 解密) 的虛擬機器加密，則無法啟用。
- 無法在已啟用「主機加密」的磁片上啟用 Azure 磁碟加密。
- 您可以在現有的虛擬機器擴展集上啟用加密。 不過，只有在啟用加密之後建立的新 Vm 會自動加密。
- 必須將現有的 Vm 解除配置並重新配置，才能加密。