---
title: 使用 Geth 連結至 Azure 區塊鏈服務
description: 連結至 Azure 區塊鏈服務交易節點上的 Geth 執行個體
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 2c5b6a56d96ac132949052e9879c8f61d41ffb06
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "83994760"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>快速入門：使用 Geth 連結至 Azure 區塊鏈服務交易節點

在本快速入門中，使用 Geth 用戶端連結至 Azure 區塊鏈服務交易節點上的 Geth 執行個體。 附加之後，您可以使用 Geth 主控台來呼叫 Ethereum JavaScript API。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 安裝 [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* 完成[快速入門：使用 Azure 入口網站建立區塊鏈成員](create-member.md)或[快速入門：使用 Azure CLI 建立 Azure 區塊鏈服務的區塊鏈成員](create-member-cli.md)

## <a name="get-geth-connection-string"></a>取得 Geth 連接字串

您可以在 Azure 入口網站中取得 Azure 區塊鏈服務交易節點的 Geth 連接字串。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的 Azure 區塊鏈服務成員。 選取 [交易節點] 和預設交易節點連結。

    ![選取預設交易節點](./media/connect-geth/transaction-nodes.png)

1. 選取 [連接字串]。
1. 從 [HTTPS (存取金鑰 1)] 複製連接字串。 您在下一節將需要此字串。

    ![連接字串](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>連線至 Geth

1. 開啟命令提示字元或殼層。
1. 使用 Geth attach 子命令，連結至您交易節點上執行中的 Geth 執行個體。 貼上連接字串，作為 attach 子命令的引數。 例如：

    ``` bash
    geth attach <connection string>
    ```

1. 連線至交易節點的 Ethereum 主控台後，您可以使用 Ethereum JavaScript API。

    例如，使用下列 API 找出 chainId。

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    在此範例中，chainId 為 661。

    ![Azure 區塊鏈服務選項](./media/connect-geth/geth-attach.png)

1. 若要從主控台中斷連線，請輸入 `exit`。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Geth 用戶端連結至 Azure 區塊鏈服務交易節點上的 Geth 執行個體。 請嘗試進行下一個教學課程，使用適用於 Ethereum 的 Azure 區塊鏈開發套件，透過交易建立、建置、部署及執行智慧型合約函式。

> [!div class="nextstepaction"]
> [在 Azure 區塊鏈服務上建立、建立和部署智慧型合約](send-transaction.md)
