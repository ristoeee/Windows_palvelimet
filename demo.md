# Kurssin demo toteutus

## Alkusanat

Koska olen jo jonkin verran käyttänyt Windows-palvelimia aikaisemmissa opinnoissani, päätin suorittaa kurssin demo-toteutuksen. Tiivistettynä demo-toteutus tulee sisältämään seuraavat:

* Virtuaalinen verkko -> pääsy Internettiin
* Template-mallinne serveri
* Domain controller serveri
* File server serveri
* Aktiivihakemiston rakenne ohjeiden mukaan
* Ryhmäkäytännöt ohjeiden mukaan
* DNS palvelu ohjeiden mukaan
* IIS ja FTP
* Työasema, vähintään 1.

Demoa jatketaan kurssin ensimmäisellä luennolla tehdyssä virtuaaliympäristössä (repon 0-2.md raportit). Myöskään tämä `demo.md` raportti ei ole pakollinen demo-toteutuksessa, mutta ajattelin kuitenkin rustata tämän, jotta itselle jää myös jotain muistiinpanoja kurssista. Ensimmäisellä kurssin luennolla tein siis jo harjoitukset 0, 1 ja 2, joten tästä jatketaan ja seuraavaksi siis itse demoon.

## Domain Controller

### Virtuaalikoneen luonti ja perus asetukset

Domain Controlleria (DC01) varten loin uuden virtuaalikoneen, jossa käytin aikaisemmin luotua templatea hyväksi. Nimeksi koneelle annoin DC01, tämän lisäksi luodessa määriteltiin muistin (3072 MB) ja CPU ytimien (4) määrä.

