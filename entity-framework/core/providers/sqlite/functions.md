---
title: Mapping di funzioni-provider di database SQLite-EF Core
description: Mapping di funzioni del provider di database EF Core SQLite
author: bricelam
ms.date: 1/26/2021
uid: core/providers/sqlite/functions
ms.openlocfilehash: 66fb24219465b8c34407d279450b7d501e7a572d
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543211"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>Mapping di funzioni del provider di EF Core SQLite

Questa pagina mostra i membri .NET che vengono tradotti in funzioni SQL quando si usa il provider SQLite.

## <a name="binary-functions"></a>Funzioni binarie

.NET                                           | SQL                                  | Aggiunta in
---------------------------------------------- | ------------------------------------ | --------
byte. Contains (valore)                          | InStr ( @bytes , char ( @value )) > 0      | EF Core 5.0
byte. Lunghezza                                   | Lunghezza ( @bytes )                       | EF Core 5.0
byte. SequenceEqual (secondo)                    | @bytes = @second                     | EF Core 5.0
EF. Functions. Hex (byte)                        | esadecimale ( @bytes )                          | EF Core 6.0
EF. Functions. substr (byte, startIndex)         | substr ( @bytes , @startIndex )          | EF Core 6.0
EF. Functions. substr (byte, startIndex, length) | substr ( @bytes , @startIndex , @length ) | EF Core 6.0

## <a name="conversion-functions"></a>Funzioni di conversione

.NET                      | SQL                           | Aggiunta in
------------------------- | ----------------------------- | --------
boolValue. ToString ()      | CAST ( @boolValue come testo)      | EF Core 6.0
byteValue. ToString ()      | CAST ( @byteValue come testo)      | EF Core 6.0
byte. ToString ()          | CAST ( @bytes come testo)          | EF Core 6.0
charValue. ToString ()      | CAST ( @charValue come testo)      | EF Core 6.0
dateTime. ToString ()       | CAST ( @dateTime come testo)       | EF Core 6.0
dateTimeOffset. ToString () | CAST ( @dateTimeOffset come testo) | EF Core 6.0
decimalValue. ToString ()   | CAST ( @decimalValue come testo)   | EF Core 6.0
doubleValue. ToString ()    | CAST ( @doubleValue come testo)    | EF Core 6.0
floatValue. ToString ()     | CAST ( @floatValue come testo)     | EF Core 6.0
GUID. ToString ()           | CAST ( @guid come testo)           | EF Core 6.0
intValue. ToString ()       | CAST ( @intValue come testo)       | EF Core 6.0
longValue. ToString ()      | CAST ( @longValue come testo)      | EF Core 6.0
sbyteValue. ToString ()     | CAST ( @sbyteValue come testo)     | EF Core 6.0
shortValue. ToString ()     | CAST ( @shortValue come testo)     | EF Core 6.0
timeSpan. ToString ()       | CAST ( @timeSpan come testo)       | EF Core 6.0
uintValue. ToString ()      | CAST ( @uintValue come testo)      | EF Core 6.0
ushortValue. ToString ()    | CAST ( @ushortValue come testo)    | EF Core 6.0

## <a name="date-and-time-functions"></a>Funzioni di data e ora

.NET                            | SQL                                                                      | Aggiunta in
------------------------------- | ------------------------------------------------------------------------ | --------
DateTime.Now                    | DateTime (' Now ',' localtime ')
DateTime. Today                  | DateTime (' Now ',' localtime ',' Start of Day ')
DateTime.UtcNow                 | DateTime (' Now ')
dateTime. AddDays (valore)         | DateTime ( @dateTime , @value \| \| ' Days ')
dateTime. AddHours (valore)        | DateTime ( @dateTime , @d \| \| ' hours ')
dateTime. AddMilliseconds (valore) | DateTime ( @dateTime , ( @value /1000,0) \| \| ' seconds ')                   | EF Core 2.2
dateTime. AddMinutes (valore)      | DateTime ( @dateTime , @value \| \| ' minutes ')
dateTime. AddMonths (mesi)      | DateTime ( @dateTime , @months \| \| ' months ')
dateTime. AddSeconds (valore)      | DateTime ( @dateTime , @value \| \| ' seconds ')
dateTime. AddTicks (valore)        | DateTime ( @dateTime , ( @value /10000000,0) \| \| ' seconds ')               | EF Core 2.2
dateTime. AddYears (valore)        | DateTime ( @dateTime , @value \| \| ' years ')
Data/ora                   | DateTime ( @dateTime ,' Start of Day ')
Data/ora                    | strftime ('% d', @dateTime )
dateTime. DayOfWeek              | strftime ('% w ', @dateTime )                                                | EF Core 2.2
dateTime. DayOfYear              | strftime ('% j ', @dateTime )
dateTime. hour                   | strftime ('% H ', @dateTime )
dateTime. millisecondo            | (strftime ('% f ', @dateTime ) * 1000) %1000
dateTime. minute                 | strftime ('% m', @dateTime )
dateTime. month                  | strftime ('% m', @dateTime )
dateTime. Second                 | strftime ('% s', @dateTime )
dateTime. cicli                  | (julianday ( @dateTime )-julianday (' 0001-01-01 00:00:00')) * 864 miliardi | EF Core 2.2
dateTime. TimeOfDay              | ora ( @dateTime )                                                          | EF Core 3.0
dateTime. Year                   | strftime ('% Y ', @dateTime )

> [!NOTE]
> Alcuni SQL sono stati semplificati a scopo illustrativo. L'oggetto SQL effettivo è più complesso per gestire una gamma più ampia di valori.

