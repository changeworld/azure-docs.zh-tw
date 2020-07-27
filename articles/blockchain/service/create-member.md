---
title: 建立 Azure 區塊鏈服務成員 - Azure 入口網站
description: 使用 Azure 入口網站建立區塊鏈聯盟的 Azure 區塊鏈服務成員。
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 5632c8a17c5ac25c8b4b436297d0dd5a0071df26
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503416"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立 Azure 區塊鏈服務的區塊鏈成員

在本快速入門中，您會使用 Azure 入口網站在 Azure 區塊鏈服務中部署新的區塊鏈成員和聯盟。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

無。

## <a name="create-a-blockchain-member"></a>建立區塊鏈成員

Azure 區塊鏈服務成員是私人聯盟區塊鏈網路中的區塊鏈節點。 在佈建成員時，您可以建立或加入聯盟網路。 一個聯盟網路至少需要一個成員。 參與者所需的區塊鏈成員數目視您的案例而定。 聯盟參與者可以有一或多個區塊鏈成員，也可以與其他參與者共用成員。 如需聯盟的詳細資訊，請參閱 [Azure 區塊鏈服務聯盟](consortium.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 Azure 入口網站左上角的 [建立資源]  。
1. 選取 [區塊鏈]   > [Azure 區塊鏈服務 (預覽)]  。

    ![Create Service](./media/create-member/create-member.png)

    設定 | 描述
    --------|------------
    訂用帳戶 | 選取您要用於服務的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選擇資源計費的訂用帳戶。
    資源群組 | 建立新的資源群組名稱，或選擇您訂用帳戶中現有的資源群組名稱。
    區域 | 選擇要建立成員的區域。 聯盟的所有成員都必須位於相同的位置。 某些區域可能無法使用這些功能。 下列 Azure 區域提供 Azure 區塊鏈資料管理員服務：美國東部和西歐。
    通訊協定 | 目前，Azure 區塊鏈服務預覽版支援仲裁通訊協定。
    聯盟 | 針對新的聯盟，請輸入唯一名稱。 如果是透過邀請來加入聯盟，請選擇您要加入的聯盟。 如需聯盟的詳細資訊，請參閱 [Azure 區塊鏈服務聯盟](consortium.md)。
    名稱 | 選擇 Azure 區塊鏈服務成員的唯一名稱。 區塊鏈成員的名稱只能包含小寫字母和數字。 第一個字元必須是字母。 此值長度必須介於 2 至 20 個字元之間。
    成員帳戶密碼 | 成員帳戶密碼可用來對為您的成員建立的 Ethereum 帳戶進行私密金鑰加密。 您可以使用帳戶成員和成員帳戶密碼進行聯盟管理。
    定價 | 新服務的節點組態和成本。 選取 [變更]  連結，以在 [標準]  和 [基本]  層之間做選擇。 使用「基本」層來進行開發、測試和概念證明。 使用「標準」層來進行生產等級的部署。 如果您使用區塊鏈資料管理員或傳送大量的私人交易，也應該使用*標準*層。 不支援在建立成員之後變更基本和標準間的定價層。
    節點密碼 | 成員預設交易節點的密碼。 連線至區塊鏈成員的預設交易節點公用端點時，請使用此密碼進行基本驗證。

1. 選取 [檢閱 + 建立]  以驗證您的設定。 選取 [建立]  以佈建服務。 佈建需要大約 10 分鐘的時間。
1. 若要監視部署程序，請在工具列上選取 [通知]  。
1. 部署之後，瀏覽至您的區塊鏈成員。

選取 [概觀]  ，即可檢視您服務的基本資訊，包括 RootContract 位址和成員帳戶。

![區塊鏈成員概觀](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>清除資源

您可以使用您建立的成員來進行下一個快速入門或教學課程。 當不再需要資源時，您可藉由刪除您為本快速入門建立的 `myResourceGroup` 資源群組來刪除資源。

若要刪除資源群組：

1. 在 Azure 入口網站中，瀏覽至左側瀏覽窗格中的 [資源群組]，然後選取您想要刪除的資源群組。
2. 選取 [刪除資源群組]。 輸入資源群組名稱並選取 [刪除]，以確定進行刪除。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Azure 區塊鏈服務成員和新的聯盟。 請嘗試進行下一個快速入門，使用適用於 Ethereum 的 Azure 區塊鏈開發套件連結至 Azure 區塊鏈服務成員。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)
