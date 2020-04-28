---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174023"
---
<!--v-sharos 10/13/2105 virtual device security-->

使用 StorSimple 虛擬裝置時，請留意下列安全性考量：

* 虛擬裝置會透過您的 Microsoft Azure 訂用帳戶受到保護。 這表示，如果您使用的是虛擬裝置，而您的 Azure 訂用帳戶遭到洩漏，則儲存在虛擬裝置上的資料也會受到影響。
* 用來對儲存在 Azure StorSimple 中的資料加密的憑證公開金鑰，會安全地在 Azure 傳統入口網站中供人使用，而私密金鑰則會保留在 StorSimple 裝置中。 在 StorSimple 虛擬裝置上，公開和私密金鑰都會儲存於 Azure 中。
* 虛擬裝置會裝載於 Microsoft Azure 資料中心。

