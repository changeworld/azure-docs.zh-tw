---
title: 在 Azure 資料資源管理器中保護群集
description: 本文介紹如何在 Azure 門戶中的 Azure 資料資源管理器中保護群集。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720339"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>在 Azure 資料資源管理器中保護群集 - Azure 門戶

[Azure 磁片加密](/azure/security/azure-security-disk-encryption-overview)有助於保護資料，以滿足組織安全性和合規性承諾。 它為群集虛擬機器的作業系統和資料磁片提供卷加密。 它還與 Azure[金鑰保存庫](/azure/key-vault/)集成，這使我們能夠控制和管理磁片加密金鑰和機密，並確保 VM 磁片上的所有資料都加密。 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>在 Azure 門戶中啟用靜態加密
  
群集安全設置允許您在群集上啟用磁片加密。 在群集上啟用[靜態加密](/azure/security/fundamentals/encryption-atrest)為存儲的資料（靜態）提供資料保護。 

1. 在 Azure 門戶中，轉到 Azure 資料資源管理器群集資源。 在 **"設置"** 標題下，選擇 **"安全**"。 

    ![在靜止時打開加密](media/manage-cluster-security/security-encryption-at-rest.png)

1. 在 **"安全"** 視窗中，為 **"磁片加密**安全設置**打開**"。 

1. 選取 [儲存]****。
 
> [!NOTE]
> 選擇 **"關閉**"以在加密啟用後禁用加密。

## <a name="next-steps"></a>後續步驟

[檢查叢集健康情況](/azure/data-explorer/check-cluster-health)
