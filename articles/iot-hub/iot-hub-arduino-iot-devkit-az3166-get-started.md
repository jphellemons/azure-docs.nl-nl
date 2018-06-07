---
title: IoT DevKit naar cloud--IoT DevKit AZ3166 verbinden met Azure IoT Hub | Microsoft Docs
description: Informatie over het instellen en IoT DevKit AZ3166 verbinding met Azure IoT Hub, zodat het gegevens naar het Azure-cloud-platform verzenden kan in deze zelfstudie.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: ce20ae800887d8c9e865ecec46d0cf9a49fb7c3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631622"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Verbinding maken met IoT DevKit AZ3166 Azure IoT Hub in de cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

U kunt de [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) gebruiken om Internet der dingen (IoT) (prototype) te  ontwikkelen die van Microsoft Azure-services gebruikmaken. Het bevat een Arduino compatibel bord met uitgebreide randapparatuur en sensoren, een open source bord pakket en een groeiende [projecten catalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Wat u doet
Verbinding maken met de [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) naar een Azure-IoT-hub die u maakt, kunt u de temperatuur en vochtigheid-gegevens verzamelen van sensoren en de gegevens verzenden naar de IoT-hub.

Heb je nog een DevKit? Probeer de [DevKit simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) of [koop er een](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Wat u leert

* Hoe de IoT DevKit verbinding maakt met een draadloos toegangspunt en u uw ontwikkelingsomgeving voorbereiden.
* Het maken van een IoT-hub en een apparaat registreren voor de IoT MXChip DevKit.
* Hoe je sensorgegevens kan verzamelen door het uitvoeren van een voorbeeld toepassing op de MXChip IoT DevKit.
* Hoe de sensorgegevens verzendt naar uw IoT-hub.

## <a name="what-you-need"></a>Wat u nodig hebt

* Een MXChip IoT DevKit moederbord met een Micro USB-kabel. [Download nu](https://aka.ms/iot-devkit-purchase).
* Een computer met Windows 10- of Mac OS 10.10 +.
* Een actief Azure-abonnement. [Activeren van een gratis 30-daagse evaluatieversie Microsoft Azure-account](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Bereid uw hardware voor

De hardware op de computer koppelen.

U heeft deze hardware nodig:

* DevKit mededelingenbord
* Micro USB-kabel

![Vereiste hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Om de DevKit op uw computer aan te sluiten:

1. Verbind de USB-verbinding met uw computer.
2. Verbind de Micro-USB-verbinding met de DevKit.
3. De groene LED voor power bevestigt de verbinding.

![Hardwareverbindingen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Wi-Fi configureren

IoT-projecten zijn afhankelijk van verbinding met internet. Gebruik de volgende instructies voor het configureren van de DevKit verbinding maken met Wi-Fi.

### <a name="enter-ap-mode"></a>Accesspoint modus

Houd B ingedrukt. Druk op de reset knop en laat deze los, laat daarna B los. Uw DevKit komt in de AP-modus voor het configureren van Wi-Fi. Het scherm geeft de serviceset-id (SSID) van de DevKit weer en het portal IP-adres.

![Opnieuw instellen van de knop, knop B en SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Verbinding maken met DevKit AP

Gebruik een ander apparaat dat geschikt is voor een Wi-Fi verbinding (computer of mobiele telefoon). Maak nu verbinding met de DevKit SSID (gemarkeerd in de vorige afbeelding). Laat het wachtwoord leeg.

![Netwerkgegevens- en knop verbinding maken](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Wi-Fi configureren voor de DevKit

Open de IP-adres op het scherm DevKit op uw computer of mobiele telefoon browser weergegeven, selecteert u het Wi-Fi-netwerk dat u wilt dat de DevKit verbinding maken met en typ het wachtwoord. Selecteer **Verbinden**.

![Wachtwoordvak en de knop verbinding maken](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Als de verbinding is geslaagd, wordt de DevKit in enkele seconden opnieuw opgestart. Vervolgens ziet u de naam Wi-Fi- en IP-adres op het scherm:

![Naam Wi-Fi- en IP-adres](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> Het IP-adres weergegeven in de foto mogelijk niet overeen met het werkelijke IP-adres toegewezen en weergegeven op het scherm DevKit. Dit is normaal, omdat het Wi-Fi IP-adressen dynamisch toewijzen via DHCP.

Na de configuratie van Wi-Fi bewaard uw referenties op het apparaat voor die verbinding, zelfs als het apparaat losgekoppeld wordt. Bijvoorbeeld, als u de DevKit voor Wi-Fi thuis configureren en vervolgens de DevKit met het kantoor nemen, moet u AP-modus (te beginnen bij de stap in de sectie 'Voer Azië modus') om de DevKit verbinding met uw kantoor Wi-Fi te configureren. 

## <a name="start-using-the-devkit"></a>Aan de slag met de DevKit

De standaard-app uitgevoerd op de DevKit controleert de meest recente versie van de firmware en sommige sensorgegevens diagnose wordt voor u.

### <a name="upgrade-to-the-latest-firmware"></a>Een upgrade uitvoert naar de meest recente firmware

> [!NOTE] 
> Sinds v1.1 kunnen DevKit ST-SAFE in bootloader. U moet firmware bijwerken als u om u dit waarschijnlijk onder v1.1 uitvoert.

Als u een upgrade nodig, wordt het scherm de huidige en de meest recente firmware-versies weergegeven. Als u wilt upgraden, volgt u de [firmware bijwerken](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) handleiding.

![Weergave van de huidige en de meest recente firmware-versies](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Dit is een eenmalige inspanning. Nadat u op de DevKit voor de ontwikkeling en uw app uploaden, wordt de meest recente firmware wordt geleverd met uw app.

### <a name="test-various-sensors"></a>Verschillende sensoren testen

Druk op de knop B sensoren testen. Indrukken en loslaten van de knop B om te bladeren naar elke sensor blijven.

![Knop B en sensor weergeven](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Nu is het tijd om de ontwikkelomgeving instellen: hulpprogramma's en pakketten voor u geweldige IoT-toepassingen te bouwen. U kunt Windows of Mac OS-versie van de basis van uw besturingssysteem.

### <a name="windows"></a>Windows

We raden u aan het gebruik van het installatiepakket voor het voorbereiden van de ontwikkelomgeving. Als u problemen ondervindt, kunt u de [handmatige stappen](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) om te werken.

#### <a name="download-the-latest-package"></a>Download de meest recente pakket

Het ZIP-bestand dat u hebt gedownload bevat alle benodigde hulpprogramma's en pakketten voor DevKit-ontwikkeling.

> [!div class="button"]
[Downloaden](https://aka.ms/devkit/prod/installpackage/latest)

Het ZIP-bestand bevat de volgende hulpprogramma's en pakketten. Als u al een aantal onderdelen geïnstalleerd, wordt het script detecteren en ze overslaan.

* Node.js en Yarn: Runtime voor de setup-script en geautomatiseerde taken.
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): platformoverschrijdende opdrachtregel ervaring voor het beheren van Azure-resources. Het MSI-bestand bevat afhankelijke Python en pip.
* [Visual Studio Code](https://code.visualstudio.com/) (VS-Code): Lightweight code-editor voor DevKit-ontwikkeling.
* [Visual Studio Code-uitbreiding voor Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): extensie waarmee Arduino-ontwikkeling in Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): hulpprogramma dat afhankelijk is van de extensie voor Arduino.
* DevKit mededelingenbord pakket: Hulpprogramma ketens, bibliotheken en projecten voor de DevKit.
* ST koppeling hulpprogramma: Essentiële hulpprogramma's en stuurprogramma's.

#### <a name="run-the-installation-script"></a>Voer het script voor installatie

In Windows Verkenner, zoek het ZIP-bestand en pakt. Zoeken naar `install.cmd`, met de rechtermuisknop en selecteer **als administrator uitvoeren**.

![Windows Verkenner](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Tijdens de installatie ziet u de voortgang van elk hulpprogramma of het pakket.

![Installatievoortgang](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> Afhankelijk van uw omgeving ontvangt soms u een fout bij het installeren van Arduino IDE. In dit geval kunt u proberen [Arduino IDE afzonderlijk installeren](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) en install.cmd opnieuw uitvoert. Volg anders de [handmatige stappen](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) voor het installeren van alle benodigde hulpprogramma's en pakketten.

#### <a name="install-drivers"></a>Stuurprogramma's installeren

De Code van de VS voor Arduino-extensie is afhankelijk van de Arduino IDE. Als dit de eerste keer dat u de Arduino IDE installeert, kunt u wordt gevraagd om relevante's te installeren:

![ophalen van-slag-stuurprogramma](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Installatie moet ongeveer 10 minuten duren, afhankelijk van de snelheid van uw internet. Nadat de installatie voltooid is, ziet u Visual Studio Code en Arduino IDE snelkoppelingen op het bureaublad.

> [!NOTE] 
> In sommige gevallen terwijl u Code van de VS, wordt u gevraagd met een fout dat geen met de Arduino IDE of het gerelateerde mededelingenbord pakket gevonden. Los deze, VS Code sluiten en opnieuw starten van de Arduino IDE. VS Code moet vervolgens Ga naar het pad Arduino IDE.

### <a name="macos"></a>macOS

We raden u aan het gebruik van één muisklik installatie-ervaring voor het voorbereiden van de ontwikkelomgeving. Als u problemen ondervindt, kunt u de [handmatige stappen](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) om te werken.

#### <a name="install-homebrew"></a>Homebrew installeren

> [!NOTE] 
> Als u Homebrew hebt geïnstalleerd, kunt u deze stap overslaan.

Ga als volgt de [Homebrew installatie-instructies](https://docs.brew.sh/Installation.html) om deze te installeren.

#### <a name="download-the-latest-package"></a>Download de meest recente pakket
Het ZIP-bestand dat u hebt gedownload bevat alle benodigde hulpprogramma's en pakketten voor DevKit-ontwikkeling.

> [!div class="button"]
[Downloaden](https://aka.ms/devkit/prod/installpackage/mac/latest)

Het ZIP-bestand bevat de volgende hulpprogramma's en pakketten. Als u al een aantal onderdelen geïnstalleerd, wordt het script detecteren en ze overslaan.

* Node.js en Yarn: Runtime voor de setup-script en geautomatiseerde taken.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): platformoverschrijdende opdrachtregel ervaring voor het beheren van Azure-resources.
* [Visual Studio Code](https://code.visualstudio.com/) (VS-Code): Lightweight code-editor voor DevKit-ontwikkeling.
* [Visual Studio Code-uitbreiding voor Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): extensie waarmee Arduino-ontwikkeling in Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): hulpprogramma dat afhankelijk is van de extensie voor Arduino.
* DevKit mededelingenbord pakket: Hulpprogramma ketens, bibliotheken en projecten voor de DevKit.
* ST koppeling hulpprogramma: Essentiële hulpprogramma's en stuurprogramma's.

#### <a name="run-the-installation-script"></a>Voer het script voor installatie

In de zoekfunctie zoek naar de .zip en pakt:

![Mac OS zoeken](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Start Terminal app, Ga naar de map die u ZIP-bestand uitpakken en uitvoeren:

```bash
./install.sh
```

![Mac OS installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Als u voldoet aan de Machtigingsfout Homebrew, voert u `brew doctor` downloaden vast. Controleer [Veelgestelde vragen over](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) voor meer informatie.

U hebt nu alle benodigde hulpprogramma's en pakketten die zijn geïnstalleerd voor Mac OS.


## <a name="open-the-project-folder"></a>Open de projectmap

### <a name="start-vs-code"></a>VS Code starten

Zorg ervoor dat uw DevKit niet is verbonden. VS Code eerst te starten en de DevKit aansluiten op uw computer. VS-Code wordt automatisch de DevKit vindt en opent een pagina Inleiding:

![Introductiepagina](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> In sommige gevallen terwijl u Code van de VS, wordt u gevraagd met een fout dat geen met de Arduino IDE of het gerelateerde mededelingenbord pakket gevonden. VS Code sluiten en opnieuw starten van de Arduino IDE. VS Code moet vervolgens Ga naar het pad Arduino IDE.


### <a name="open-the-arduino-examples-folder"></a>Open de map Arduino-voorbeelden

Op de **Arduino voorbeelden** tabblad, blader naar **voorbeelden voor MXCHIP AZ3166** > **AzureIoT**, en selecteer **GetStarted**.

![Tabblad Arduino-voorbeelden](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Als u per ongeluk het venster sluit, kunt u deze opnieuw openen. Gebruik `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) typt u de opdracht om palet te openen, **Arduino**, en zoek en selecteer **Arduino: voorbeelden**.

## <a name="provision-azure-services"></a>Azure-services inrichten

In het oplossingsvenster kunt u uw taak uitvoeren via `Ctrl+P` (Mac OS: `Cmd+P`) door te voeren `task cloud-provision`.

In de terminal VS-Code in leert een interactieve opdrachtregel u de vereiste Azure-services inrichten:

![Interactieve vanaf de opdrachtregel](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Bouwen en de Arduino schema uploaden

### <a name="windows"></a>Windows

1. Gebruik `Ctrl+P` om uit te voeren `task device-upload`.
2. De terminal vraagt u om de configuratie activeren. Om dit te doen, houdt u A, en vervolgens push en release van de knop herstellen. Het scherm wordt weergegeven voor de DevKit-id en 'Configuration'.

Dit is het instellen van de verbindingsreeks die wordt opgehaald uit `task cloud-provision` stap.

VS Code begint vervolgens te controleren en het schema Arduino uploaden:

![Verificatie en uploaden van het schema Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

De DevKit opnieuw wordt opgestart en de code wordt gestart.

> [!NOTE] 
> In sommige gevallen krijgt u de fout ' fout: AZ3166: onbekende pakket '. Dit wordt veroorzaakt door het mededelingenbord package index is niet vernieuwd. Dit selectievakje inschakelt [Veelgestelde vragen over stappen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) voor het oplossen van deze.

### <a name="macos"></a>macOS

1. DevKit in configuratiemodus geplaatst: houd knop A, en vervolgens push en release van de knop herstellen. Het scherm weergegeven 'Configuration'.
2. Gebruik `Cmd+P` om uit te voeren `task device-upload`.

Dit is het instellen van de verbindingsreeks die wordt opgehaald uit `task cloud-provision` stap.

VS Code begint vervolgens te controleren en het schema Arduino uploaden:

![apparaat-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

De DevKit opnieuw wordt opgestart en de code wordt gestart.

> [!NOTE] 
> In sommige gevallen krijgt u de fout ' fout: AZ3166: onbekende pakket '. Dit wordt veroorzaakt door het mededelingenbord package index is niet vernieuwd. Dit selectievakje inschakelt [Veelgestelde vragen over stappen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) voor het oplossen van deze.


## <a name="test-the-project"></a>Het project testen

In de VS Code volgende stappen uit om te openen en het instellen van de seriële Monitor:

1. Klik op de `COM[X]` word op de statusbalk om in te stellen met de juiste COM-poort `STMicroelectronics`: ![com-poort](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Klik op power plug-pictogram op de statusbalk om te openen van de seriële Monitor: ![serieel-monitor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Klik op de statusbalk op het getal dat de Baud-Rate en ingesteld op `115200`: ![baud-rate](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

De voorbeeldtoepassing wordt correct uitgevoerd wanneer u de volgende resultaten ziet:

* De seriële Monitor wordt dezelfde informatie als de inhoud in de onderstaande schermafbeelding.
* De LED MXChip IoT DevKit knippert.

![Uiteindelijke uitvoer in VS-Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, kunt u vinden [Veelgestelde vragen over](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). U kunt ook Geef ons feedback door een opmerking op deze pagina verlaten.

## <a name="next-steps"></a>Volgende stappen

U hebt een MXChip IoT DevKit verbonden met uw IoT-hub en u de vastgelegde sensorgegevens naar uw IoT-hub hebt verzonden.

Zie om door te gaan aan de slag met Azure IoT Hub en om te verkennen andere IoT-scenario's:

- [Berichten op cloudapparaten beheren met iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [IoT Hub-berichten opslaan in Azure-gegevensopslag](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Power BI gebruiken voor het visualiseren van realtime-sensorgegevens uit Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Web-Apps gebruiken voor het visualiseren van realtime-sensorgegevens uit Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Weer voorspellen met behulp van de sensorgegevens uit uw IoT-hub in Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Apparaatbeheer met iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Externe bewaking en meldingen met Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
