---
title: 範本函式
description: 描述要在 Azure 資源管理員範本中用來擷取值、搭配字串和數字使用，並擷取部署資訊的函數。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: fbd82f89ed9a97a3f376a9ed6eaa8ae3760759ff
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982373"
---
# <a name="arm-template-functions"></a>ARM 樣本功能

本文介紹可在 Azure 資源管理員 (ARM) 範本中使用的所有功能。 有關在範本中使用函數的資訊,請參閱[樣本語法](template-expressions.md)。

若要建立自己的函式，請參閱[使用者定義的函式](template-syntax.md#functions)。

部署到資源組、訂閱、管理組或租戶時,大多數函數的工作方式相同。 一些函數不能在所有作用域中使用。 下面列出了它們。

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>陣列和物件函式

Resource Manager 提供了幾個用來使用陣列和物件的函式。

* [陣列](template-functions-array.md#array)
* [合併](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [包含](template-functions-array.md#contains)
* [建立Array](template-functions-array.md#createarray)
* [空](template-functions-array.md#empty)
* [第一](template-functions-array.md#first)
* [交叉口](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [最後](template-functions-array.md#last)
* [長度](template-functions-array.md#length)
* [分鐘](template-functions-array.md#min)
* [麥克斯](template-functions-array.md#max)
* [range](template-functions-array.md#range)
* [跳](template-functions-array.md#skip)
* [採取](template-functions-array.md#take)
* [聯盟](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>比較函式

Resource Manager 提供了幾個用來在範本中進行比較的函式。

* [equals](template-functions-comparison.md#equals)
* [少](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>日期函式

資源管理員提供以下函數,用於處理日期。

* [日期時間新增](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>部署值函式

資源管理員提供下列函式，以從與部署相關的範本和值的區段中取得值：

* [部署](template-functions-deployment.md#deployment)
* [環境](template-functions-deployment.md#environment)
* [參數](template-functions-deployment.md#parameters)
* [變數](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>邏輯函式

Resource Manager 提供下列函式以使用邏輯條件：

* [和](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [不](template-functions-logical.md#not)
* [或](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>數值函數

資源管理員提供下列函式以使用整數：

* [新增](template-functions-numeric.md#add)
* [複製索引](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [浮動](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [分鐘](template-functions-numeric.md#min)
* [麥克斯](template-functions-numeric.md#max)
* [國防部](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>資源函式

資源管理員提供下列函式以取得資源值：

* [延伸資源 Id](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [清單鍵](template-functions-resource.md#listkeys)
* [清單秘密](template-functions-resource.md#list)
* [清單*](template-functions-resource.md#list)
* [供應商](template-functions-resource.md#providers)
* [參考](template-functions-resource.md#reference)
* [資源組](template-functions-resource.md#resourcegroup)- 只能在部署到資源組時使用。
* [resourceId](template-functions-resource.md#resourceid) - 可在任何作用域使用,但有效參數會根據範圍而變化。
* [訂閱](template-functions-resource.md#subscription)- 只能在部署到資源組或訂閱時使用。
* [訂閱資源 Id](template-functions-resource.md#subscriptionresourceid)
* [租戶資源 Id](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>字串函數

資源管理員提供下列函式以使用字串：

* [基地64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [包含](template-functions-string.md#contains)
* [資料裡](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [空](template-functions-string.md#empty)
* [結束與](template-functions-string.md#endswith)
* [第一](template-functions-string.md#first)
* [格式](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [索引](template-functions-string.md#indexof)
* [最後](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [長度](template-functions-string.md#length)
* [新吉德](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [取代](template-functions-string.md#replace)
* [跳](template-functions-string.md#skip)
* [分裂](template-functions-string.md#split)
* [開始與](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [子](template-functions-string.md#substring)
* [採取](template-functions-string.md#take)
* [到下](template-functions-string.md#tolower)
* [到上](template-functions-string.md#toupper)
* [修剪](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>後續步驟

* 有關 ARM 範本中部分的說明,請參閱創作[ARM 範本](template-syntax.md)
* 若要合併多個範本，請參閱 [透過 Azure 資源管理員使用連結的範本](linked-templates.md)
* 要反覆發代建立資源類型時指定的次數,請參考[在 Azure 資源管理員建立多個資源實例](copy-resources.md)。
* 要檢視如何部署已建立的樣本,請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)
