# **GroupGTS - behandelen transport**
## **Descriptie**

Dit project is een UiPath robot dat bepaalde mails uitleest die PDFs of Excel bestanden bezitten. In deze bestanden staat gestructureerde data die uitgelezen wordt om later te verwerken in een XML bestand dat zo verder buiten het proces van de robot gebruikt word.  

## **Waarneming**

Dit project is tot stand gebracht met gebruikmakend van UiPath en het UiPath Robotic Enterprise Framework. Meer informatie over het [Robotic Enterprise Framework](#robotic-enterprise-framework) kan u vinden onderaan de pagina.

* [Initializering](#initializering)
  1. [Aanmaken folders](#stap-1---aanmaken-folders)
  1. [Emails lezen](#stap-2---emails-lezen)
  1. [Verwijderen van folders vorig proces](#stap-3---verwijderen-van-folders-vorig-proces)
* [Toewijzen transactie item](#toewijzen-transactie-item)

## **Uitvoering**

Hieronder zie je de workflow van het proces. Deze workflow is het Robotic Enterprise Framework

![Workflow](Documentation/Images/00_Main.jpg)

### **Initializering**

#### **Stap 1 - Aanmaken folders**

In het begin van het proces wordt er een uniek nummer gemaakt. Dit uniek nummer bestaat uit de combinatie van de start datum en start tijd tot op de second van het proces. Door dit te doen kunnen er bestanden en folders aangemaakt worden om voor huidige proces en kunnen nadien deze bestanden en folder bekeken worden.

In het begin zal er ook nagekeken worden of de feedback folder al bestaat. In deze fodler zal elke feedback van elk proces worden opgeslagen. Het indetificeren van een proces wordt gedaan met gebruikmakende van het unieke nummer.
![Nakijken of feedback folder bestaat](Documentation/Images/01_Init.jpg)
![Nakijken of XML folder bestaat](Documentation/Images/02_Init.jpg)

#### **Stap 2 - Emails lezen**

De workflow voor lezen van emails staat in een retry scope activiteit. Dit zorgt ervoor als er een probleem is tijdens het lezen van de emails zal het proces opnieuw proberen om deze workflow uit te voeren. Het aantal keren van herproberen kan meegegeven worden. Hier wordt er maximaal drie keer opnieuw geprobeert.


![Emails lezen](Documentation/Images/03_Init.jpg)


Vooraleer de emails worden gelezen wordt er een lege lijst aangemaakt voor transactie items. Deze transactie items zijn de emails die behandeld moeten worden.

Verder in het proces wordt in de achtergrond de Outlook geopend. Als het gebruikte email adres nog nooit toegang heeft gegeven aan het project om Outlook te gebruiken wordt er gevraagt om in te loggen. Daarna gebeuren de onderstaande activiteiten op de achtergrond. Tijdens deze activiteiten leest de robot emails in en kijkt na of deze bepaalde filters bezitten. Als er een email is met de jusite filter wordt deze in de lijst gestoken met te behandelende emails.


![Emails lezen](Documentation/Images/04_Init.jpg)

![Filter voor te behandelende emails](Documentation/Images/05_Init.jpg)


Na het inlezen van de emails wordt de lijst met de te behandelende emails teruggegeven naar waar de workflow is opgeroepen. Namelijk in het initializerings proces.

#### **Stap 3 - Verwijderen van folders vorig proces**

Als laatste stap van de initializering wordt de folder waar de bestanden van emails worden gedownload verwijdert. Door dit te doen kan het proces met een propere werkplek starten. Deze bestanden worden verwijderd omdat na het proces van de robot deze bestanden niet meer van toepassing zijn. Het wordt hier opgeroepen omdat het mogelijk is dat het vorige proces gecrasht is en de bestanden niet had kunnen verwijderen.

![Verwijderen van email folder](Documentation/Images/06_Init.jpg)

### **Toewijzen transactie item**

Bij het afhandelen van voorgaande transactie item wordt er een gecontroleerde of er nog een nieuw transactie item is. Als dit is wordt het nieuwe transactie item, of email, doorgegeven naar het volgende proces. Als er geen nieuw transactie item is wordt er een leeg item meegegeven zodat het proces weet dat het zichzelf mag beëindigen.

![Doorgeven nieuw transactie item](Documentation/Images/07_Transaction_Data.jpg)


### **Proces transactie item**

Nadat er een transactie item is meegegeven wordt dit behandeld in het process workflow.

#### **Stap 1 - Eerste run**

Als dit transactie item het eerste is worden er bepaalde variabelen en bestanden aangemaakt.


## **Robotic Enterprise Framework**
### Documentation is included in the Documentation folder ###

[REFrameWork Documentation](https://github.com/UiPath/ReFrameWork/blob/master/Documentation/REFramework%20documentation.pdf)

### REFrameWork Template ###
**Robotic Enterprise Framework**

* Built on top of *Transactional Business Process* template
* Uses *State Machine* layout for the phases of automation project
* Offers high level logging, exception handling and recovery
* Keeps external settings in *Config.xlsx* file and Orchestrator assets
* Pulls credentials from Orchestrator assets and *Windows Credential Manager*
* Gets transaction data from Orchestrator queue and updates back status
* Takes screenshots in case of system exceptions


### How It Works ###

1. **INITIALIZE PROCESS**
 + ./Framework/*InitiAllSettings* - Load configuration data from Config.xlsx file and from assets
 + ./Framework/*GetAppCredential* - Retrieve credentials from Orchestrator assets or local Windows Credential Manager
 + ./Framework/*InitiAllApplications* - Open and login to applications used throughout the process

2. **GET TRANSACTION DATA**
 + ./Framework/*GetTransactionData* - Fetches transactions from an Orchestrator queue defined by Config("OrchestratorQueueName") or any other configured data source

3. **PROCESS TRANSACTION**
 + *Process* - Process trasaction and invoke other workflows related to the process being automated 
 + ./Framework/*SetTransactionStatus* - Updates the status of the processed transaction (Orchestrator transactions by default): Success, Business Rule Exception or System Exception

4. **END PROCESS**
 + ./Framework/*CloseAllApplications* - Logs out and closes applications used throughout the process


### For New Project ###

1. Check the Config.xlsx file and add/customize any required fields and values
2. Implement InitiAllApplications.xaml and CloseAllApplicatoins.xaml workflows, linking them in the Config.xlsx fields
3. Implement GetTransactionData.xaml and SetTransactionStatus.xaml according to the transaction type being used (Orchestrator queues by default)
4. Implement Process.xaml workflow and invoke other workflows related to the process being automated
