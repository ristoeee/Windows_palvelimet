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

Demoa jatketaan kurssin ensimmäisellä luennolla tehdyssä virtuaaliympäristössä (repon 0-2.md raportit). Myöskään tämä `demo.md` raportti ei ole pakollinen demo-toteutuksessa, mutta ajattelin kuitenkin rustata tämän, jotta itselle jää myös jotain kongreettista kurssista. Ensimmäisellä kurssin luennolla tein siis jo harjoitukset 0, 1 ja 2, joten tästä jatketaan ja seuraavaksi siis itse demoon.

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

