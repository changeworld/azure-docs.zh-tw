---
title: 包含檔案
description: 包含檔案
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012064"
---
## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您會建立虛擬網路和子網路。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]  ，或在搜尋方塊中搜尋 [虛擬網路]  。

2. 在 [建立虛擬網路]  中，在 [基本]  索引標籤中輸入或選取這項資訊：

    | **設定**          | **ReplTest1**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 [新建]，輸入 **\<resource-group-name>** ，然後選取 [確定]，或根據參數選取現有的 **\<resource-group-name>** 。 |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **\<virtual-network-name>**                                    |
    | 區域           | 選取 **\<region-name>** |

3. 選取 [IP 位址]  索引標籤，或選取頁面底部的 [下一步：  IP 位置] 按鈕。

4. 在 [IP 位址]  索引標籤中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | IPv4 位址空間 | 輸入 **\<IPv4-address-space>** |

5. 在 [子網路名稱]  下，選取 [預設]  字組。

6. 在 [編輯子網路]  中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | 子網路名稱 | 輸入 **\<subnet-name>** |
    | 子網路位址範圍 | 輸入 **\<subnet-address-range>**

7. 選取 [儲存]  。

8. 選取 [檢閱 + 建立]  索引標籤，或選取 [檢閱 + 建立]  按鈕。

9. 選取 [建立]  。