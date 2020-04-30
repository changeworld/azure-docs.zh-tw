---
title: 範本函式
description: 描述要在 Azure 資源管理員範本中用來擷取值、搭配字串和數字使用，並擷取部署資訊的函數。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a15e7dfdf01a99cd23b216fafcfb44320a716d16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231283"
---
# <a name="arm-template-functions"></a>ARM 範本函式

本文說明您可以在 Azure Resource Manager （ARM）範本中使用的所有功能。 如需在您的範本中使用函式的詳細資訊，請參閱[範本語法](template-expressions.md)。

若要建立自己的函式，請參閱[使用者定義的函式](template-syntax.md#functions)。

在部署至資源群組、訂用帳戶、管理群組或租使用者時，大部分函式的運作方式都相同。 有些函式不能用在所有範圍中。 它們會在下列清單中注明。

<a id="array" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-functions"></a>陣列函數

Resource Manager 提供數個函式來處理陣列。

* [array](template-functions-array.md#array)
* [concat](template-functions-array.md#concat)
* [contains](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [empty](template-functions-array.md#empty)
* [頭](template-functions-array.md#first)
* [處](template-functions-array.md#intersection)
* [次](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [分鐘](template-functions-array.md#min)
* [max](template-functions-array.md#max)
* [格或](template-functions-array.md#range)
* [過](template-functions-array.md#skip)
* [採取](template-functions-array.md#take)
* [並](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true" />
<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>比較函式

Resource Manager 提供了幾個用來在範本中進行比較的函式。

* [接合](template-functions-comparison.md#coalesce)
* [equals](template-functions-comparison.md#equals)
* [低](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>日期函式

Resource Manager 提供用於處理日期的下列函數。

* [dateTimeAdd](template-functions-date.md#datetimeadd)
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
* [型](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [並非](template-functions-logical.md#not)
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

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [分鐘](template-functions-numeric.md#min)
* [max](template-functions-numeric.md#max)
* [乘](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true" />

## <a name="object-functions"></a>物件函式

Resource Manager 提供數個函式來處理物件。

* [contains](template-functions-object.md#contains)
* [empty](template-functions-object.md#empty)
* [處](template-functions-object.md#intersection)
* [json](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [並](template-functions-object.md#union)

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

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [名單](template-functions-resource.md#list)
* [都會](template-functions-resource.md#providers)
* [證明](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) -只能用在資源群組的部署中。
* [resourceId](template-functions-resource.md#resourceid) -可以用於任何範圍，但有效的參數會根據範圍而變更。
* [訂](template-functions-resource.md#subscription)用帳戶-只能用在資源群組或訂用帳戶的部署中。
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

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

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [contains](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [empty](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [頭](template-functions-string.md#first)
* [format](template-functions-string.md#format)
* [guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [次](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [replace](template-functions-string.md#replace)
* [過](template-functions-string.md#skip)
* [平分](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [子字串](template-functions-string.md#substring)
* [採取](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [物](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>後續步驟

* 如需 ARM 範本中各區段的說明，請參閱[編寫 arm 範本](template-syntax.md)
* 若要合併多個範本，請參閱 [透過 Azure 資源管理員使用連結的範本](linked-templates.md)
* 若要在建立資源類型時反復執行指定的次數，請參閱[在 Azure Resource Manager 中建立資源的多個實例](copy-resources.md)。
* 若要瞭解如何部署您所建立的範本，請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)
