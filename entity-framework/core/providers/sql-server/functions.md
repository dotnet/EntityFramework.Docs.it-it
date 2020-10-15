---
title: Mapping di funzioni-provider di database Microsoft SQL Server-EF Core
description: Mapping di funzioni del provider di database Microsoft SQL Server
author: bricelam
ms.date: 10/07/2020
uid: core/providers/sql-server/functions
ms.openlocfilehash: 8eb66521b00f2f4879a098200239849c7219a095
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066670"
---
# <a name="function-mappings-of-the-microsoft-sql-server-provider"></a>Mapping di funzioni del provider di Microsoft SQL Server

Questa pagina mostra i membri .NET che vengono tradotti in funzioni SQL quando si usa il provider di SQL Server.

## <a name="binary-functions"></a>Funzioni binarie

.NET                         | SQL                           | Aggiunta in
---------------------------- | ----------------------------- | --------
byte. Contains (valore)        | CHARINDEX ( @value , @bytes ) > 0 | EF Core 5.0
byte. Lunghezza                 | DataLength ( @bytes )            | EF Core 5.0
byte. SequenceEqual (secondo)  | @bytes = @second              | EF Core 5.0
EF. Functions. DataLength (ARG) | DataLength ( @arg )              | EF Core 5.0

## <a name="conversion-functions"></a>Funzioni di conversione

.NET                      | SQL                                    | Aggiunta in
------------------------- | -------------------------------------- | --------
byte. ToString ()          | CONVERT (varchar (100), @bytes )
byteValue. ToString ()      | CONVERT (varchar (3), @byteValue )
charValue. ToString ()      | CONVERT (varchar (1), @charValue )
Convert. ToBoolean (value)  | CONVERT (bit, @value )                   | EF Core 5.0
Convert. ToByte (valore)     | Converti (tinyint, @value )
Convert. ToDecimal (valore)  | CONVERT (Decimal (18, 2), @value )
Convert. ToDouble (valore)   | CONVERT (float, @value )
Convert. ToInt16 (valore)    | Converti (smallint, @value )
Convert. ToInt32 (valore)    | CONVERT (int, @value )
Convert. ToInt64 (valore)    | CONVERT (bigint, @value )
Convert. ToString (valore)   | CONVERT (nvarchar (max), @value )
dateTime. ToString ()       | CONVERT (varchar (100), @dateTime )
dateTimeOffset. ToString () | CONVERT (varchar (100), @dateTimeOffset )
decimalValue. ToString ()   | CONVERT (varchar (100), @decimalValue )
doubleValue. ToString ()    | CONVERT (varchar (100), @doubleValue )
floatValue. ToString ()     | CONVERT (varchar (100), @floatValue )
GUID. ToString ()           | CONVERT (varchar (36), @guid )
intValue. ToString ()       | CONVERT (varchar (11), @intValue )
longValue. ToString ()      | CONVERT (varchar (20), @longValue )
sbyteValue. ToString ()     | CONVERT (varchar (4), @sbyteValue )
shortValue. ToString ()     | CONVERT (varchar (6), @shortValue )
timeSpan. ToString ()       | CONVERT (varchar (100), @timeSpan )
uintValue. ToString ()      | CONVERT (varchar (10), @uintValue )
ulongValue. ToString ()     | CONVERT (varchar (19), @ulongValue )
ushortValue. ToString ()    | CONVERT (varchar (5), @ushortValue )

## <a name="date-and-time-functions"></a>Funzioni di data e ora

