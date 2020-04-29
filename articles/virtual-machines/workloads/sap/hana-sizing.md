---
title: SAP HANA on Azure (大型執行個體) 的大小調整 | Microsoft Docs
description: SAP HANA on Azure (大型執行個體) 的大小調整。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616900"
---
# <a name="sizing"></a>調整大小

「HANA 大型執行個體」的大小調整與 HANA 的大小調整大致上沒有差別。 針對您想要就現有的和已部署的系統從其他 RDBMS 移到 HANA 的情況，SAP 提供一些可在您現有 SAP 系統上執行的報告。 如果將資料庫移到 HANA，這些報告就會檢查資料並計算 HANA 執行個體的記憶體需求。 若要取得有關如何執行這些報告和取得其最新修補程式或版本的詳細資訊，請參閱下列 SAP 附註：

- [SAP Note #1793345-HANA 上的 SAP 套件大小調整](https://launchpad.support.sap.com/#/notes/1793345)
- [HANA 和 S/4 HANA 大小調整報告上的 SAP Note #1872170 套件](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP 附注 #2121330-常見問題： SAP BW on HANA 大小調整報告](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP 附注 #1736976-HANA 上 BW 的大小調整報告](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP 附注 #2296290-新的 BW on HANA 大小調整報表](https://launchpad.support.sap.com/#/notes/2296290)

針對嶄新的實作，可使用 SAP Quick Sizer 來計算在 HANA 上實作 SAP 軟體時的記憶體需求。

Hana 的記憶體需求會隨著資料磁碟區擴大而增加。 請留意您目前的記憶體耗用量，以利預測其未來的狀況。 根據記憶體需求，您可以接著將您的需求對應至其中一個「HANA 大型執行個體」SKU。

**後續步驟**
- 請參閱[上線需求](hana-onboarding-requirements.md)