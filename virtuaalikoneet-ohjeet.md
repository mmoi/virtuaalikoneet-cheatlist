# Virtuaalikoneet

* VM = Virtual Machine, virtuaalikone

* Host = isäntäkone (jolla VM pyörii)

* Guest = vieraskone (VM joka pyörii isäntäkoneella)

Virtuaalikone tarjoaa eristetyn ympäristön jossa on oma käyttöjärjestelmä ja omat ohjelmat ja asetukset, jotka eivät vaikuta isäntäkoneen vastaaviin.

Virtuaalikone saa osan isäntäkoneen resursseista, kuten osan kiintolevytilasta ja osan muistista, käyttöönsä.

Virtuaalikoneen käyttäminen on kuin käyttäisi oikeaa erillistä tietokonetta, vaikka oikeasti virtuaalikone on vain isäntäkoneen sisällä oleva ohjelma.

Esimerkiksi isäntäkoneen ja vieraskoneiden välille voi luoda verkkoyhteyksiä aivan kuin ne olisivat oikeasti erillisiä verkon kautta toisiinsa yhteydessä olevia tietokoneita.

Virtuaalikoneiden hallinnointiin tarvitaan virtualisointiympäristö. Seuraavassa esitellään tällainen työkalu nimeltä VirtualBox.

# VirtualBox