![image](https://github.com/user-attachments/assets/a920c603-61ee-41fe-b5c3-eddca1fdef5b)

Seuraavaksi oli vuorossa DC käynnistys, ja kuten alla olevasta screenshotista ilmenee, template on luotu onnistuneestki, koska se kysyy alkuun perustietoja koneelle.

![image](https://github.com/user-attachments/assets/abdf06f7-1d88-4b7c-b5dd-351f5f6008e1)

Seuraavaksi määritin DC01 koneelle kiinteän IP:n

![image](https://github.com/user-attachments/assets/9aefa619-af1e-49ed-a50e-dafae2c1f9de)

Kiinteästä IP:stä huolimatta virtuaalikoneen verkkoyhteys ei toimi (keltainen huutomerkki verkkoikonin päällä), tutkin ongelmaa ja huomasin, että edellisessä kohdassa DNS serverin määritys jäi tekemättä ja korjasin ongelman:

![image](https://github.com/user-attachments/assets/35af40e4-3515-4486-8dc7-e37b19dd7e42)

Seuraavana oli vuorossa Windowsin aktivointi, jonka tein cmd (Run as Admin):
```
slmgr /skms kms.core.windows.net
slmgr /ato
```

Alla vielä yhteenveto perusasetuksista DC01 koneella

![image](https://github.com/user-attachments/assets/a3900e8a-e7e7-453b-ab91-4b510659439c)


### DC:n roolit

Seuraavana vuorossa oli roolien määritys DC01 palvelimelle. Aloitetaan AD DS-roolin määrityksellä:

![image](https://github.com/user-attachments/assets/c444ab36-38b8-40ba-952e-49934cc96256)

Tämän jälkeen oli vuorossa vielä AD DS määritys Domain Contolleriksi

![image](https://github.com/user-attachments/assets/11478065-f089-4f6a-84cf-ae1f4df82db5)

Asennuksen jälkeen virtuaalikone käynnistyi uudelleen ja määritti asetuksia melko pitkään

![image](https://github.com/user-attachments/assets/ddfcf3e5-ba96-4786-a1b7-6e03239eba79)

Kun vihdoin pääsin takaisin ja Server Manager oli ladannut kaikki muutokset, voidaan todeta, että AD DS sekä DNS ovat asetettu

![image](https://github.com/user-attachments/assets/43ab49fb-3fac-4f36-a644-d9f65b36acad)

## File Server

### Virtuaalikoneen luonti ja perusasetukset

Tämän kohdalla toimitaan samoin kuin DC:n kohdalla, josta syystä en käy kaikkia kohtia niin tarkasti läpi. Alla tärkeimmät asetukset

![image](https://github.com/user-attachments/assets/df2fd92a-7ff2-4f23-8780-15d4b6d3f809)

![image](https://github.com/user-attachments/assets/94b03db1-0415-459a-a5a3-6ad73c881177)

Huomion arvoista tässä on "Domain"-kohta, josta selviää, että FileServer kone on liitetty `ennevaara.lan`-domainiin ja aikaisemmin määritelty DC01 hallitsee nyt myös tätä palvelinta.

Jotta FileServer konetta voidaan hallita myös etänä, tulee se lisätä DC01 koneelle

![image](https://github.com/user-attachments/assets/77b51955-a3ea-4821-acb4-ba8515a62fb9)

![image](https://github.com/user-attachments/assets/e43a0912-a5b4-48e7-8635-6cfdf9440b5f)

### File Server määritys

Nyt kun perusasetukset ovat kunnossa, voidaan määrittää File Serveri DC01 koneen kautta

![image](https://github.com/user-attachments/assets/65955e97-3101-4ea2-958a-a1f80e156440)

Seuraavaksi määritetään FileServer koneella sijainti, jossa jako tapahtuu sekä miten se näkyy verkossa oleville koneille

![image](https://github.com/user-attachments/assets/b2be8c69-59fc-46b0-9658-0892d2e6c75e)

Seuraavaksi tehdään homma etänä DC01 koneen kautta.

![image](https://github.com/user-attachments/assets/f921ac5c-511e-417e-b3e7-a5f69908ea75)

Ja vielä lopuksi näkymä kummaltakin palvelimelta käsin

![image](https://github.com/user-attachments/assets/92080948-1603-48bd-b25e-bbd48c50c040)

## Resurssit ja AD

Seuraavaksi oli vuorossa resurssien luonti AD:ssa. Tämä on mahdollista toteuttaa komentorivillä tai GUI:n kautta, ja itse tein GUI:n kautta toimet. Alkuun loin domainin alle `Accounts` ja `Resources` Organization Unitit (OU:t) ja niiden alle halutut OU:t (näkyy avattuina kuvassa). Tämän jälkeen loin käyttäjäryhmät `Accounts` OU:n alle. 

![image](https://github.com/user-attachments/assets/0903a516-7950-4abf-bcfb-473abe7b291c)

Tämän jälkeen oli vuorossa yksittäisten käyttäjien luonti kunkin yksikön alle.

![image](https://github.com/user-attachments/assets/7e36a14d-274c-40a6-bf51-845da4a14adc)

Lopuksi vielä käytetään LDAP kyselyä sekä käyttäjien että resurssien selvittämiseksi Domainin sisällä

![image](https://github.com/user-attachments/assets/ea15e856-7962-4a5c-9ba7-3ec3d50ecd7f)

![image](https://github.com/user-attachments/assets/5da1ecf2-5eca-409e-8b03-5127d88bf74d)

## Ryhmäkäytännöt

Seuraavaksi hyödynsin AD:n hallintatyökaluja määrittämällä aikaisemmin luodun `Person` kansion pääsynhallinan. Lisäsin `Domain Admins` ryhmän, jolla on kaikki oikeudet, sekä `Managment`ryhmän, jolla on vain "Read and Execute" oikeudet.

![image](https://github.com/user-attachments/assets/4a41bee9-8ae0-4aa3-a0dd-b66960f95178) ![image](https://github.com/user-attachments/assets/752c4fe6-dc06-43ae-94a1-f9b1cadc1f4d)


![image](https://github.com/user-attachments/assets/ce8aa1ce-89ad-4edc-b94d-dee00df42758) ![image](https://github.com/user-attachments/assets/6c7fa1ee-4320-49e3-af02-a8aedff010ed)


Seuraavana oli vuorossa `Group Policy`-käytäntöjen luonti, joissa noudatin ohjeistuksen mallin lisäksi itse hyväksi näkemiäni käytöntöjä seuraavan taulukon mukaisesti:

| Group Policy | AccountsGP | ProductionGP | ResourcesGP | DesktopsGP | LogonPolicyGP|
| :--- | :---: | :---: | :---: | :---: | :---: | 
| IE selaimen ilmoitukset pois päältä | | | | ✔️ | |
|Salasana vaatimukset | ✔️ | | | | ✔️|
| Turn on Script execution | | ✔️ | | | |
| Do not require CTRL+ALT+DEL | | | | | ✔️ |
| Do not display network UI | | | | ✔️ | |
| Always wait for the network at computer startup and logon | | | ✔️ | | |
| Interactive login: Don't display last signed-in | | | | | ✔️ |
| Enforce user logon restrictions | ✔️ | | | | |
| Maximum lifetime for user ticket renewal | ✔️ | | | | | 
| Windows Firewall enabled | | | | ✔️ | |

### GP Modeling

Nyt oli vuorossa tehtävä, jossa haetaan vastuasta seuraanaa kysymykseen: 

```
Jos käyttäjä Prod Uction tai Prodnon Uction kirjautuu pöytäkoneelle, joka kuuluu
organisaatioyksikköön Desktops, mitä käytäntöjä hänelle sovelletaan?
```

### Workstation 

Tehtävää varten loin virtuaaliympäristöön uuden virtuaalikoneen, josta tein työaseman

![image](https://github.com/user-attachments/assets/71727e42-dc18-4799-9f27-25f6e65e1b3f)

Asennuksen jälkeen laitoin sen verkkoasetukset kuntoon, sekä liitin sen domainiin.

![image](https://github.com/user-attachments/assets/6c2f30b3-65dd-4975-a167-abfe8534fe1d)

Jonka jälkeen ajoin Group Policy Modeling

![image](https://github.com/user-attachments/assets/b3dfe2c2-40c8-496f-8900-d5aba4ed5777)

Jostain syystä käyttäjäkohtaiset GPO:t eivät ole käytössä, mutta konekohtaiset GPO:t ovat kyllä käytössä.

## DNS

Ohjeistuiksen mukaisesti luotu DNS-tietueet:

![image](https://github.com/user-attachments/assets/54e26998-edaa-41a9-98a0-bf8d80f7abb6)

![image](https://github.com/user-attachments/assets/46db3532-ef70-44ae-82f2-664d286dc1f1)

![image](https://github.com/user-attachments/assets/855819dc-7d74-4c90-a7b8-95bf17eb9ea4)

## FTP ja HTTP

### http

Seuraavaksi asensin DC:n kautta FileServerille WEB Serverin ja lisäsin myös FTP serverin sekä IIS Managment consolin asennukseen

![image](https://github.com/user-attachments/assets/e32ccc4c-eeb4-4468-9a9d-3e61568857e9)

Muokkasin FileServerillä oletus sivun otsikon muotoon `IIS Testisivu` sekä lisäsin myös portin 8080 HTTP käyttöön

![image](https://github.com/user-attachments/assets/61670fdc-b575-4c63-8373-25b9df2b6456)

Tämän jälkeen yritin määrittää http uudelleenohjauksen tuohon porttiin 8080, mutta olin epähuomiossa jättänyt HTTP Redirect ominaisuuden asentamatta, joten sen asennus vielä

![image](https://github.com/user-attachments/assets/acf1312e-a2c9-491d-89aa-e471111ac33e)

Jonka jälkeen toteutin uudelleen ohjauksen porttiin 8080 ja muutin vielä sivun otsikon muotoon `IIS Testisivu portti 8080`. Näin ollen selaimessa kirjoitettaessa `http://localhost` ohjautuu se automaattisesti osoitteeseen `http://localhost:8080`

![image](https://github.com/user-attachments/assets/0809435e-fb68-4cde-8f0d-6363ff99f82d)

### ftp

Nyt vuorossa oli FTP sivun lisääminen IIS Managerin kautta, ja nimesin tämän sivuston `TestiFTP` sivustoksi perus asetuksilla. Käytin IIS:ää FTP-palvelun asentamiseen ja tein `TestiFTP`-sivun, jonne lisäsin Adminnille oikeudet.

![image](https://github.com/user-attachments/assets/e5f94633-672c-4b6a-a870-dce30bda72af)

Jonka jälkeen file explorerin osoitekenttään kirjoitin `ftp:\\localhost`. Tämän jälkeen kokeilin kirjautua sisään Anonynous-tilassa ja tehdä muutoksia kansiossa testin vuoksi. Ja kuten oletinkin, sain virheilmoituksen oikeuksien puuttumisesta.

![image](https://github.com/user-attachments/assets/f3a7d07e-3d0a-43f1-a252-ecbcce62c14b)

Tämän jälkeen kirjauduin sisään Admin-tunnuksilla ja lisäsin `testi.txt`-tiedoston kansioon.

![image](https://github.com/user-attachments/assets/2a3cd387-7632-484f-b226-1db5b3bb99a7)

![image](https://github.com/user-attachments/assets/5224c45e-f3a2-48b4-9b29-fb44b57e1fd3)

Näin sain todettua FTP-palvelimen toimivan.

Lopuksi vielä varmistin kaikilta virtuaalikoneilta pääsyn nettiin komennolla `nslookup haaga-helia.fi`

![image](https://github.com/user-attachments/assets/f28d06df-21eb-46bc-a1ae-98351c22df02)

## Loppusanat

Kurssin demo suoritus meni melkeinpä ilman ongelmia. Ainoa asia mitä en ole toistaiseksi saanut ratkaistua, on Group Policy Modeling tehtävän osalta käyttäjä kohtaiset GPO:t, jotka ei jostain syystä ole käytössä, mutta kone kohtaiset kyllä ovat.

Kaikenkaikkiaan tämä kurssin demo suoritus oli mukavaa muistiin palauttelua aiheen tiimoilta, jonka myös koin hyödylliseksi omalla kohdalla asioiden toistona, jotta ne jäävät paremmin muistiin.
