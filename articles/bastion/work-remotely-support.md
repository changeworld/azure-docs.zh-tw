---
title: 使用堡壘遠端工作：Azure 堡壘
description: 本頁介紹如何利用 Azure 堡壘啟用由於 COVID-19 大流行而遠端工作。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: c0bba2560cccc208e2384421218ecebdfef65236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333074"
---
# <a name="working-remotely-using-azure-bastion"></a>使用 Azure 堡壘遠端工作

Azure Bastion 通過允許具有 Internet 連接的使用者訪問 Azure 虛擬機器，在支援遠端工作方案方面發揮著關鍵作用。 特別是，它使 IT 管理員能夠隨時隨地管理在 Azure 上運行的應用程式。

>[!NOTE]
>本文介紹如何利用 Azure 堡壘、Azure、Microsoft 網路和 Azure 合作夥伴生態系統遠端工作，並緩解因 COVID-19 危機而面臨的網路問題。
>

## <a name="securely-access-virtual-machines"></a>安全訪問虛擬機器

具體而言，Azure Bastion 提供安全、無縫的 RDP/SSH 連接，直接在 Azure 門戶中與 Azure 虛擬網路中的虛擬機器連接，而無需使用公共 IP 位址。 有關 Azure 堡壘體系結構和關鍵功能的詳細資訊，請查看什麼是[Azure 堡壘](bastion-overview.md)。

Azure 堡壘部署于每個虛擬網路，這意味著公司可以配置和管理一個 Azure 堡壘，以快速支援遠端使用者訪問 Azure 虛擬網路中的虛擬機器。 有關如何創建和管理 Azure 堡壘的指導，請參閱[創建堡壘主機](bastion-create-host-portal.md)。

## <a name="next-steps"></a>後續步驟

* 使用[Azure 門戶](bastion-create-host-portal.md)、[電源外殼](bastion-create-host-powershell.md)或 Azure CLI 配置 Azure 堡壘。

* 有關詳細資訊，請閱讀[堡壘常見問題解答](bastion-faq.md)。
