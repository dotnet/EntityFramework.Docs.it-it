---
title: Personalizzazione della tabella di cronologia delle migrazioni-EF6
description: Personalizzazione della tabella di cronologia delle migrazioni in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: a6cd27f39c648d35d2e0238a10f8a6b351cc1220
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618072"
---
# <a name="customizing-the-migrations-history-table"></a>Personalizzazione della tabella di cronologia delle migrazioni
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

> [!NOTE]
> Questo articolo presuppone che l'utente sappia come usare Migrazioni Code First negli scenari di base. In caso contrario, sarà necessario leggere [migrazioni Code First](xref:ef6/modeling/code-first/migrations/index) prima di continuare.

## <a name="what-is-migrations-history-table"></a>Che cos'è la tabella di cronologia delle migrazioni?

Tabella di cronologia migrazioni è una tabella utilizzata da Migrazioni Code First per archiviare i dettagli sulle migrazioni applicate al database. Per impostazione predefinita, il nome della tabella nel database è \_ \_ MigrationHistory e viene creato quando si applica la prima migrazione al database. In Entity Framework 5 questa tabella è una tabella di sistema se l'applicazione utilizza il database di Microsoft SQL Server. Questa operazione è stata modificata in Entity Framework 6, ma la tabella di cronologia delle migrazioni non è più contrassegnata come tabella di sistema.

## <a name="why-customize-migrations-history-table"></a>Perché personalizzare la tabella di cronologia delle migrazioni?

La tabella di cronologia delle migrazioni dovrebbe essere utilizzata esclusivamente da Migrazioni Code First e modificarla manualmente può suddividere le migrazioni. Tuttavia, a volte la configurazione predefinita non è adatta e la tabella deve essere personalizzata, ad esempio:

-   Per abilitare un provider di migrazioni<sup>di terze</sup> parti, è necessario modificare i nomi e/o i facet delle colonne
-   Si desidera modificare il nome della tabella
-   È necessario usare uno schema non predefinito per la \_ \_ tabella MigrationHistory
-   È necessario archiviare dati aggiuntivi per una determinata versione del contesto e pertanto è necessario aggiungere una colonna aggiuntiva alla tabella.

## <a name="words-of-precaution"></a>Parole di precauzione

Modificare la tabella di cronologia della migrazione è potente, ma è necessario prestare attenzione a non esagerare. EF Runtime attualmente non controlla se la tabella di cronologia delle migrazioni personalizzate è compatibile con il Runtime. In caso contrario, l'applicazione potrebbe interrompersi in fase di esecuzione o comportarsi in modi imprevedibili. Questa operazione è ancora più importante se si usano più contesti per database, nel qual caso più contesti possono usare la stessa tabella di cronologia della migrazione per archiviare le informazioni sulle migrazioni.

## <a name="how-to-customize-migrations-history-table"></a>Come personalizzare la tabella di cronologia delle migrazioni

Prima di iniziare, è necessario tenere presente che è possibile personalizzare la tabella di cronologia delle migrazioni solo prima di applicare la prima migrazione. A questo punto, al codice.

In primo luogo, sarà necessario creare una classe derivata dalla classe System. Data. Entity. Migrations. History. HistoryContext. La classe HistoryContext è derivata dalla classe DbContext, quindi la configurazione della tabella di cronologia delle migrazioni è molto simile alla configurazione dei modelli EF con l'API Fluent. È sufficiente eseguire l'override del metodo OnModelCreating e usare l'API Fluent per configurare la tabella.

>[!NOTE]
> In genere, quando si configurano i modelli EF non è necessario chiamare base. OnModelCreating () dal metodo OnModelCreating sottoposto a override poiché DbContext. OnModelCreating () ha un corpo vuoto. Questa situazione non si verifica quando si configura la tabella di cronologia delle migrazioni. In questo caso, la prima operazione da eseguire nell'override di OnModelCreating () consiste nell'eseguire effettivamente la chiamata di base. OnModelCreating (). Verrà configurata la tabella di cronologia delle migrazioni con la modalità predefinita, che verrà quindi modificata nel metodo di override.

Si noti che si vuole rinominare la tabella di cronologia delle migrazioni e inserirla in uno schema personalizzato denominato "admin". Inoltre, l'amministratore di database desidera rinominare la colonna MigrationId in ID migrazione \_ . È possibile ottenere questo risultato creando la classe seguente derivata da HistoryContext:

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

Quando il HistoryContext personalizzato è pronto, è necessario fare in modo che EF lo renda conto mediante la registrazione tramite la [configurazione basata su codice](https://msdn.com/data/jj680699):

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

Questo è molto simile. A questo punto è possibile passare alla console di gestione pacchetti, abilitare-Migrations, Add-Migration e infine Update-database. Ciò comporta l'aggiunta al database di una tabella di cronologia delle migrazioni configurata in base ai dettagli specificati nella classe derivata HistoryContext.

![Tabella di cronologia migrazioni](~/ef6/media/database.png)
