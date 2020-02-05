---
title: 帳戶管理-Azure VMware 解決方案（AVS）入口網站
description: 說明如何在 Azure VMware 解決方案（AVS）入口網站上管理帳戶
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025362"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>管理 Azure VMware 解決方案（AVS）入口網站上的帳戶

當您建立您的 AVS 服務時，它會在 AVS 上建立帳戶。 此帳戶會與服務所在的 Azure 訂用帳戶相關聯。 訂用帳戶中具有「擁有者」和「參與者」角色的所有使用者都可以存取 AVS 入口網站。 與 AVS 服務相關聯的 Azure 訂用帳戶識別碼和租使用者識別碼可在 [帳戶] 頁面上找到。

若要在 AVS 入口網站中管理帳戶，請[存取入口網站](access-cloudsimple-portal.md)，然後選取側邊功能表上的 [**帳戶**]。

選取 [**摘要**] 以查看貴公司的 AVS 設定相關資訊。 系統會顯示您的雲端設定目前的容量，包括 AVS 私人雲端的數目、儲存體總計、vSphere 叢集設定、節點數目和計算核心數目。 如果目前的設定不符合您的所有需求，則會包含一個連結來購買其他節點。

## <a name="email-alerts"></a>電子郵件警示

您可以新增任何您想要通知的任何人的電子郵件地址，讓您知道是否已變更 AVS 私人雲端設定。

1. 在 [**其他電子郵件警示**] 區域中，按一下 [**加入新**的]。
2. 輸入電子郵件地址。
3. 按下 Return。  

若要移除專案，請按一下 [ **X**]。

## <a name="avs-operator-access"></a>AVS 操作員存取

操作員存取設定允許支援工程師登入您的 AVS 入口網站，以協助您進行疑難排解。 預設會啟用此設定。 支援工程師在登入您的客戶帳戶時所執行的所有動作都會記錄下來，並可供您在 [**活動** > **Audit** ] 頁面上進行審核。

按一下 [**已啟用 AVS 操作員存取**] 開關，以開啟或關閉存取。
