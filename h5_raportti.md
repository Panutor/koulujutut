# Git

Päivämäärä: 24.11.2026 Panu Torstila

## Tiivistelmä:

- Git tallentaa tiedostoista ns. tilannekuvia eli snapshotteja.
- Suurin osa gitin toiminnoista tapahtuu paikallisesti tietokoneella eli tämä toimii vaikka internetyhteyttä ei olisi.
- Git käyttää tarkistussummia tiedostojen eheyden varmistamiseen.
- Git käyttää kolmea tilaa: `modified`, `staged`, `committed`.

Oma huomio: Olen aikaisemmin jotekin pitänyt gittiä ja Githubia samana asiana, tavallaanhan ne on mutta en tiedostanut että git ei automaattisesti tarkoita githubia. En myöskään ymmärtänyt sitä että git toimii myös ilman verkkoyhteyttä paikallisesti tietokoneella.

Lähde: Getting Started - What is Git? https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F Luettu: 24.11.2026

Git komennot:

- `git add .` lisää muutokset tiedostoihin staging-alueelle committia varten.
- `git commit` tallentaa staging alueella olevat muutokset paikalliseen versionhallintaan ja tähän aina lisätään commit note eli mitä muutoksia on tehty.
- `git pull` hakee muutokset.
- `git push` siirtää paikalliset commitit etävarastoon.

## a) Online

Aloitin luomalla uuden repon GitHubiin.

Annoin repon nimeksi `sunshine` ja lisäsin sinne READMEn ja avalitsin lisenssiksi `GNL 3.0`.

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 18 49 50" src="https://github.com/user-attachments/assets/77d1293f-99e7-4674-9b9c-01dac5f62d15" />

Repon luominen onnistui.

## b) Dolly

Seuraavaksi clonesin luomani repon virtuaalikoneelle.

Käytin GitHubin kirjautumisessa SSH avaimia.

Olen jo aijemmin luonut SSH avaimet virtuaalikoneella joten niitä ei tarvinnut luoda uudestaan. Näytin oman pub avaimen komennolla:
```
cat .ssh/id_25519.pub
```

Jonka jälkeen kopioin tämän.

Lisäsin tämän GitHub asetuksissa olevaan SSH keys kohtaan.

Sen jälkeen tehtiin oikea cloonaaminen. Eli kopioitiin GitHubista repon SSH clone code snipet ja komennolla:

```
git clone git@github.com:Panutor/sunshine.git
```

Sitten varmistin cloonatun kansion siirtymällä sinne.

```
cd sunshine
```

Sen jälkeen loin kansioon `testi.md` tiedoston.

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 18 56 10" src="https://github.com/user-attachments/assets/65a1e032-20a4-43ef-8cb7-b48e315cd040" />

Kuvasta näkee luodun tiedoston.

Sitten annoin komennot

```
git add .

git commit
```

add lisää muutoksen staging alueelle. Ja commit lisää ne paikalliseen versionhallintaan ( tällöin tähän myös lisätään kommentti mitä muokattiin ) 

Sitten tarkistin `git log` komennolla tehdyt muutokset ja omat tietoni.

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 18 57 15" src="https://github.com/user-attachments/assets/1eb32634-2394-431b-9d89-5a1875c41c72" />

Kaikki ok.

Sitten vedin gitistä viimeisimmät muutokset komennolla `git pull` <- tämä pitää tehdä aina ennen työntöä.

Sitten työnsin uudet muutokset etävarastoon eli githubiin komennolla: `git push`

Tarkistin tämän onnistuneen github verkko reposta.

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 18 57 32" src="https://github.com/user-attachments/assets/03d4c3ae-fbaf-43e0-94ba-8c03d335fc9c" />

Tästä nähdään että tehty tiedoston on päivittynyt etävarastoon onnistuneesti. 

## c) Doh!

Seuraavaksi tahallaan poistin kaiken `testi.md` sisällön ja halusin sitten peruuttaa tämän toiminnon.

```
micro testi.md
```

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 18 59 41" src="https://github.com/user-attachments/assets/9705fcef-0e51-4bc8-99a7-04a95b19ef19" />

Tästä näkee että muutokset oli tehty staging alueelle.

Sitten halusinkin peruuttaa poistamisen, tein sen komennolla:

```
git reset --hard
```

Tarkistin sitten git statuksen `git status`

Se oli "clean".

Tarkistin sitten vielä itse `testi.md` sisällön komennolla:

```
cat testi.md
```

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 19 00 18" src="https://github.com/user-attachments/assets/f10a809d-8b5b-4223-be1a-dd96bfa8b9b3" />

Sisältö palautui ennalleen.

## d) Tukki

Sitten tarkasteltiin versionhallinan lokeja komennolla:

```
git log --patch
```

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 19 27 06" src="https://github.com/user-attachments/assets/d7fd6450-4b37-4d0c-8d0e-7589fcdb4646" />

Lokeista näki:
- commit eli muokkauksen yksilöllinen tunnus.
- author eli muokkauksen tekijä ja sähköposti
- date eli muokkauksen aikaleima
- commit msg eli muokkaukselle annettu viesti
- - tiedostosta poistetut rivit
- + tiedostoon lisätyt rivit

## e) Gitanbile

Seuraavaksi laitoin aikaisemmin luodun ansible kansion gittiin.

Siirryin ensimmäiseksi ansible hakemistoon `cd ansible/`

Sitten alustin gitin komennolla:

```
git init
```

Tämä loi ansible hakemistoon .git kansion. Sitten tarkistin tämän komennolla `ls -la` ja näin siellä piilotetun .git hakemiston.

Sitten lisäsin tiedostot versionhallintaan ja tein ensimmäisen commitin:

```
git add .
git commit
```

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 19 35 19" src="https://github.com/user-attachments/assets/f33f1442-04cb-418e-b00f-caf04b407a19" />

Sitten muokkasin nginx verkkosivun index.html tiedostoa ja default sivua jotta handleri käynnistää uudelleen daemonin. 

Tämän jälkeen ajoin:

```
ansible-playbook --ask-become site.yml
```

Jonka jälkeen tarkistin että nginx käynnistyi. 

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 20 03 37" src="https://github.com/user-attachments/assets/d18a9afe-6f94-4949-acbe-10214ebd39bc" />

Tässä kuva `systemctl status nginx.server` ja taustalla näkyy vanha index.html localhost sivu.

Alla kuva sitten kun päivitin selaimen uudestaan

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 20 03 50" src="https://github.com/user-attachments/assets/beb7f601-0048-4b91-b8ff-a8a41e0f8a1c" />

Sitten tallensin tekemäni muutokset gittiin komennoilla:

´´´
git add .

git commit
´´´

Sitten tarkistin lokit komennolla:

```
git log
```

<img width="1512" height="982" alt="Screenshot 2026-11-24 at 20 05 02" src="https://github.com/user-attachments/assets/fb8bd2df-12d9-4886-ab0b-0c6cc54dcd5b" />

Tämän jälkeen tarkisin taas `git status` ja se kertoi ettei mitään tallentamattomia muokkauksia ollut. Eli kaikki OK!

## Yhteenveto

Tässä tehtävässä opin käyttämään gittiä paikallisesti ja suoraa komentokehotteesta, aikaisemmin olen vain käyttänyt graafista käyttöliittymää. Ja pakko se on todeta että tämä on kyllä paljon kätevämpää...


## Lähteet:

T. Karvinen. 2026. https://terokarvinen.com/palvelinten-hallinta/ Luettu: 24.11.2026
