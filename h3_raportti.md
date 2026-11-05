# Demoni

Päivämäärä: 5.11.2026 Panu Torstila

## x) Tiivistelmä

Apache installed with Ansible

- Apachen tai muun verkkopalvelimen asentaminen voidaan automatisoida ansiblella
- Palvelimen hallinnassa käytetään package file server mallia.
- `apt` asentaa ohjelman, `copy` kopioi asetustiedoston

Pohdinta: Tämä selkeytti hyvin sitä miten eri ansible komennot toimivat yhdessä. Sama perjaate toimii nginx kanssa (jota itse tässä tehtävässä automatisoin)

Ansible handlers

- Handler on rooli jonka tehtävä suoritetaan ainoastaan jos jokin toinen tehtävä notifyaa tätä muutoksesta, jolloin se sitten käynnistää palvelun uudestaan.

Pohdinta: Handler on kätevä koska palvelinta ei tarvitse turhaan käynnistää joka kerta kun muutoksia tehdään jossei se ole tarpeen.

Ansible doc service

- `service` komennolla hallitaan palvelimen tilaa.
- `name` määrittää hallittavan palvelun
- `state` määrittää mitä palvelulle tehdään, käynnisetäänkö se? sammutetaanko se?
- `enabled` määrittää käynnistetäänkö palvelu kohdekoneen käynnistäessä.

Pohdinta: `service` komento on jo hieman tuttu edellisestä harjoituksesta, nyt ymmärrän paremmin myös palvelun uudelleen herättelyn automatisointia.

## Johdanto

Tässä harjoituksessa tavoitteena oli tutustua verkkopalvelimen asentamiseen käsin sekä ansiblen avulla.

Tehtävän annosta poiketen käytin tunnilla tehtyä Caddyä a) kohtaan.

## a) Caddy

Aloitin asentamalla caddyn komennolla:
```
sudo apt install caddy
```

Sitten tarkistin sen tilan, että käynnistyikö se heti vai ei:
```
sudo systemctl status caddy
```

Palvelu oli käynnissä, joten tarkistin localhost netti sivut ja huomasin että siellä näkyykin caddyn oletus sivu.

Sitten loin uuden index sivun omaan kotihakemistoon `/home/publicwww/`, jota pystyin muokkaamaan ilman sudoa.

Sitten ohjasin caddyn käyttämään tätä sivua.

Tässä kuva sivusta

<img width="1264" height="1484" alt="image" src="https://github.com/user-attachments/assets/5dda721e-d852-4643-aa1b-14d0217f0485" />

## b) Nginx

Pysäytin ensin caddyn, jotta molemmat palvelimet eivät yritä samaan aikaan tunkeutua samaan porttin.
```
sudo systemctl stop caddy
sudo systemctl disable caddy
```

Seuraavaksi asensin nginx verkkopalvelimen käsin.
```
sudo apt update
sudo apt install nginx
```

Sitten tarkistin käynnistykö palvelin itsestään vai vaatiko se potkaisua.
`systemctl status nginx`

Sitten varmistin localhosti sivun kautta että nään oletus kotisivut siellä.

Sitten muokkasin oletustiedostoa:
`sudo micro /etc/nginx/sites-available/default`

Määritin kotisivun lokaatioksi aikaisemmin luodun hakemiston `/home/publicwww/index.html`

Ensimmäisellä yrittämällä sain tämän tuloksen localhost sivulta:

<img width="1281" height="856" alt="Screenshot 2026-11-05 at 20 55 30" src="https://github.com/user-attachments/assets/4b6628b0-cee2-4caa-8a04-e2e254dcd79c" />

Rupesin tutkimaan tekemiäni muutoksia `default` tiedostoon ja huomasinkin että olin laittanut tiedosto polun sinne väärin. 
```
/home/publicwww/index.html
^
sijaan

/home/publicwww

```

Kun tämä muutos tehtiin ja käynnistin palvelun uudestaan ja kokeilin uudestaan localhost sivua tulosti se tämän:

<img width="1278" height="854" alt="Screenshot 2026-11-05 at 20 57 44" src="https://github.com/user-attachments/assets/2e7d24d3-190b-4aae-a0a0-fe31683a4c69" />

Nyt voin todeta että asennus onnistui ja kaikki tämän suhteen OK

## c) Autonginx

Seuraavaksi tavoitteena oli automatisoida nginx asennus ansiblen avulla.

Käytin tässä kurssilla aikaisemmin tekemääni ansible rakennetta.

Loin aluksi uuden `nginx` roolin sinne ja sen alle `tasks`,`files` ja `handlers`

Kopioin käsin jo valmiiksi testatun `default` tiedoston roolin  `files` hakemistoon.
```
cp /etc/nginx/sites-available/default /roles/nginx/files/
```

Lisäsin `tasks` hakemistoon tiedoston `main.yml` johon asetin seuraavat configuraatiot:

<img width="1512" height="982" alt="Screenshot 2026-11-05 at 21 26 41" src="https://github.com/user-attachments/assets/5d13de43-2a56-4388-b623-f80d68456d4a" />

Configuraatiot otettu T, Karvinen. 2026. https://terokarvinen.com/apache-ansible/

Configuraatiossa ensimmäinen osa varmistaa että nginx on asennettu ja toinen sitten varmistaa että asetustiedosto on copioitu `files` hakemistoon.

Seuraavaksi loin `handlers` hakemistoon myös `main.yml` tiedoston jonne laitoin seuraavat configuraatiot.

<img width="1512" height="982" alt="Screenshot 2026-11-05 at 21 26 06" src="https://github.com/user-attachments/assets/b5577986-9aa1-4fbf-be18-54e5f838e4fd" />

Configuraatiot otettu T, Karvinen. 2026. https://terokarvinen.com/apache-ansible/

Nämä configuraatiot määrittää nginx uudelleenkäynnistyksen. Eli jos jokin tiedosto muuttuu nginx roolin alla niin ne ilmoittaa handlerille joka sitten uudelleen käynnistää palvelun.

Sitten suoritin playbookin:

<img width="1512" height="982" alt="Screenshot 2026-11-05 at 21 27 42" src="https://github.com/user-attachments/assets/33acc034-1213-4c42-a1a1-5ef63c59cafc" />

Se meni läpi ilman muutoksia, eli kaikki ok.

Halusin sitten kuitenkin varmistaa että handleri toimii niinkuin pitää, eli kävin muokkaamassa `tasks` `main.yml` tiedostoa hieman.

Sitten ajoin playbookin uudestaan.

<img width="1512" height="982" alt="Screenshot 2026-11-05 at 21 30 02" src="https://github.com/user-attachments/assets/5429a29b-0160-4c25-8484-d74bd64eefed" />

Tällä kertaa muutoksia tehtiin ja palvelu käynnistettiin uudestaan. Eli kaikki toimii niinkuin pitääkin!

## Yhteenveto

Tämä tehtävä tuntui hieman helpommalta kuin aikaisemmat tehtävät sillä palvelimen asennus ja pystytys ja muokkaus on jo suht tuttua puuhaa aikaisemmilta kursseilta.

Handlerin käyttö oli mielenkiintoinen, se mahdollistaa erilaisia automaatioita.

## Lähteet:

T, Karvinen. 2026. Apache installed with Ansible. https://terokarvinen.com/apache-ansible/ Luettu: 5.11.2026

Tekoäly toiminut .md oppaana.



