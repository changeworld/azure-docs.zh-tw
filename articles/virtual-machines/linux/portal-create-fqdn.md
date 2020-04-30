---
title: 在 Azure 入口網站中建立 VM 的 FQDN
description: 了解如何在 Azure 入口網站中為基於 Resource Manager 的虛擬機器建立完整格式的網域名稱或 FQDN。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51b9ecf8e300af6b1e3f11d8431de7a282ab342
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759362"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 入口網站中建立 Linux VM 的完整網域名稱

在 [Azure 入口網站](https://portal.azure.com) 中建立虛擬機器 (VM) 時，會自動建立虛擬機器的公用 IP 資源。 您可以使用此 IP 位址從遠端存取 VM。 雖然入口網站不會建立 [fully qualified domain name (完整網域名稱)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(或稱 FQDN)，但是您可以在 VM 建立之後新增一個。 這篇文章示範建立 DNS 名稱或 FQDN 的步驟。

## <a name="create-a-fqdn"></a>建立 FQDN
此文章假設您已經建立一個 VM。 如果需要，您可以[在入口網站中建立一個 VM](quick-create-portal.md) 或[使用 Azure CLI](quick-create-cli.md) 來建立。 在您的 VM 已啟動並執行之後，請遵循下列步驟：

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

您現在可以使用這個 DNS 名稱 (例如搭配 `ssh azureuser@mydns.westus.cloudapp.azure.com`) 從遠端連接到 VM。

## <a name="next-steps"></a>後續步驟
既然您的 VM 已經有公用 IP 和 DNS 名稱，您便可以部署通用應用程式架構或服務，例如 nginx、MongoDB、Docker 等等。

您也可以進一步閱讀[使用 Resource Manager](../../azure-resource-manager/management/overview.md)，以取得建置 Azure 部署的相關秘訣。

