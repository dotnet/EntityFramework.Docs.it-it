---
title: Mapping di funzioni-provider di database SQLite-EF Core
description: Mapping di funzioni del provider di database EF Core SQLite
author: bricelam
ms.date: 10/06/2020
uid: core/providers/sqlite/functions
ms.openlocfilehash: 0787981a0c6fa401a7ef4b4c3c2b406f78117ad1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066658"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>Mapping di funzioni del provider di EF Core SQLite

Questa pagina mostra i membri .NET che vengono tradotti in funzioni SQL quando si usa il provider SQLite.

## <a name="binary-functions"></a>Funzioni binarie

.NET                        | SQL                             | Aggiunta in
--------------------------- | ------------------------------- | --------
byte. Contains (valore)       | InStr ( @bytes , char ( @value )) > 0 | EF Core 5.0
byte. Lunghezza                | Lunghezza ( @bytes )                  | EF Core 5.0
byte. SequenceEqual (secondo) | @bytes = @second                | EF Core 5.0

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

.NET                  | SQL                                  | Aggiunta in
--------------------- | ------------------------------------ | --------
-decimalValue         | ef_negate ( @decimalValue )             | EF Core 5.0
decimalValue-d      | ef_add ( @decimalValue , ef_negate ( @d )) | EF Core 5.0
decimalValue * d      | ef_multiply ( @decimalValue , @d )       | EF Core 5.0
decimalValue/d      | ef_divide ( @decimalValue , @d )         | EF Core 5.0
decimalValue% d      | ef_mod ( @decimalValue , @d )            | EF Core 5.0
decimalValue + d      | ef_add ( @decimalValue , @d )            | EF Core 5.0
decimalValue < d      | ef_compare ( @decimalValue , @d ) < 0    | EF Core 5.0
decimalValue <= d     | ef_compare ( @decimalValue , @d ) <= 0   | EF Core 5.0
decimalValue > d      | ef_compare ( @decimalValue , @d ) > 0    | EF Core 5.0
decimalValue >= d     | ef_compare ( @decimalValue , @d ) >= 0   | EF Core 5.0
doubleValue% d       | ef_mod ( @doubleValue , @d )             | EF Core 5.0
floatValue% d        | ef_mod ( @floatValue , @d )              | EF Core 5.0
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
EF. Functions. COLLATE (operando, regole di confronto)                     | @operand COLLATE @collation                            | EF Core 5.0
EF. Functions. like (matchExpression, pattern)                  | @matchExpression COME @pattern
EF. Functions. like (matchExpression, pattern, escapeCharacter) | @matchExpression COME @pattern escape @escapeCharacter
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

## <a name="see-also"></a>Vedere anche

* [Mapping di funzioni spaziali](xref:core/providers/sqlite/spatial#spatial-function-mappings)
