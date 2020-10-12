---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02ced43f8c3fc7c83359b78362e8ad0feeab3070
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "72168383"
---
>[!NOTE]
>使用預設原則時，Azure 在 IPsec 通道設定期間可以同時作為啟動器和回應者。 不支援 Azure 作為回應者。
>

### <a name="initiator"></a>Initiator

以下小節列出當 Azure 是通道的啟動器時，所支援的原則組合。

**第 1 階段**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2

**第 2 階段**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE

### <a name="responder"></a>回應者

以下小節列出當 Azure 是通道的回應者時，所支援的原則組合。

**第 1 階段**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2

**第 2 階段**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE
* AES_256、SHA_1、PFS_1
* AES_256、SHA_1、PFS_2
* AES_256、SHA_1、PFS_14
* AES_128、SHA_1、PFS_1
* AES_128、SHA_1、PFS_2
* AES_128、SHA_1、PFS_14
* AES_256、SHA_256、PFS_1
* AES_256、SHA_256、PFS_2
* AES_256、SHA_256、PFS_14
* AES_256、SHA_1、PFS_24
* AES_256、SHA_256、PFS_24
* AES_128、SHA_256、PFS_NONE
* AES_128、SHA_256、PFS_1
* AES_128、SHA_256、PFS_2
* AES_128、SHA_256、PFS_14