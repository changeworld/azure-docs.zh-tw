---
title: 包含檔案
description: 包含檔案
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: mhopkins
ms.custom: include file
ms.openlocfilehash: 7dd22886d11c3a35a7a866ff7c9a4f56ea74cab7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "75351231"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>從 Azure 入口網站複製您的認證

當應用程式範例向 Azure 儲存體發出要求時，該要求必須獲得授權。 若要對要求授權，請以連接字串的形式將儲存體帳戶認證新增至應用程式。 請依照下列步驟檢視您的儲存體帳戶認證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 找出您的儲存體帳戶。
3. 在儲存體帳戶概觀的 [設定]  區段中，選取 [存取金鑰]  。 在此處，您可以檢視帳戶存取金鑰，和每個金鑰的完整連接字串。
4. 尋找 [金鑰 1] 下方的 [連接字串] 值，然後選取 [複製] 按鈕以複製連接字串。 在下一個步驟中，您會將連接字串值新增至環境變數。

    ![顯示如何從 Azure 入口網站複製連接字串的螢幕擷取畫面](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

在複製您的連接字串後，請在執行應用程式的本機電腦上，將該字串寫入至新的環境變數中。 若要設定環境變數，請開啟主控台視窗，並遵循您的作業系統所適用的指示。 將 `<yourconnectionstring>` 用實際的連接字串取代。

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

在 Windows 中新增環境變數之後，您必須啟動新的命令視窗執行個體。

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>重新啟動程式

新增環境變數之後，請重新啟動任何需要讀取環境變數的執行中程式。 例如，在繼續之前，先重新啟動您的開發環境或編輯器。
