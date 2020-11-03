---
title: 邀請使用者加入影片索引子 - Azure
titleSuffix: Azure Media Services
description: 本文將說明如何邀請使用者加入影片索引子。
services: media-services
author: ReutAmior
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: 4023f8584e7b901a19906235d98982abbbfbaaf8
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364130"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>快速入門：邀請使用者加入影片索引子

若要與同事共同作業，您可以邀請他們加入您的影片索引子帳戶。 

> [!NOTE]
> 只有帳戶管理員可以新增或移除使用者。

## <a name="invite-new-users"></a>邀請新使用者

1. 登入 [Video Indexer](https://www.videoindexer.ai/) 網站。 請確定您已與系統管理員帳戶連線。
1. 如果您是管理員，會在右上角看到 **共用帳戶** 按鈕。 按一下按鈕，您就可以邀請使用者。 

   ![邀請新使用者](./media/invite-users/share-account.png)
1. 針對您要新增至影片索引子帳戶的人員，新增其電子郵件地址：

    ![邀請使用者加入此帳戶](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > 您邀請的所有使用者都將擁有您帳戶中所有影片的 [讀取] 和 [寫入] 權限。
1. 您邀請的使用者將會收到包含連結的電子郵件，一旦他們按一下 **加入影片索引子** 連結，就能存取該帳戶：

    ![確認](./media/invite-users/invite-msg.png)

    使用者必須按一下連結來加入，才能取得帳戶的存取權。 

## <a name="removing-existing-users"></a>移除現有的使用者

如果您想要移除具有帳戶存取權的使用者，您可以按一下其名稱旁邊的 **X** 符號：

![移除使用者](./media/invite-users/remove-users.png)

使用者不會在移除時收到通知。 移除之後，使用者將無權登入。

## <a name="next-steps"></a>後續步驟

您現在可以使用 [Video Indexer](video-indexer-view-edit.md) 網站或 [Video Indexer 開發人員入口網站](video-indexer-use-apis.md)來查看影片的深入解析。

## <a name="see-also"></a>另請參閱

- [影片索引子概觀](video-indexer-overview.md)
- [如何註冊及上傳您的第一支影片](video-indexer-get-started.md)
- [開始使用 API](video-indexer-use-apis.md)