Oracle VM VirtualBox (https://www.virtualbox.org/) on ilmainen (GPL-lisenssi) virtualisointiohjelma, jonka avulla saa varsin nopeasti luotua ja kloonattua virtuaalikoneita, joissa voi käyttää eri käyttöjärjestelmiä (Linux, Windows, Mac OS X jne...).

VirtualBox ei asenna valmiiksi käyttiksiä, vaan ne pitää asentaa samalla tavalla kuin normaalillekin tietokoneelle niitä asennetaan. Asentamista voi automatisoida Vagrantin avulla, josta ohjeet myöhemmin tässä artikkelissa.

Huom! Kun asennat VirtualBoxia Ubuntun päälle katso ohjeet virtualboxin sivulta (https://www.virtualbox.org/wiki/Linux_Downloads) kohdasta "Debian-based Linux distributions".

VirtualBox tukee virtuaalisia Windows-koneita, mutta ilmaisversio ei tue RDP-yhteyttä, joten Windows-palvelinten virtualisointiin VirtualBoxia ei kannata käyttää (VirtualBoxin maksullinen versio on erittäin kallis). Windows-koneiden virtualisointiin yksi vaihtoehto on Windows-palvelinkäyttöjärjestelmiin ja työasemakäyttisten professional-versioihin sisältyvä Hyper-V. Hyper-V on asennettuna palvelimella Kaniini.

Huom! RDP-yhteydet toimivat VirtualBoxissa ainakin Windows Server 2012 Standardilla oikeilla nettiasetuksilla (Bridged Adapter)!

# Manager (GUI)

Nämä ohjeet ovat graafisen käyttöliittymän hallinnointityökalua varten.

## Virtuaalikoneen luonti

* Lataa ja asenna VirtualBox (https://www.virtualbox.org/wiki/Downloads)
* Käynnistä ohjelma
* Klikkaa "New"
    * Käy läpi virtuaalikoneen luontiohjelma, joka neuvoo millaisilla asetuksilla VM kannattaa luoda (oletusasetukset ovat aika hyvät)
* Uusi VM ilmestyy listaan. Käynnistä se klikkaamalla "Start".
* Asenna käyttöjärjestelmä niin kuin asentaisit mille tahansa koneelle, esim. cd-levyltä buuttaamalla. VM:n toiminta on täysin sille annetun "boxin" sisäistä eikä sillä ole vaikutusta isäntäkoneen toimintaan, joten esimerkiksi käyttiksen asentamisen yhteydessä voi huoletta tyhjentää VM:n kovalevyn, isäntäkoneen kovalevy ei jyräydy.

Huom! Isäntäkoneella on oltava BIOSsissa VT-x enabloituna, jotta virtuaalikoneita voipi ajella!

# VBoxManage (CLI)

https://www.virtualbox.org/manual/ch08.html

Virtuaalikoneiden hallintaan komentoriviltä (jos haluaa hallinnoida palvelinta esim. ssh:n kautta eikä näytön ja näppiksen kautta).

## Monitorointi

Näytä kaikki virtuaalikoneet:

	vboxmanage list vms

Näytä käynnistä olevat virtuaalikoneet:

	vboxmanage list runningvms

Näytä virtuaalikoneen nimeltä "heppa" tiedot:

	vboxmanage showvminfo heppa

## Käynnistys

Käynnistä "heppa" -niminen virtuaalikone (heppa:n pitää olla luotuna). "--type headless" vivulla virtuaalikone käynnistyy taustalle eikä luo ikkunoita isäntäkoneen ikkunointijärjestelmään. (Jos isäntäkoneessa ei ole näyttöä kiinni ei edes käynnisty ilman headless -vipua.)

	vboxmanage startvm heppa --type headless

## Sammutus

Sammuta hallitusti lähettämällä shutdown-käsky:

	vboxmanage controlvm koneen-nimi acpipowerbutton

Pakota sammutus (virtuaalinen pistoke irti seinästä -toimenpide):

	vboxmanage controlvm koneen-nimi poweroff

(Huom. Hallittu sammutus sammutti koneen vasta sammutusviiveen jälkeen jos kone oli käynnistetty ilman "--type headless" optiota, joten kannattaa käyttää sitä koneita käynnisteltäessä...)

## Kloonaus

Tee "heppa" -virtuaalikoneesta klooni nimeltä "noppa":

	vboxmanage clonevm heppa --name noppa

Rekisteröi klooni:

	vboxmanage registervm /home/vm-admin/vms/noppa/noppa.vbox

Koneet pitää aina rekisteröidä jotta ne näkyvät VirtualBoxin listoilla.

Huom! Kloonatessa kannattaa varmistaa että virtuaalisten verkkokorttien MAC-osoitteet muuttuvat, ettei tule samat MAC-osoitteet koneille, mikä voi aiheuttaa ongelmia verkkoyhteyksissä. Komentoriviltä kloonattaessa taitaa tehdä sen oletuksena. GUI-työkalulla täytyy laittaa erillinen täppä päälle.

## Luonti

Luo "heppa" -niminen virtuaalikone:

	vboxmanage createvm --name heppa

Käytännössä luontikomento luo heppa/heppa.vbox -nimisen tiedoston.

Uuden koneen rekisteröinti tapahtuu näin:

	vboxmanage registervm /home/vm-admin/vms/heppa/heppa.vbox

Tämän jälkeen kone näkyy VirtualBox -ohjelmassa.

Huom! Koneiden luomisen jälkeen ne pitää konfiguroida. Se on paljon helpompaa graafisen VirtualBox Managerin ohjattujen toimintojen avulla, joten käytännössä virtuaalikoneita kannattaa kloonata olemassaolevista koneista tai käyttää Vagranttia (ks. ohjeet alla). Jos koneita tarvitsee joskus luoda tyhjästä kannattaa tehdä se graafisen käyttöliittymän eikä komentorivin kautta.

## Tuhoaminen

Hävitä "heppa" -virtuaalikone:

	vboxmanage unregistervm heppa --delete

--delete lippu tuhoaa kaikki virtuaalikoneeseen liittyvät tiedostot ja levykuvat isäntäkoneen levyltä. Käytä sitä jos haluat lopullisesti eroon virtuaalikoneesta ja kaikesta siihen liittyvästä.

= Vagrant =

Vagrant (https://www.vagrantup.com/) on VirtualBoxin päällä pyörivä kehitysympäristö, jonka avulla voi ajaa ns. boxeja, laatikoita, jotka kuvaavat tietyn virtuaalikoneen tietyllä käyttöjärjestelmällä ja ohjelmakokoonpanolla. Boxeja voi ladata netistä valmiina tai luoda itse omaan tarpeeseen sopiva.

Erittäin selkeä ja hyvä Vagrant -tutoriaali: https://docs.vagrantup.com/

Vagrantista on monia hyötyjä, esimerkiksi:

* VM määritellään konfigurointitiedostossa, jonka voi sujauttaa kätevästi versionhallintaan. Virtuaalikoneen asetusten siirto kehitysympäristöjen välillä tällöin helppoa.

* Voidaan määrittää jaettuja hakemistoja (synced folders) isäntä- ja vieraskoneen välille, jolloin esimerkiksi voidaan koodata omalla koneella (isäntäkoneella) ja tiedostot päivittyvät automaattisesti vieraskoneelle määriteltyyn hakemistoon.

* Netistä voi ladata valmiita kokoonpanoja (esim. LAMP, Laravel, Node.js kehitysympäristöt) jolloin välttyy ohjelmien asentelulta ja konffaamiselta.

Käytännössä Vagrant on parhaimmillaan softakehityksessä kehitysympäristöjen virittelyssä. Esimerkiksi webbikehityksessä on kätevää että kehitysympäristö on mahdollisimman samankaltainen tuotantoympäristön kanssa ja halutaan että useilla kehittäjillä on täsmälleen sama ympäristö.

Huom! Vagrant -asennus on oletuksena varsin tietoturvaton! Ks. kohta tietoturva.

# Komennot 

Luo virtuaalikone:

	vagrant init

Luo Vagrantfile -nimisen tiedoston, jota muokkaamalla voi muuttaa VM:n asetuksia.

Luo virtuaalikone valmiiksi määritellyllä kokoonpanolla (tässä tapauksessa Ubuntu 14.04):

	vagrant init ubuntu/trusty64

Valmiita laatikoita eli "boxeja" voi kelailla täältä: https://atlas.hashicorp.com/boxes/search.

Käynnistä virtuaalikone:

	vagrant up

Käynnistää virtuaalikoneen. (Tässä voi ensimmäisellä kerralla kestää kahvitauon verran varsinkin jos käytetään jotain boxia joka täytyy ladata netistä.)

'''Huom!''' Joskus jos virtualbox on päivitetty uudempaan versioon täytyy myös Vagrant päivittää uudempaan tai muuten tulee hämäriä virheilmoituksia tässä vaiheessa eivätkä koneet suostu lähtemään käyntiin.

Avaa SSH -yhteys virtuaalikoneelle:

	vagrant ssh

Ottaa ssh-yhteyden virtuaalipalvelimelle (Windowsissa täytyy olla asennettuna ssh clientti, joka toimii komentoriviltä, esim. Cygwin tai openssh).

Oletuksena uuden virtuaalikoneen käyttäjänimi on "vagrant" ja salasana "vagrant".

# Jaetut kansiot 

Vagrantissa voi luoda oman jaetun kansion laittamalla seuraavan rivin asetustiedostoon (Vagrantfile). Ensimmäinen parametri on polku paikallisen koneen (isäntäkoneen) hakemistoon ja seuraava parametri on polku virtuaalikoneen (vieraskoneen) hakemistoon. Esimerkiksi tässä tapauksessa Apachen oletusjuurihakemisto /var/www/html.

# Vagrantfile
	Vagrant.configure(2) do |config|
   	...
      		config.vm.synced_folder "./testi", "/var/www/html/testi"
   	...
   	end

# Verkkoyhteydet 

Vagrant luo oletuksena ssh-yhteydet vieraskoneelle. Vagrant käyttää oletuksena VirtualBoxin NAT-asetusta verkkoyhteyksien muodostamiseen.

Jos halutaan saada TCP-yhteys (esimerkiksi HTTP-palvelinta varten, vaikkapa jos halutaan käyttää vieraskoneella olevia webbisoftia isäntäkoneen selaimella) täytyy VirtualBoxiin konffata porttivälitys (port forwarding) TCP-yhteyttä varten. Sen voi tehdä esimerkiksi näin:

## Porttivälitys

Lisää Vagrantfile:en seuraava rivi:

	config.vm.network :forwarded_port, guest: 80, host: 8888

Tämän jälkeen käynnistä VM uudelleen käskyllä:

	vagrant reload

Kun VM on taas pystyssä mene isäntäkoneen selaimella osoitteeseen 127.0.0.1:8888. Siellä pitäisi näkyä virtuaalikoneen palvelimen antama vastaus. Testaa vieraskoneella että sen HTTP-palvelin pyörii esim. seuraavalla käskyllä:

	curl 127.0.0.1:80

## Staattinen IP

Seuraavalla rimpsulla Vagrantfilessä saa vieraskoneen näkymään verkossa omalla ip-osoitteellaan:

	config.vm.network "public_network", ip: "195.156.114.109"

*Anna sellainen IP-numero jota ei ole vielä varattu minkään muun koneen käyttöön.
*Porttivälitys ei toimi jos ip-osoite on määritelty näin (eikä sitä kyllä tarvitakaan)

Huom! Kun koneen IP:n määrittelee edellä olevalla tavalla kone näkyy Intrinsicin sisäverkossa muille koneille mutta sisäverkon ulkopuolelle kone näkyy isäntäkoneen IP:llä. Jos halutaan kone näkymään ulkopuolelle omalla osoitteellaan (esim. Pacius) sain sen näkymään ainakin näin:

*Virtualboxin verkkoasetuksista valitaan vaihtoehto "Bridged"
*Koneen sisäiset verkkoasetukset määritellään käsin (IP, aliverkon peite, oletusyhdyskäytävä ja DNS-palvelimet)

# Kloonaaminen 

Luo paketti komennolla:

	vagrant package

Tämä luo package.box -nimisen tiedoston. Jos haluat kloonata koneen jollekin muulle isäntäkoneelle niin siirrä tässä vaiheessa package.box -tiedosto sille koneelle.

Liitä paketti boksiksi:

	vagrant box add omaboksi package.box

Alusta uusi virtuaalikone komennolla (mene ensin uuteen hakemistoon):

	vagrant init omaboksi

Tällä tavalla saa kopioitua virtuaalikoneen koko sisällön asennuksineen, asetuksineen ja tietosisältöineen! :D

Isäntäkoneelle asennetut boxit näkee näin:

	vagrant box list

# Ratkaisuja ongelmiin 

## Virtuaalikoneet eivät käynnisty

Jos virtuaalikoneet eivät pyöri, laita BIOS:sista päälle VT-x ja VT-d tuet. T460p -läppärillä ne löytyvät kohdasta Config -> Virtualisation.

## Guest additions do not match

Jos VM antaa tällaista virhettä:
	Checking for guest additions in VM...
	default: The guest additions on this VM do not match the installed version of
	default: VirtualBox! In most cases this is fine, but in rare cases it can
	default: prevent things such as shared folders from working properly. If you see
	default: shared folder errors, please make sure the guest additions within the
	default: virtual machine match the version of VirtualBox you have installed on
	default: your host and reload your VM.

Asenna plugin vagrant-vbguest.

Lisäohjeita: http://kvz.io/blog/2013/01/16/vagrant-tip-keep-virtualbox-guest-additions-in-sync/

## MachineWrap ongelma

Jos tulee seuraavanlaista virheilmoitusta niin VirtualBoxin uudelleenasennuksen pitäisi auttaa. VirtualBoxin ja Vagrantin välillä on ilmeisesti tapahtunut jotain...

	There was an error while executing `VBoxManage`, a CLI used by Vagrant
	for controlling VirtualBox. The command and stderr is shown below.
	   
	Command: ["e4794f74-f830-40b1-8bc8-2ab889961281", "--type", "headless"]("startvm",)
	   
	Stderr: VBoxManage.exe: error: The virtual machine 'wpscot_default_1486540637575_79092' has terminated unexpectedly
	during startup with exit code 1 (0x1).  More details may be available in 'C:\VirtualBox VMs\machine\Logs\VBoxHardening.log'
	VBoxManage.exe: error: Details: code E_FAIL (0x80004005), component MachineWrap, interface IMachine

# Tietoturva 

Viritä Vagrantin päällä pyörivää virtuaalikonetta turvallisemmaksi näillä toimenpiteillä:

*Vagrant-virtuaalikoneella on automaattisesti käyttäjä "vagrant" jolla salasana "vagrant". Tämä salasana pitää muuttaa vaikeammin arvattavaksi (ja käyttäjänimikin olisi hyvä vaihtaa).
*Jaetut kansiot ovat tietoturvariski, joten jos niitä ei välttämättä tarvita, ne tulee olla pois päältä.

Ylipäänsä Vagranttia kannattaisi käyttää vain softakehitykseen, ei tuotantoympäristönä eikä jatkuvasti päällä olevana ympäristönä.
