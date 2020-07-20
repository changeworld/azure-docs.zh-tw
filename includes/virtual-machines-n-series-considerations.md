---
title: 包含檔案
description: 包含檔案
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 202bffb03a73acde67f0d4a03b31aa9a6fbf9824
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901864"
---
## <a name="deployment-considerations"></a>部署考量因素

* 如需了解 N 系列 VM 的可用性，請參閱[依區域提供的產品](https://azure.microsoft.com/regions/services/)。

* N 系列 VM 只能部署在 Resource Manager 部署模型。

* N 系列 VM 對其磁碟支援的 Azure 儲存體類型有所不同。 NC 和 NV VM 只支援標準磁碟儲存體 (HDD) 所支持的 VM 磁碟。 NCv2、NCv3、ND、NDv2 和 NVv2 只支援進階磁碟儲存體 (SSD) 所支持的 VM 磁碟。

* 如果您想要部署不只一些 N 系列 VM ，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

* 您可能需要增加 Azure 訂用帳戶的核心配額 (依地區)，以及增加 NC、NCv2、NCv3、ND、NDv2、NV 或 NVv2 核心的個別配額。 若要要求增加配額，可免費[開啟線上客戶支援要求](../articles/azure-portal/supportability/how-to-create-azure-support-request.md)。 預設限制會視您的訂用帳戶類別而有所不同。