.NET                                                        | SQL                                                  | Aggiunta in
----------------------------------------------------------- | ---------------------------------------------------- | --------
DateTime.Now                                                | GETDATE ()
DateTime. Today                                              | CONVERT (date, GETDATE ())
DateTime.UtcNow                                             | GETUTCDATE ()
dateTime. AddDays (valore)                                     | DATEADD (giorno, @value , @dateTime )
dateTime. AddHours (valore)                                    | DATEADD (ora, @value , @dateTime )
dateTime. AddMilliseconds (valore)                             | DATEADD (millisecondi, @value , @dateTime )
dateTime. AddMinutes (valore)                                  | DATEADD (minuto, @value , @dateTime )
dateTime. AddMonths (mesi)                                  | DATEADD (mese, @months , @dateTime )
dateTime. AddSeconds (valore)                                  | DATEADD (Second, @value , @dateTime )
dateTime. AddYears (valore)                                    | DATEADD (anno, @value , @dateTime )
Data/ora                                               | CONVERT (date, @dateTime )
Data/ora                                                | DATEPART (giorno, @dateTime )
dateTime. DayOfYear                                          | DATEPART (DayOfYear @dateTime )
dateTime. hour                                               | DATEPART (ora, @dateTime )
dateTime. millisecondo                                        | DATEPART (millisecondi @dateTime )
dateTime. minute                                             | DATEPART (minuto, @dateTime )
dateTime. month                                              | DATEPART (month, @dateTime )
dateTime. Second                                             | DATEPART (Second, @dateTime )
dateTime. TimeOfDay                                          | Converti (ora, @dateTime )                              | EF Core 2.2
dateTime. Year                                               | DATEPART (Year, @dateTime )
DateTimeOffset.Now                                          | SYSDATETIMEOFFSET ()
DateTimeOffset. UtcNow                                       | SYSUTCDATETIME ()
dateTimeOffset. AddDays (giorni)                                | DATEADD (giorno, @days , @dateTimeOffset )
dateTimeOffset. AddHours (ore)                              | DATEADD (ora, @hours , @dateTimeOffset )
dateTimeOffset. AddMilliseconds (millisecondi)                | DATEADD (millisecondi, @milliseconds , @dateTimeOffset )
dateTimeOffset. AddMinutes (minuti)                          | DATEADD (minuto, @minutes , @dateTimeOffset )
dateTimeOffset. AddMonths (mesi)                            | DATEADD (mese, @months , @dateTimeOffset )
dateTimeOffset. AddSeconds (secondi)                          | DATEADD (Second, @seconds , @dateTimeOffset )
dateTimeOffset. AddYears (anni)                              | DATEADD (anno, @years , @dateTimeOffset )
dateTimeOffset. Data                                         | CONVERT (date, @dateTimeOffset )
dateTimeOffset. giorno                                          | DATEPART (giorno, @dateTimeOffset )
dateTimeOffset. DayOfYear                                    | DATEPART (DayOfYear @dateTimeOffset )
dateTimeOffset. hour                                         | DATEPART (ora, @dateTimeOffset )
dateTimeOffset. millisecondo                                  | DATEPART (millisecondi @dateTimeOffset )
dateTimeOffset. minuto                                       | DATEPART (minuto, @dateTimeOffset )
dateTimeOffset. mese                                        | DATEPART (month, @dateTimeOffset )
dateTimeOffset. Second                                       | DATEPART (Second, @dateTimeOffset )
dateTimeOffset. TimeOfDay                                    | Converti (ora, @dateTimeOffset )                        | EF Core 2.2
dateTimeOffset. Year                                         | DATEPART (Year, @dateTimeOffset )
EF. Functions. DateDiffDay (inizio, fine)                        | DATEDIFF (giorno, @start , @end )
EF. Functions. DateDiffHour (inizio, fine)                       | DATEDIFF (ora, @start , @end )
EF. Functions. DateDiffMicrosecond (inizio, fine)                | DATEDIFF (microsecondi, @start , @end )
EF. Functions. DateDiffMillisecond (inizio, fine)                | DATEDIFF (millisecondi, @start , @end )
EF. Functions. DateDiffMinute (inizio, fine)                     | DATEDIFF (minuto, @start , @d2 )
EF. Functions. DateDiffMonth (inizio, fine)                      | DATEDIFF (mese, @start , @end )
EF. Functions. DateDiffNanosecond (inizio, fine)                 | DATEDIFF (nanosecondi, @start , @end )
EF. Functions. DateDiffSecond (inizio, fine)                     | DATEDIFF (secondo, @start , @end )
EF. Functions. DateDiffWeek (inizio, fine)                       | DATEDIFF (settimana, @start , @end )                         | EF Core 5.0
EF. Functions. DateDiffYear (inizio, fine)                       | DATEDIFF (anno, @start , @end )
EF. Functions. DateFromParts (anno, mese, giorno)                | DATEFROMPARTS ( @year , @month , @day )                   | EF Core 5.0
EF. Functions. DateTime2FromParts (anno, mese, giorno,...)      | DATETIME2FROMPARTS ( @year , @month , @day ,...)         | EF Core 5.0
EF. Functions. DateTimeFromParts (anno, mese, giorno,...)       | DATETIMEFROMPARTS ( @year , @month , @day ,...)          | EF Core 5.0
EF. Functions. DateTimeOffsetFromParts (anno, mese, giorno,...) | DATETIMEOFFSETFROMPARTS ( @year , @month , @day ,...)    | EF Core 5.0
EF. Functions. undate (espressione)                             | Data di scadenza ( @expression )                                  | EF Core 3.0
EF. Functions. SmallDateTimeFromParts (anno, mese, giorno,...)  | SMALLDATETIMEFROMPARTS ( @year , @month , @day ,...)     | EF Core 5.0
EF. Functions. TimeFromParts (hour, minute, Second,...)       | TIMEFROMPARTS ( @hour , @minute , @second ,...)          | EF Core 5.0
timeSpan. hours                                              | DATEPART (ora, @timeSpan )                            | EF Core 5.0
timeSpan. millisecondi                                       | DATEPART (millisecondi @timeSpan )                     | EF Core 5.0
timeSpan. minuti                                            | DATEPART (minuto, @timeSpan )                          | EF Core 5.0
timeSpan. secondi                                            | DATEPART (Second, @timeSpan )                          | EF Core 5.0

## <a name="numeric-functions"></a>Funzioni numeriche

