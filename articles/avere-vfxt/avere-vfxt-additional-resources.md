---
title: 關於 Avere vFXT for Azure 的其他連結
description: 關於 Avere vFXT for Azure 的其他資訊的連結
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76153763"
---
# <a name="additional-documentation"></a>其他文件

本文提供關於 Avere 控制台管理介面及相關主題之其他文件的連結。

## <a name="avere-cluster-documentation"></a>Avere 叢集文件

其他 Avere 叢集文件可以在下列網站上找到：<https://azure.github.io/Avere/>。 這些文件可以協助您了解叢集的功能，以及如何進行其設定。

* [FXT 叢集建立指南](<https://azure.github.io/Avere/#fxt_cluster>)是針對由實體硬體節點所組成的叢集而設計，但文件中的某些資訊也與 vFXT 叢集相關。 特別是，新的 vFXT 叢集系統管理員可以從閱讀以下幾節受益：
  * [自訂支援和監視設定](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>)說明如何自訂支援上傳設定，並啟用遠端監視。
  * [設定 VServers 和全域命名空間](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>)提供建立用戶端面向命名空間的相關資訊。
  * [設定 Avere 叢集的 DNS](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) 說明如何設定循環配置資源 DNS。
  * [新增後端儲存體](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>)檔如何新增核心檔。

* [叢集設定指南](<https://azure.github.io/Avere/#operations>)是 Avere 叢集設定和選項的完整參考。 vFXT 叢集會使用這些選項的子集，但大部分相同的設定頁面都適用。

* [儀表板指南](<https://azure.github.io/Avere/#operations>)說明如何使用 Avere 控制台的叢集監視功能。

## <a name="vfxt-creation-and-management-documentation"></a>vFXT 建立和管理文件

GitHub 上提供使用 vfxt.py （以腳本為基礎的雲端叢集建立和管理公用程式）的完整指南：[使用 vfxt.py 進行雲端叢集管理](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)。
