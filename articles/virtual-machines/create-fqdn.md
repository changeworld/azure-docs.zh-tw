---
title: 在 Azure 入口網站中建立 VM 的 FQDN
description: 瞭解如何為 Azure 入口網站中的虛擬機器建立 (FQDN) 的完整功能變數名稱。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132059"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 入口網站中建立 Linux VM 的完整網域名稱

在 [Azure 入口網站](https://portal.azure.com) 中建立虛擬機器 (VM) 時，會自動建立虛擬機器的公用 IP 資源。 您可以使用此 IP 位址從遠端存取 VM。 雖然入口網站不會建立 [fully qualified domain name (完整網域名稱)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(或稱 FQDN)，但是您可以在 VM 建立之後新增一個。 這篇文章示範建立 DNS 名稱或 FQDN 的步驟。 

## <a name="create-a-fqdn"></a>建立 FQDN
此文章假設您已經建立一個 VM。 如有需要，您可以在入口網站中建立 [Linux](./linux/quick-create-portal.md) 或 [Windows](./windows/quick-create-portal.md) VM。 在您的 VM 已啟動並執行之後，請遵循下列步驟：


1. 在入口網站中選取您的 VM。 
1. 在左側功能表中 **，選取 [** 設定]
1. 在 [ **DNS 名稱] 標籤** 下，輸入您要使用的前置詞。
1. 在頁面上方選取 [儲存]  。
1. 選取左側功能表中的 **[總覽** ]，返回 VM 總覽分頁。 
1. 確認 *DNS 名稱* 是否正確出現。 

## <a name="next-steps"></a>後續步驟

您也可以使用 [Azure DNS 區域](../dns/dns-getstarted-portal.md)管理 DNS。