.NET                    | SQL
----------------------- | ---
Math. ABS (valore)         | ABS ( @value )
Math. ARCCOS (d)            | ARCCOS ( @d )
Math. Asin (d)            | ASIN ( @d )
Math. Atan (d)            | ATAN ( @d )
Math. atan2 (y, x)        | ATN2 ( @y , @x )
Math. Ceiling (d)         | CEILING ( @d )
Math. cos (d)             | COS ( @d )
Math. exp (d)             | EXP ( @d )
Math. Floor (d)           | FLOOR ( @d )
Math. log (d)             | LOG ( @d )
Math. log (a, newBase)    | LOG ( @a , @newBase )
Math. Log10 (d)           | LOG10 ( @d )
Math. Pow (x, y)          | POTENZA ( @x , @y )
Math. Round (d)           | ROUND ( @d , 0)
Math. Round (d, decimali) | ROUND ( @d , @decimals )
Math. Sign (valore)        | FIRMA ( @value )
Math. sin (a)             | SIN ( @a )
Math. sqrt (d)            | SQRT ( @d )
Math. Tan (a)             | TAN ( @a )
Math. Truncate (d)        | ROUND ( @d , 0, 1)

## <a name="string-functions"></a>Funzioni per i valori stringa

.NET                                                                    | SQL                                                                    | Aggiunta in
----------------------------------------------------------------------- | ---------------------------------------------------------------------- | --------
EF. Functions. COLLATE (operando, regole di confronto)                                | @operand COLLATE @collation                                            | EF Core 5.0
EF. Functions. Contains (propertyReference, searchCondition)               | CONTAINs ( @propertyReference , @searchCondition )                         | EF Core 2.2
EF. Functions. Contains (propertyReference, searchCondition, languageTerm) | CONTAINs ( @propertyReference , @searchCondition , Language @languageTerm ) | EF Core 2.2
EF. Functions. FreeText (propertyReference, freeText)                      | FREETEXT ( @propertyReference , @freeText )
EF. Functions. FreeText (propertyReference, freeText, languageTerm)        | FREETEXT ( @propertyReference , @freeText , linguaggio @languageTerm )
EF. Functions. like (matchExpression, pattern)                             | @matchExpression COME @pattern
EF. Functions. like (matchExpression, pattern, escapeCharacter)            | @matchExpression COME @pattern escape @escapeCharacter
String. Compare (strA, strB)                                              | CASO in cui @strA = @strB then 0... FINE
stringa. Concat (str0, str1)                                               | @str0 + @str1
stringa. IsNullOrEmpty (valore)                                             | @value IS NULL o @value = n''
stringa. IsNullOrWhiteSpace (valore)                                        | @value IS NULL o LTRIM (RTRIM ( @value )) = n''
stringValue. CompareTo (strB)                                             | CASO in cui @stringValue = @strB then 0... FINE
stringValue. Contains (valore)                                             | @stringValue LIKE N '%' + @value + n'%'
stringValue. EndsWith (valore)                                             | @stringValue LIKE N'%' + @value
stringValue. FirstOrDefault ()                                            | Substring ( @stringValue , 1,1)                                          | EF Core 5.0
stringValue. IndexOf (valore)                                              | CHARINDEX ( @value , @stringValue )-1
stringValue. LastOrDefault ()                                             | Substring ( @stringValue , Len ( @stringValue ), 1)                          | EF Core 5.0
stringValue. length                                                      | LEN ( @stringValue )
stringValue. Replace ( @oldValue , @newValue )                               | SOSTITUISCi ( @stringValue , @oldValue , @newValue )
stringValue. StartsWith (valore)                                           | @stringValue LIKE @value + n'%'
stringValue. Substring (startIndex, lunghezza)                               | Substring ( @stringValue , @startIndex + 1, @length )
stringValue. ToLower ()                                                   | INFERIORE ( @stringValue )
stringValue. ToUpper ()                                                   | UPPER ( @stringValue )
stringValue. Trim ()                                                      | LTRIM (RTRIM ( @stringValue ))
stringValue. tricorregge ()                                                   | RTRIM ( @stringValue )
stringValue. TrimStart ()                                                 | LTRIM ( @stringValue )

## <a name="miscellaneous-functions"></a>Funzioni varie

.NET                                     | SQL                                | Aggiunta in
---------------------------------------- | ---------------------------------- | --------
raccolta. Contains (item)                | @item IN @collection               | EF Core 3.0
enumValue. HasFlag (flag)                  | @enumValue & @flag = @flag
Guid.NewGuid()                           | NEWID()
Nullable. GetValueOrDefault ()             | COALESCE ( @nullable , 0)
Nullable. GetValueOrDefault (defaultValue) | COALESCE ( @nullable , @defaultValue )

> [!NOTE]
> Alcuni SQL sono stati semplificati a scopo illustrativo. L'oggetto SQL effettivo è più complesso per gestire una gamma più ampia di valori.

## <a name="see-also"></a>Vedere anche

* [Mapping di funzioni spaziali](xref:core/providers/sql-server/spatial#spatial-function-mappings)
