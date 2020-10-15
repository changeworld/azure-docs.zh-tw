---
title: 教學課程：建立別名記錄以參考區域中的資源記錄
titleSuffix: Azure DNS
description: 本教學課程示範如何設定 Azure DNS 別名記錄，以參考區域內的資源記錄。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: 2b122a34cfd382a58f7680743d3a1cb1ae598fd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "76939256"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>教學課程：建立別名記錄以參考區域資源記錄

別名記錄可以參考其他相同類型的記錄集。 例如，DNS CNAME 記錄集可以是相同類型的另一個 CNAME 記錄集所用的別名。 如果想要在行為方面將一些記錄集設為別名，有些設為非別名，這項功能相當有用。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在區域中建立資源記錄的別名記錄。
> * 測試別名記錄。


如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites
必須提供可用的網域名稱，如此才能裝載於 Azure DNS 進行測試。 您必須擁有此網域的完整控制權。 完整控制權包括為網域設定名稱伺服器 (NS) 記錄的能力。

如需在 Azure DNS 中裝載網域的相關指示，請參閱[教學課程：在 Azure DNS 上託管您的網域](dns-delegate-domain-azure-dns.md)。


## <a name="create-an-alias-record"></a>建立別名記錄

建立指向區域之中資源記錄的別名記錄。

### <a name="create-the-target-resource-record"></a>建立目標資源記錄
1. 選取您的 Azure DNS 區域以開啟該區域。
2. 選取 [記錄集]  。
3. 在 [名稱]  文字方塊中，輸入 **server**。
4. [類型]  請選取 [A]  。
5. 在 [IP 位址]  文字方塊中，輸入 **10.10.10.10**。
6. 選取 [確定]  。

### <a name="create-the-alias-record"></a>建立別名記錄
1. 選取您的 Azure DNS 區域以開啟該區域。
2. 選取 [記錄集]  。
3. 在 [名稱]  文字方塊中，輸入 **test**。
4. [類型]  請選取 [A]  。
5. 選取 [別名記錄集]  核取方塊中的 [是否]  。 然後選取 [區域記錄集]  選項。
6. 對於 [區域記錄集]  ，選取**伺服器**記錄。
7. 選取 [確定]  。

## <a name="test-the-alias-record"></a>測試別名記錄

1. 啟動您慣用的 nslookup 工具。 其中一個選項是瀏覽至 [https://network-tools.com/nslook](https://network-tools.com/nslook)。
2. 設定 A 記錄的查詢類型，並查閱 **test.\<your domain name\>** 。 答案為 **10.10.10.10**。
3. 在 Azure 入口網站中，將**伺服器** A 記錄變更為 **10.11.11.11**。
4. 等候幾分鐘，然後再次對 **test** 記錄使用 nslookup。 答案為 **10.11.11.11**。

## <a name="clean-up-resources"></a>清除資源

若不再需要本教學課程中建立的資源，請刪除您區域中的 **server** 和 **test** 資源資料。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立用以參考區域內的資源記錄的別名記錄。 若要了解 Azure DNS 和 Web 應用程式，請繼續進行 Web 應用程式的教學課程。

> [!div class="nextstepaction"]
> [在自訂網域中建立 Web 應用程式的 DNS 記錄](./dns-web-sites-custom-domain.md)
