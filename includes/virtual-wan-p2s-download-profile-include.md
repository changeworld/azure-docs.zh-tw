---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561814"
---
1. 在虛擬 WAN 的頁面上，選取 [使用者 VPN 設定]。
1. 在頁面頂端，選取 [下載使用者 VPN 設定]。當您下載 WAN 層級設定時，您會取得以內建流量管理員為基礎的使用者 VPN 設定檔。 如需全域設定檔或以中樞為基礎的設定檔詳細資訊，請參閱[中樞設定檔](../articles/virtual-wan/global-hub-profile.md)。 全域設定檔簡化了容錯移轉案例。

   如果因為某些原因而無法使用中樞，服務所提供的內建流量管理可確保透過不同的中樞連線至點對站使用者的 Azure 資源連線。 您可以瀏覽至中樞，以下載中樞特定的 VPN 設定。 在 **使用者 VPN (點對站)** 中下載虛擬中樞 **使用者 VPN** 設定檔。
1. 檔案建立完成之後，請選取連結來下載。
1. 使用設定檔檔案來設定 VPN 用戶端。