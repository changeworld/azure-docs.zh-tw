---
title: 包含檔案
description: 包含檔案
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 482241deb1081ac8a5265a076eabbdc3fb6d659e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173907"
---
雖然使用系統路由可自動加速您部署的流量，但是也有一些您希望透過虛擬設備控制封包路由的情況。 您可以透過建立使用者定義的路由 (其指定流向指定子網路之封包的下個躍點，改為流向您的虛擬設備)，並啟用做為虛擬設備執行之 VM 的 IP 轉送。

以下提供一些可使用虛擬應用裝置的案例：

* 使用入侵偵測系統 (IDS) 監視流量
* 使用防火牆控制流量

如需 UDR 和 IP 轉送的詳細資訊，請參閱 [使用者定義的路由和 IP 轉送](../articles/virtual-network/virtual-networks-udr-overview.md)。

