---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875533"
---
DNS 設定不是此設定的必要部分，但如果您想要在 Vm 之間進行名稱解析，則需要 DNS。 指定一個值並不會建立新的 DNS 伺服器。 您指定的 DNS 伺服器 IP 位址應該是可以解析您所連線之資源名稱的 DNS 伺服器。

建立虛擬網路之後，您可以新增 DNS 伺服器的 IP 位址，以便處理名稱解析。 開啟虛擬網路的設定，選取 [DNS 伺服器]，然後新增您想要用於名稱解析之 DNS 伺服器的 IP 位址。

1. 在入口網站中找出虛擬網路。
2. 在虛擬網路的頁面上，選取 [ **設定** ] 區段底下的 [ **DNS 伺服器**]。
3. 新增 DNS 伺服器。
4. 若要儲存您的設定，請選取頁面頂端的 [ **儲存** ]。