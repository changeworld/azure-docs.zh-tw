---
title: 使用防禦從遠端工作： Azure 防禦
description: 此頁面說明如何利用 Azure 防禦，讓您能夠從遠端工作，因為 COVID-19 流感。
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80619390"
---
# <a name="working-remotely-using-azure-bastion"></a>使用 Azure 防禦從遠端工作

Azure 防禦可讓具有網際網路連線能力的使用者存取 Azure 虛擬機器，以在支援遠端工作案例中扮演 pivotal 角色。 特別是，它可讓 IT 系統管理員隨時隨地管理其在 Azure 上執行的應用程式。

>[!NOTE]
>本文說明如何運用 Azure 防禦、Azure、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作，並減輕因 COVID-19 危機而面臨的網路問題。
>

## <a name="securely-access-virtual-machines"></a>安全地存取虛擬機器

具體而言，Azure 防禦會直接在 Azure 入口網站中，為 Azure 虛擬網路內的虛擬機器提供安全且順暢的 RDP/SSH 連線，而不需要使用公用 IP 位址。 如需 Azure 防禦架構和主要功能的詳細資訊，請參閱[什麼是 azure](bastion-overview.md)防禦。

Azure 防禦會針對每個虛擬網路部署，這表示公司可以設定及管理一個 Azure 防禦，以快速支援 Azure 虛擬網路內虛擬機器的遠端使用者存取。 如需有關如何建立和管理 Azure 防禦的指引，請參閱[建立防禦主機](bastion-create-host-portal.md)。

## <a name="next-steps"></a>後續步驟

* 使用[Azure 入口網站](bastion-create-host-portal.md)、 [PowerShell](bastion-create-host-powershell.md)或 Azure CLI 設定 Azure 防禦。

* 如需其他資訊，請閱讀防禦[常見問題](bastion-faq.md)。
