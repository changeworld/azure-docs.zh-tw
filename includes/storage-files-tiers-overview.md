---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 034f5c487750600910cb32f73bcc6ea243b3ec6f
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563421"
---
Azure 檔案儲存體提供了四種不同的儲存體，分別是進階、交易最佳化、經常性存取和非經常性存取，使您可以根據案例的效能和價格需求來量身打造檔案共用：

- **進階**：進階檔案共用是由固態硬碟 (SSD) 所支援，且會以 **FileStorage 儲存體帳戶**類型進行部署。 進階檔案共用可提供持續高效能和低延遲，能在毫秒內處理大部分的 IO 作業，適用於 IO 密集型工作負載。 進階檔案共用適合各種不同的工作負載，例如資料庫、網站託管以及開發環境等等。 進階檔案共用可以同時與伺服器訊息 (SMB) 和網路檔案系統 (NFS) 通訊協定搭配使用。
- **交易最佳化**：交易最佳化檔案共用可讓交易繁重的工作負載不需要進階檔案共用所提供的延遲功能。 交易最佳化檔案共用會在硬碟 (HDD) 所支援的標準儲存硬體上提供，並且會以**一般用途版本 2 (GPv2) 儲存體帳戶**類型進行部署。 交易最佳化原本稱為「標準」層，不過這是指儲存體媒體類型，而不是存取層本身 (經常性存取和非經常性存取也是「標準」層，因其位於標準儲存硬體上)。
- **經常性**：經常性檔案共用可針對一般用途的檔案共用案例 (例如小組共用和 Azure 檔案同步) 提供儲存體最佳化。經常性檔案共用會在 HDD 所支援的標準儲存硬體上提供，並且會以**一般用途版本 2 (GPv2) 儲存體帳戶**類型進行部署。
- **非經常性**：非經常性檔案共用可針對線上封存儲存案例提供符合成本效益的儲存體最佳化。 Azure 檔案同步也可能適合變換較小的工作負載。 非經常性檔案共用會在 HDD 所支援的標準儲存硬體上提供，並且會以**一般用途版本 2 (GPv2) 儲存體帳戶**類型進行部署。

進階檔案共用僅適用於佈建計費模型。 如需進階檔案共用佈建計費模型的詳細資訊，請參閱[了解如何佈建進階檔案共用](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)。 標準檔案共用 (包括交易最佳化、經常性存取和非經常性存取檔案共用) 可以透過「隨用隨付」計費來取得。

經常性存取和非經常性存取檔案共用適用於所有的 Azure 公用區域。 交易最佳化檔案共用適用於所有 Azure 區域，包括國家/地區雲端區域。

若要部署經常性存取或非經常性存取檔案共用，請參閱[建立經常性存取或非經常性存取檔案共用](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share)。 