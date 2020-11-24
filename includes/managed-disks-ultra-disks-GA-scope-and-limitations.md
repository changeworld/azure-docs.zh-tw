---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26a5537496d9e881ece135437c403baf4a4fd67c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553416"
---
目前，ultra 磁片有額外的限制，如下所示：

Ultra 磁片目前唯一可用的基礎結構冗余選項是可用性區域。 使用任何其他冗余選項的 Vm 無法連接 ultra 磁片。

下表列出 ultra 磁片的可用區域，以及其對應的可用性選項：

> [!NOTE]
> 如果下列清單中的區域沒有可支援 ultra 磁片的可用性區域，則該區域中的 Vm 必須在沒有任何基礎結構冗余選項的情況下部署，才能連接 ultra 磁片。

|區域  |冗余選項  |
|---------|---------|
|巴西南部     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|印度中部     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|東亞     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|德國中西部     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|南韓中部     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|美國中南部    |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|US Gov 亞利桑那州     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|US Gov 維吉尼亞州     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|US Gov 德克薩斯州     |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|美國西部     |不支援單一 Vm (可用性設定組和虛擬機器擴展集)         |
|澳大利亞中部    |不支援單一 Vm (可用性設定組和虛擬機器擴展集) |
|澳大利亞東部     |三個可用性區域         |
|東南亞    |三個可用性區域        |
|加拿大中部 *     |三個可用性區域          |
|美國中部     |三個可用性區域          |
|美國東部     |三個可用性區域          |
|美國東部 2     |三個可用性區域         |
|法國中部    |兩個可用性區域        |
|日本東部    |三個可用性區域        |
|北歐    |三個可用性區域        |
|英國南部    |三個可用性區域        |
|西歐    | 三個可用性區域|
|美國西部 2    |三個可用性區域|

\* 請聯絡 Azure 支援，以取得此區域的可用性區域存取權。

- 只有下列 VM 系列支援：
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 並非每個 VM 大小都可在具有 ultra 磁片的每個支援區域中使用。
- 只能作為資料磁片使用。 
- 預設支援4k 實體磁區大小。 512E 磁區大小可作為正式推出的供應專案， (不需要註冊) 但目前僅供使用 CLI 或 powershell 使用。 大部分的應用程式都相容于4k 磁區大小，但有些應用程式需要512個位元組磁區的大小。 其中一個範例是 Oracle Database，這需要版本12.2 或更新版本，才能支援4k 原生磁片。 針對較舊版本的 Oracle DB，需要512個位元組的磁區大小。
- 只能建立為空磁片。
- 目前不支援磁片快照集、VM 映射、可用性設定組、Azure 專用主機或 Azure 磁片加密。
- 目前不支援與 Azure 備份或 Azure Site Recovery 整合。
- 只支援未快取的讀取和未快取的寫入。
- GA Vm 上目前的 IOPS 上限為80000。

Azure ultra 磁片預設會針對每個訂用帳戶在每個區域提供最多16個 TiB，但 ultra 磁片依要求支援更高的容量。 若要要求增加容量，請聯絡 Azure 支援。
