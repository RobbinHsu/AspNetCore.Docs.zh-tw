---
title: 建立 .NET 應用程式的 Protobuf 訊息
author: jamesnk
description: 瞭解如何建立 .NET 應用程式的 Protobuf 訊息。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/protobuf
ms.openlocfilehash: f898907e5bae7c67cfca72c70dc8497f36de2622
ms.sourcegitcommit: 111b4e451da2e275fb074cde5d8a84b26a81937d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89040849"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="b33f0-103">建立 .NET 應用程式的 Protobuf 訊息</span><span class="sxs-lookup"><span data-stu-id="b33f0-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="b33f0-104">依 [James 牛頓](https://twitter.com/jamesnk) 和 [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="b33f0-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="b33f0-105">gRPC 會使用 [Protobuf](https://developers.google.com/protocol-buffers) 作為其介面定義語言 (IDL) 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="b33f0-106">Protobuf IDL 是一種語言中性格式，用於指定 gRPC 服務所傳送和接收的訊息。</span><span class="sxs-lookup"><span data-stu-id="b33f0-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="b33f0-107">Protobuf 訊息是在 *proto* 檔案中定義。</span><span class="sxs-lookup"><span data-stu-id="b33f0-107">Protobuf messages are defined in *.proto* files.</span></span> <span data-ttu-id="b33f0-108">本檔說明 Protobuf 概念如何對應至 .NET。</span><span class="sxs-lookup"><span data-stu-id="b33f0-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="b33f0-109">Protobuf 訊息</span><span class="sxs-lookup"><span data-stu-id="b33f0-109">Protobuf messages</span></span>

<span data-ttu-id="b33f0-110">訊息是 Protobuf 中的主要資料傳輸物件。</span><span class="sxs-lookup"><span data-stu-id="b33f0-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="b33f0-111">它們在概念上類似于 .NET 類別。</span><span class="sxs-lookup"><span data-stu-id="b33f0-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="b33f0-112">上述訊息定義將三個欄位指定為名稱/值配對。</span><span class="sxs-lookup"><span data-stu-id="b33f0-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="b33f0-113">如同 .NET 類型上的屬性，每個欄位都有名稱和類型。</span><span class="sxs-lookup"><span data-stu-id="b33f0-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="b33f0-114">欄位類型可以是 Protobuf 純量實 [數值型別](#scalar-value-types)，例如 `int32` 或其他訊息。</span><span class="sxs-lookup"><span data-stu-id="b33f0-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="b33f0-115">除了名稱之外，訊息定義中的每個欄位都有唯一的數位。</span><span class="sxs-lookup"><span data-stu-id="b33f0-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="b33f0-116">當訊息序列化為 Protobuf 時，會使用欄位號碼來識別欄位。</span><span class="sxs-lookup"><span data-stu-id="b33f0-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="b33f0-117">將小數位序列化比序列化整個功能變數名稱更快。</span><span class="sxs-lookup"><span data-stu-id="b33f0-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="b33f0-118">因為欄位編號可識別欄位，所以變更時請務必小心。</span><span class="sxs-lookup"><span data-stu-id="b33f0-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="b33f0-119">如需變更 Protobuf 訊息的詳細資訊，請參閱 <xref:grpc/versioning> 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="b33f0-120">建立應用程式時，Protobuf 工具會從 *proto* 檔案產生 .net 類型。</span><span class="sxs-lookup"><span data-stu-id="b33f0-120">When an app is built the Protobuf tooling generates .NET types from *.proto* files.</span></span> <span data-ttu-id="b33f0-121">此 `Person` 訊息會產生 .net 類別：</span><span class="sxs-lookup"><span data-stu-id="b33f0-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="b33f0-122">如需 Protobuf 訊息的詳細資訊，請參閱 [Protobuf 語言指南](https://developers.google.com/protocol-buffers/docs/proto3#simple)。</span><span class="sxs-lookup"><span data-stu-id="b33f0-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="b33f0-123">純量數值型別</span><span class="sxs-lookup"><span data-stu-id="b33f0-123">Scalar Value Types</span></span>

<span data-ttu-id="b33f0-124">Protobuf 支援一系列原生純量實數值型別。</span><span class="sxs-lookup"><span data-stu-id="b33f0-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="b33f0-125">下表列出它們都具有對等的 c # 型別：</span><span class="sxs-lookup"><span data-stu-id="b33f0-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="b33f0-126">Protobuf 類型</span><span class="sxs-lookup"><span data-stu-id="b33f0-126">Protobuf type</span></span> | <span data-ttu-id="b33f0-127">C# 類型</span><span class="sxs-lookup"><span data-stu-id="b33f0-127">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

### <a name="dates-and-times"></a><span data-ttu-id="b33f0-128">日期和時間</span><span class="sxs-lookup"><span data-stu-id="b33f0-128">Dates and times</span></span>

<span data-ttu-id="b33f0-129">原生純量類型不提供日期和時間值，相當於。NET 的 <xref:System.DateTimeOffset> 、 <xref:System.DateTime> 和 <xref:System.TimeSpan> 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="b33f0-130">您可以使用一些 Protobuf 的「已知類型」延伸模組來指定這些類型。</span><span class="sxs-lookup"><span data-stu-id="b33f0-130">These types can be specified by using some of Protobuf's "Well Known Types" extensions.</span></span> <span data-ttu-id="b33f0-131">這些擴充功能可在支援的平臺上，為複雜的欄位類型提供程式碼產生與執行時間支援。</span><span class="sxs-lookup"><span data-stu-id="b33f0-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="b33f0-132">下表顯示日期和時間類型：</span><span class="sxs-lookup"><span data-stu-id="b33f0-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="b33f0-133">.NET 類型</span><span class="sxs-lookup"><span data-stu-id="b33f0-133">.NET type</span></span> | <span data-ttu-id="b33f0-134">Protobuf 知名型別</span><span class="sxs-lookup"><span data-stu-id="b33f0-134">Protobuf well-known type</span></span> |
| ------- | ------------------------ |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime` | `google.protobuf.Timestamp` |
| `TimeSpan` | `google.protobuf.Duration` |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="b33f0-135">C # 類別中產生的屬性不是 .NET 日期和時間類型。</span><span class="sxs-lookup"><span data-stu-id="b33f0-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="b33f0-136">屬性使用 `Timestamp` `Duration` 命名空間中的和類別 `Google.Protobuf.WellKnownTypes` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="b33f0-137">這些類別提供在、和之間轉換的 `DateTimeOffset` 方法 `DateTime` `TimeSpan` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="b33f0-138">此 `Timestamp` 類型適用于 UTC 時間。</span><span class="sxs-lookup"><span data-stu-id="b33f0-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="b33f0-139">`DateTimeOffset` 值的位移一律為零，而且 `DateTime.Kind` 屬性一律為 `DateTimeKind.Utc` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="b33f0-140">可為 Null 的型別</span><span class="sxs-lookup"><span data-stu-id="b33f0-140">Nullable types</span></span>

<span data-ttu-id="b33f0-141">C # 的 Protobuf 程式碼產生會使用原生類型，例如 `int` for `int32` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="b33f0-142">因此，一律會包含這些值，而且不能是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="b33f0-143">對於需要明確的值（ `null` 例如 `int?` 在 c # 程式碼中使用），Protobuf 的「已知型別」會包含編譯成可為 Null 的 c # 型別的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="b33f0-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's "Well Known Types" include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="b33f0-144">若要使用這些檔案，請將其匯入 `wrappers.proto` 您 `.proto` 的檔案，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="b33f0-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="b33f0-145">Protobuf 使用 .NET 可為 null 的型別，例如， `int?` 針對產生的訊息屬性。</span><span class="sxs-lookup"><span data-stu-id="b33f0-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="b33f0-146">下表顯示包裝函式類型的完整清單及其對等的 c # 類型：</span><span class="sxs-lookup"><span data-stu-id="b33f0-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="b33f0-147">C# 類型</span><span class="sxs-lookup"><span data-stu-id="b33f0-147">C# type</span></span>   | <span data-ttu-id="b33f0-148">知名的型別包裝函式</span><span class="sxs-lookup"><span data-stu-id="b33f0-148">Well Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="b33f0-149">小數位數</span><span class="sxs-lookup"><span data-stu-id="b33f0-149">Decimals</span></span>

<span data-ttu-id="b33f0-150">Protobuf 本身並不支援 .NET `decimal` 型別， `double` 而是和 `float` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="b33f0-151">Protobuf 專案中有一項持續的討論，當中有可能會將標準的 decimal 型別新增至已知型別，並提供支援的語言和架構平臺支援。</span><span class="sxs-lookup"><span data-stu-id="b33f0-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the well-known types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="b33f0-152">尚未執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="b33f0-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="b33f0-153">您可以建立訊息定義來代表可在 `decimal` .net 用戶端和伺服器之間進行安全序列化的型別。</span><span class="sxs-lookup"><span data-stu-id="b33f0-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="b33f0-154">但是，其他平臺上的開發人員必須瞭解所使用的格式，並對其執行自己的處理。</span><span class="sxs-lookup"><span data-stu-id="b33f0-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="b33f0-155">建立 Protobuf 的自訂十進位類型</span><span class="sxs-lookup"><span data-stu-id="b33f0-155">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="b33f0-156">`nanos`欄位表示從到的 `0.999_999_999` 值 `-0.999_999_999` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="b33f0-157">例如，此 `decimal` 值會 `1.5m` 表示為 `{ units = 1, nanos = 500_000_000 }` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="b33f0-158">這就是為什麼 `nanos` 此範例中的欄位使用 `sfixed32` 型別，其編碼方式比 `int32` 較大的值更有效率。</span><span class="sxs-lookup"><span data-stu-id="b33f0-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="b33f0-159">如果 `units` 欄位是負數，則 `nanos` 欄位也應為負數。</span><span class="sxs-lookup"><span data-stu-id="b33f0-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="b33f0-160">有多個其他演算法可將 `decimal` 值編碼為位元組字串，但這則訊息比任何這些演算法更容易瞭解。</span><span class="sxs-lookup"><span data-stu-id="b33f0-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="b33f0-161">在不同平臺上，這些值不會受到位元組由大到小或位元組由大到小的影響。</span><span class="sxs-lookup"><span data-stu-id="b33f0-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="b33f0-162">此類型與 BCL 類型之間的轉換 `decimal` 可能會在 c # 中執行，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b33f0-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="b33f0-163">集合</span><span class="sxs-lookup"><span data-stu-id="b33f0-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="b33f0-164">清單</span><span class="sxs-lookup"><span data-stu-id="b33f0-164">Lists</span></span>

<span data-ttu-id="b33f0-165">Protobuf 中的清單是藉由 `repeated` 在欄位上使用 prefix 關鍵字來指定。</span><span class="sxs-lookup"><span data-stu-id="b33f0-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="b33f0-166">下列範例顯示如何建立清單：</span><span class="sxs-lookup"><span data-stu-id="b33f0-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="b33f0-167">在產生的程式碼中， `repeated` 欄位會以 `Google.Protobuf.Collections.RepeatedField<T>` 泛型型別表示。</span><span class="sxs-lookup"><span data-stu-id="b33f0-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="b33f0-168">`RepeatedField<T>` 會實作 <xref:System.Collections.Generic.IList%601>。</span><span class="sxs-lookup"><span data-stu-id="b33f0-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="b33f0-169">因此，您可以使用 LINQ 查詢，或將它轉換成陣列或清單。</span><span class="sxs-lookup"><span data-stu-id="b33f0-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="b33f0-170">`RepeatedField<T>` 屬性沒有公用 setter。</span><span class="sxs-lookup"><span data-stu-id="b33f0-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="b33f0-171">專案應新增至現有的集合。</span><span class="sxs-lookup"><span data-stu-id="b33f0-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="b33f0-172">字典</span><span class="sxs-lookup"><span data-stu-id="b33f0-172">Dictionaries</span></span>

<span data-ttu-id="b33f0-173">.NET <xref:System.Collections.Generic.IDictionary%602> 類型在 Protobuf 中會使用來表示 `map<key_type, value_type>` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="b33f0-174">在產生的 .NET 程式碼中， `map` 欄位會以 `Google.Protobuf.Collections.MapField<TKey, TValue>` 泛型型別表示。</span><span class="sxs-lookup"><span data-stu-id="b33f0-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="b33f0-175">`MapField<TKey, TValue>` 會實作 <xref:System.Collections.Generic.IDictionary%602>。</span><span class="sxs-lookup"><span data-stu-id="b33f0-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="b33f0-176">如同 `repeated` 屬性， `map` 屬性沒有公用 setter。</span><span class="sxs-lookup"><span data-stu-id="b33f0-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="b33f0-177">專案應新增至現有的集合。</span><span class="sxs-lookup"><span data-stu-id="b33f0-177">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="b33f0-178">非結構化和條件式訊息</span><span class="sxs-lookup"><span data-stu-id="b33f0-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="b33f0-179">Protobuf 是合約優先的訊息格式，而且必須在建立應用程式時，于 *proto* 檔案中指定應用程式訊息。</span><span class="sxs-lookup"><span data-stu-id="b33f0-179">Protobuf is a contract-first messaging format, and an apps messages need to be specified in *.proto* files when the app is built.</span></span> <span data-ttu-id="b33f0-180">針對先進的案例，Protobuf 提供語言功能和知名的型別，以支援條件式和未知的訊息。</span><span class="sxs-lookup"><span data-stu-id="b33f0-180">For advanced scenarios, Protobuf offers language features and well known types to support conditional and unknown messages.</span></span>

### <a name="any"></a><span data-ttu-id="b33f0-181">任意</span><span class="sxs-lookup"><span data-stu-id="b33f0-181">Any</span></span>

<span data-ttu-id="b33f0-182">`Any`型別可讓您使用訊息做為內嵌類型，而不需要其*proto*定義。</span><span class="sxs-lookup"><span data-stu-id="b33f0-182">The `Any` type lets you use messages as embedded types without having their *.proto* definition.</span></span> <span data-ttu-id="b33f0-183">若要使用 `Any` 類型，請匯入 `any.proto` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-183">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="b33f0-184">Oneof</span><span class="sxs-lookup"><span data-stu-id="b33f0-184">Oneof</span></span>

<span data-ttu-id="b33f0-185">`oneof` 欄位是語言功能。</span><span class="sxs-lookup"><span data-stu-id="b33f0-185">`oneof` fields are a language feature.</span></span> <span data-ttu-id="b33f0-186">編譯器 `oneof` 會在產生訊息類別時處理關鍵字。</span><span class="sxs-lookup"><span data-stu-id="b33f0-186">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="b33f0-187">使用指定可能會傳回 `oneof` 或的回應訊息， `Person` 可能如下 `Error` 所示：</span><span class="sxs-lookup"><span data-stu-id="b33f0-187">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="b33f0-188">集合內的欄位在 `oneof` 整體訊息宣告中必須有唯一的欄位編號。</span><span class="sxs-lookup"><span data-stu-id="b33f0-188">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="b33f0-189">使用時 `oneof` ，產生的 c # 程式碼會包含列舉，以指定已設定的欄位。</span><span class="sxs-lookup"><span data-stu-id="b33f0-189">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="b33f0-190">您可以測試列舉，以找出已設定的欄位。</span><span class="sxs-lookup"><span data-stu-id="b33f0-190">You can test the enum to find which field is set.</span></span> <span data-ttu-id="b33f0-191">未設定的欄位會傳回 `null` 或預設值，而不是擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b33f0-191">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="b33f0-192">值</span><span class="sxs-lookup"><span data-stu-id="b33f0-192">Value</span></span>

<span data-ttu-id="b33f0-193">`Value`型別代表動態類型的值。</span><span class="sxs-lookup"><span data-stu-id="b33f0-193">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="b33f0-194">它可以是 `null` 、數位、字串、布林值、值的字典 (`Struct`) ，或 () 的值清單 `ValueList` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-194">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="b33f0-195">`Value` 是使用先前所討論之功能的知名型別 `oneof` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-195">`Value` is a well known type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="b33f0-196">若要使用 `Value` 類型，請匯入 `struct.proto` 。</span><span class="sxs-lookup"><span data-stu-id="b33f0-196">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="b33f0-197">`Value`直接使用可以是詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="b33f0-197">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="b33f0-198">另一種使用方式 `Value` 是使用 Protobuf 的內建支援，將訊息對應至 JSON。</span><span class="sxs-lookup"><span data-stu-id="b33f0-198">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="b33f0-199">Protobuf 的 `JsonFormatter` 和 `JsonWriter` 類型可以搭配任何 Protobuf 訊息使用。</span><span class="sxs-lookup"><span data-stu-id="b33f0-199">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="b33f0-200">`Value` 尤其適合在 JSON 之間進行轉換。</span><span class="sxs-lookup"><span data-stu-id="b33f0-200">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="b33f0-201">這是先前程式碼的 JSON 對等專案：</span><span class="sxs-lookup"><span data-stu-id="b33f0-201">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="b33f0-202">其他資源</span><span class="sxs-lookup"><span data-stu-id="b33f0-202">Additional resources</span></span>

* [<span data-ttu-id="b33f0-203">Protobuf 語言指南</span><span class="sxs-lookup"><span data-stu-id="b33f0-203">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>