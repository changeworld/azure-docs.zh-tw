---
title: 包含檔案
description: 包含檔案
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "83006374"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>從 Azure 入口網站複製您的認證

當應用程式範例向 Azure 儲存體發出要求時，該要求必須獲得授權。 若要對要求授權，請以連接字串的形式將儲存體帳戶認證新增至應用程式。 請依照下列步驟檢視您的儲存體帳戶認證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 找出您的儲存體帳戶。
3. 在儲存體帳戶概觀的 [設定] 區段中，選取 [存取金鑰]。 在此處，您可以檢視帳戶存取金鑰，和每個金鑰的完整連接字串。
4. 尋找 [金鑰 1] 下方的 [連接字串] 值，然後選取 [複製] 按鈕以複製連接字串。 在下一個步驟中，您會將連接字串值新增至環境變數。

    ![顯示如何從 Azure 入口網站複製連接字串的螢幕擷取畫面](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

複製連接字串之後，請在 *MainPage.xaml.cs* 檔案中將其設定為類別層級變數。 開啟 *MainPaage.xaml.cs* 並尋找 `storageConnectionString` 變數。 將 `<yourconnectionstring>` 用實際的連接字串取代。

此程式碼如下：

```csharp
string storageConnectionString = "<yourconnectionstring>";
```