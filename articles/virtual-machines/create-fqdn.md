---
title: 在 Azure 入口網站中建立 VM 的 FQDN
description: 瞭解如何為 Azure 入口網站中的虛擬機器建立 (FQDN) 的完整功能變數名稱。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351882"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 入口網站中建立 Linux VM 的完整網域名稱

在 [Azure 入口網站](https://portal.azure.com) 中建立虛擬機器 (VM) 時，會自動建立虛擬機器的公用 IP 資源。 您可以使用此 IP 位址從遠端存取 VM。 雖然入口網站不會建立 [fully qualified domain name (完整網域名稱)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(或稱 FQDN)，但是您可以在 VM 建立之後新增一個。 這篇文章示範建立 DNS 名稱或 FQDN 的步驟。 

## <a name="create-a-fqdn"></a>建立 FQDN
此文章假設您已經建立一個 VM。 如有需要，您可以在入口網站中建立 [Linux](./linux/quick-create-portal.md) 或 [Windows](./windows/quick-create-portal.md) VM。 在您的 VM 已啟動並執行之後，請遵循下列步驟：


1. 在入口網站中選取您的 VM。 在 [ **DNS 名稱** ] 底下，選取 [ **設定** ]。
2. 輸入 DNS 名稱，然後選取頁面頂端的 [ **儲存** ]。
3. 若要返回 VM 總覽分頁，請選取右上 **角的** **X** 來關閉設定分頁。 
4. 確認 *DNS 名稱* 現在已正確顯示。
   



## <a name="next-steps"></a>後續步驟
現在，您的 VM 有公用 IP 和 DNS 名稱，您可以部署通用應用程式架構或服務，例如 nginx、MongoDB 和 Docker。

您也可以進一步閱讀[使用 Resource Manager](../azure-resource-manager/management/overview.md)，以取得建置 Azure 部署的相關秘訣。