## <a name="numeric-functions"></a>Funzioni numeriche

.NET                  | SQL                                   | Aggiunta in
--------------------- | ------------------------------------- | --------
-decimalValue         | ef_negate ( @decimalValue )              | EF Core 5.0
decimalValue-d      | ef_add ( @decimalValue , ef_negate ( @d ))  | EF Core 5.0
decimalValue * d      | ef_multiply ( @decimalValue , @d )        | EF Core 5.0
decimalValue/d      | ef_divide ( @decimalValue , @d )          | EF Core 5.0
decimalValue% d      | ef_mod ( @decimalValue , @d )             | EF Core 5.0
decimalValue + d      | ef_add ( @decimalValue , @d )             | EF Core 5.0
decimalValue < d      | ef_compare ( @decimalValue , @d ) < 0     | EF Core 5.0
decimalValue <= d     | ef_compare ( @decimalValue , @d ) <= 0    | EF Core 5.0
decimalValue > d      | ef_compare ( @decimalValue , @d ) > 0     | EF Core 5.0
decimalValue >= d     | ef_compare ( @decimalValue , @d ) >= 0    | EF Core 5.0
doubleValue% d       | ef_mod ( @doubleValue , @d )              | EF Core 5.0
EF. Functions. Random () | ABS (Random ()/9223372036854780000.0) | EF Core 6.0
floatValue% d        | ef_mod ( @floatValue , @d )               | EF Core 5.0
Math. ABS (valore)       | ABS ( @value )
Math. Max (val1, val2)  | Max ( @val1 , @val2 )
Math. min (val1, val2)  | min ( @val1 , @val2 )
Math. Round (d)         | Round ( @d )
Math. Round (d, cifre) | Round ( @d , @digits )

> [!TIP]
> Le funzioni SQL con prefisso *EF* vengono create da EF core.

## <a name="string-functions"></a>Funzioni per i valori stringa

.NET                                                         | SQL                                                    | Aggiunta in
------------------------------------------------------------ | ------------------------------------------------------ | --------
char. ToLower (c)                                              | inferiore ( @c )                                              | EF Core 6.0
char. ToUpper (c)                                              | Upper ( @c )                                              | EF Core 6.0
EF. Functions. COLLATE (operando, regole di confronto)                     | @operand COLLATE @collation                            | EF Core 5.0
EF. Functions. Glob (matchExpression, pattern)                  | Glob ( @pattern , @matchExpression )                       | EF Core 6.0
EF. Functions. like (matchExpression, pattern)                  | @matchExpression COME @pattern
EF. Functions. like (matchExpression, pattern, escapeCharacter) | @matchExpression COME @pattern escape @escapeCharacter
Regex. IsMatch (input, pattern)                                | RegExp ( @pattern , @input )                               | EF Core 6.0
String. Compare (strA, strB)                                   | CASO in cui @strA = @strB then 0... FINE
stringa. Concat (str0, str1)                                    | @str0 \|\| @str1
stringa. IsNullOrEmpty (valore)                                  | @value è NULL o @value =''
stringa. IsNullOrWhiteSpace (valore)                             | @value IS NULL o Trim ( @value ) =''
stringValue. CompareTo (strB)                                  | CASO in cui @stringValue = @strB then 0... FINE
stringValue. Contains (valore)                                  | InStr ( @stringValue , @value ) > 0
stringValue. EndsWith (valore)                                  | @stringValueLIKE '%' \| \|@value
stringValue. FirstOrDefault ()                                 | substr ( @stringValue ,1, 1)                             | EF Core 5.0
stringValue. IndexOf (valore)                                   | InStr ( @stringValue , @value )-1
stringValue. LastOrDefault ()                                  | substr ( @stringValue , Length ( @stringValue ), 1)          | EF Core 5.0
stringValue. length                                           | Lunghezza ( @stringValue )
stringValue. Replace (oldValue, newValue)                      | Sostituisci ( @stringValue , @oldValue , @newValue )
stringValue. StartsWith (valore)                                | @stringValueLIKE @value \| \| '%'
stringValue. Substring (startIndex, lunghezza)                    | substr ( @stringValue , @startIndex + 1, @length )
stringValue. ToLower ()                                        | inferiore ( @stringValue )
stringValue. ToUpper ()                                        | Upper ( @stringValue )
stringValue. Trim ()                                           | Trim ( @stringValue )
stringValue. Trim (trimChar)                                   | Trim ( @stringValue , @trimChar )
stringValue. tricorregge ()                                        | RTRIM ( @stringValue )
stringValue. trirammend (trimChar)                                | RTRIM ( @stringValue , @trimChar )
stringValue. TrimStart ()                                      | LTRIM ( @stringValue )
stringValue. TrimStart (trimChar)                              | LTRIM ( @stringValue , @trimChar )

> [!NOTE]
> Alcuni SQL sono stati semplificati a scopo illustrativo. L'oggetto SQL effettivo è più complesso per gestire una gamma più ampia di valori.

## <a name="miscellaneous-functions"></a>Funzioni varie

.NET                                     | SQL                                | Aggiunta in
---------------------------------------- | ---------------------------------- | --------
raccolta. Contains (item)                | @item IN @collection               | EF Core 3.0
enumValue. HasFlag (flag)                  | @enumValue & @flag = @flag
Nullable. GetValueOrDefault ()             | COALESCE ( @nullable , 0)
Nullable. GetValueOrDefault (defaultValue) | COALESCE ( @nullable , @defaultValue )

## <a name="see-also"></a>Vedi anche

* [Mapping di funzioni spaziali](xref:core/providers/sqlite/spatial#spatial-function-mappings)
