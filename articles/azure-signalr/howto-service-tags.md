---
title: 使用服務標記
titleSuffix: Azure SignalR Service
description: 使用服務標籤來允許連至您 Azure SignalR Service 的輸出流量
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152318"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>使用服務標記進行 Azure SignalR Service

您可以在設定[網路安全性群組](../virtual-network/network-security-groups-overview.md#network-security-groups)時，將[服務標記](../virtual-network/network-security-groups-overview.md#service-tags)用於 Azure SignalR Service。 它可讓您定義輸出網路安全性規則來 Azure SignalR Service 端點，而不需要將 IP 位址硬式編碼。

Azure SignalR Service 管理這些服務標記。 您無法建立自己的服務標籤或修改現有的服務標記。 Microsoft 會管理這些與服務標籤相符的位址前置詞，並隨著位址變更自動更新服務標記。

## <a name="use-service-tag-on-portal"></a>在入口網站上使用服務標記

您可以藉由新增輸出網路安全性規則，允許連出流量 Azure SignalR Service：

1. 移至 [網路安全性群組]。

1. 按一下 [設定] 功能表，稱為 [ **輸出安全性規則**]。

1. 按一下頂端的 [ **+ 新增** ] 按鈕。

1. 選擇 [**目的地**] 底下的 [**服務標記**]。

1. 選擇 [**目的地服務標記**] 下的 [ **>azuresignalr-sample** ]。

1. 在 [**目的地埠範圍**] 中填入**443** 。

    ![建立輸出安全性規則](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. 根據您的需求調整其他欄位。

1. 按一下 [新增]  。


## <a name="next-steps"></a>後續步驟

- [網路安全性群組：服務標記](../virtual-network/network-security-groups-overview.md#security-rules)