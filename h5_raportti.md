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
