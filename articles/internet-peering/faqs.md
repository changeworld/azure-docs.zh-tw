---
title: 互聯網對等互連 - 常見問題解答
titleSuffix: Azure
description: 互聯網對等互連 - 常見問題解答
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775494"
---
# <a name="internet-peering---faqs"></a>互聯網對等互連 - 常見問題解答

您可以查看以下資訊，瞭解一般問題。

**互聯網對等互連和對等服務之間的區別是什麼？**

對等服務是一項旨在為其企業客戶提供企業級公共 IP 連接的服務。 企業級 Internet 包括通過與 Microsoft 具有高輸送量連接的 ISP 的連接和 HA 連接的冗余。 此外，使用者流量針對延遲到最近的 Microsoft Edge 進行了優化。 對等服務基於與合作夥伴運營商的對等互連連接。 與合作夥伴的對等互連連接必須是直接對等互連，而不是 Exchange 對等互連。 直接對等互連必須具有本地和地理冗余。

**什麼是傳統對等互連？**

使用 Azure PowerShell 設置的對等互連連接作為 Azure 資源進行管理。 過去設置的對等互連連接存儲在我們的系統中，作為舊對等互連，您可以選擇將其轉換為作為 Azure 資源進行管理。

**當調用 New-Az 對等互連直接連接物件時，向 Microsoft 和對等設備提供了哪些 IP 位址？**

調用 New-AzPingDirectConnectObject Cmdlet 時，將輸入 /31 位址 （a.b.c.d/31） 或 /30 位址 （a.b.c.d/30）。 第一個 IP 位址 （a.b.c.d_0） 提供給對等方的設備，第二個 IP 位址 （a.b.c.d_1） 提供給 Microsoft 設備。

**什麼是 MaxPrefixes 廣告IPv4 和 MaxPrefixes 廣告IPv6 參數在 New-AzPing 直接連接物件 Cmdlet？**

MaxPrefixes廣告IPv4和MaxPrefixes廣告IPv6參數表示 IPv4 的最大數量，IPv6 首碼是同行希望 Microsoft 接受的。 這些參數可以隨時修改。