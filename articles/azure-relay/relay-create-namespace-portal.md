---
title: 使用 Azure 入口網站建立轉送命名空間 | Microsoft Docs
description: 本文提供逐步解說，說明如何使用 Azure 入口網站建立轉送命名空間。
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 78ab6753-877a-4426-92ec-a81675d62a57
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: b9811ef92aba4891627c20e0269be136582a3304
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211992"
---
# <a name="create-a-relay-namespace-using-the-azure-portal"></a>使用 Azure 入口網站建立轉送命名空間

命名空間是所有 Azure 轉送元件的範圍容器。 多個轉送可以位於單一命名空間，而且命名空間通常會作為應用程式容器。 目前有 2 個不同的方式可建立轉送命名空間：

1. Azure 入口網站 (本文)。
2. [Azure Resource Manager](../azure-resource-manager/management/overview.md)範本。

## <a name="create-a-namespace-in-the-azure-portal"></a>在 Azure 入口網站中建立命名空間

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

恭喜！ 您現已建立轉送命名空間。

## <a name="next-steps"></a>後續步驟

* [轉送常見問題集](relay-faq.md)
* [開始使用 .NET](relay-hybrid-connections-dotnet-get-started.md)
* [開始使用 Node](relay-hybrid-connections-node-get-started.md)

