---
title: 註冊 Azure VMware 解決方案資源提供者
description: 註冊 Azure VMware 解決方案資源提供者的步驟。
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770808"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

若要使用 Azure VMware 解決方案，您必須先向您的訂用帳戶註冊資源提供者。  

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```


### <a name="azure-portal"></a>Azure 入口網站
 
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站功能表上，選取 [所有服務]。

1. 在 [所有服務] 方塊中，輸入 [訂用帳戶]，然後選取 [訂用帳戶]。

1. 從訂用帳戶清單中選取要檢視的訂用帳戶。

1. 選取 [資源提供者] 並在搜尋中輸入 **Microsoft.AVS**。 
 
1. 若資源提供者未註冊，請選取 [註冊]。