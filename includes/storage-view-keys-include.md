---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208243"
---
要從 Azure 門戶查看和複製存儲帳戶訪問金鑰或連接字串，請執行以下操作：

1. 導航到[Azure 門戶](https://portal.azure.com)。
2. 找出您的儲存體帳戶。
3. 在 **"設置"** 下，選擇 **"便捷鍵**"。 您的帳戶存取金鑰隨即出現，此外也會顯示每個金鑰的完整連接字串。
4. 尋找 [金鑰 1]**** 下方的 [金鑰]**** 值，然後按一下 [複製]**** 按鈕以複製帳戶金鑰。
5. 或者，您也可以複製整個連接字串。 尋找 [金鑰1]**** 下方的 [連接字串]**** 值，然後按一下 [複製]**** 按鈕來複製連接字串。

    ![演示如何在 Azure 門戶中查看訪問金鑰的螢幕截圖](media/storage-view-keys-include/portal-connection-string.png)

可以使用任一鍵訪問 Azure 存儲，但通常最好使用第一個金鑰，並在旋轉金鑰時保留第二個鍵的使用。
