---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76021052"
---
1. 登入 [Azure 入口網站][Azure portal]。
1. 選取 [建立資源]  。 然後，選取 [整合]   > [轉送]  。 如果清單中未顯示 [轉送]  ，請選取右上角的 [查看全部]  。
1. 選取 [建立]  ，然後在 [名稱]  欄位中輸入命名空間的名稱。 Azure 入口網站會檢查此名稱是否可用。
1. 選擇要在其中建立命名空間的 Azure 訂用帳戶。
1. 針對[資源群組](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)，選擇將放置命名空間的現有資源群組，或是建立新的資源群組。  
1. 選取要用來裝載命名空間的國家或地區。

    ![建立命名空間][create-namespace]

1. 選取 [建立]  。 Azure 入口網站便會建立並啟用命名空間。 幾分鐘後，系統便會為您的帳戶佈建資源。

### <a name="get-management-credentials"></a>取得管理認證

1. 選取 [所有資源]  ，然後選擇新建立的命名空間名稱。
1. 選取 [共用存取原則]  。  
1. 在 [共用存取原則]  下，選取 [RootManageSharedAccessKey]  。
1. 在 [SAS 原則：  RootManageSharedAccessKey] 下，選取 [主要連接字串]  旁邊的 [複製]  按鈕。 此動作會將連接字串複製到剪貼簿以供稍後使用。 將此值貼到記事本或一些其他暫存位置。
1. 重複前一個步驟，複製 [主要金鑰]  的值並貼到暫存位置以供稍後使用。  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
