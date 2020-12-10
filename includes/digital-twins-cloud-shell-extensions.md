---
author: baanders
description: 包含 Azure Digital Twins 的檔案 - 設定最新的 IoT 擴充功能
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606568"
---
首先，執行此命令來查看已安裝的所有擴充功能清單。

```azurecli
az extension list
```

其輸出會是您目前擁有的所有擴充功能陣列。 尋找每個清單項目的 `"name"` 欄位，以查看擴充功能的名稱。

使用此輸出來判斷要執行下列哪些命令 (可能會執行多個) 來設定擴充功能。
* 如果清單中包含 `azure-iot`：您已有該擴充功能。 執行此命令以確定您有最新的更新，而且已沒有其他可用的更新：

   ```azurecli
   az extension update --name azure-iot
   ```

* 如果清單中 **未** 包含 `azure-iot`：您必須安裝此擴充功能。 使用此命令：

    ```azurecli
    az extension add --name azure-iot
    ```

* 如果清單中包含 `azure-iot-cli-ext`：這是舊版的擴充功能。 一次只應安裝一個擴充功能版本，因此請解除安裝舊版擴充功能。 使用此命令：

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```