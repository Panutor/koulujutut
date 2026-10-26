# Hei Ansiblen maailma

Päivämäärä: 26.10.2026 Panu Torstila

## Johdanto

Tässä harjoituksessa tutustuin SSH yhteyden käyttöön ja ansiblen avulla tehtävään automatisointiin. Tavoitteena oli saada SSH yhteys toimimaan paikallisesti sekä kokeilla ansiblen käyttöä Hello world tehtävän avulla.

Käytin harjoituksessa omaa Mac konetta, jossa ajoin Debian 13 Virtualboxin kautta.

## SSH yhteyden testaaminen

Aloitin testaamalla SSH yhteyttä paikalliseen koneeseen komennolla:
`ssh localhost`

Yhteys muodostui onnistuneesti ja pääsin kirjautumaan paikalliseen koneeseen.

Poistuin komennolla:
`exit`

<img width="1512" height="982" alt="Screenshot 2026-10-22 at 20 23 31" src="https://github.com/user-attachments/assets/3e42f4fe-a2ab-431f-9d98-d13d24e5ed65" />

Tämän jälkeen laitoin päälle SSH kirjautumiseen julkisen avaimen.

## Ansiblen testaaminen

Seuraavaksi siirryin ansiblen käyttöön. Aloin siirtymällä juuri luomaani `ansible` hakemistoon:
`cd ansible`

Sitten loin `hosts.ini` tiedoston ansible hakemistoon:
`micro hosts.ini`

Lisäsin tiedostoon paikallisen Debian koneeni:
`localhost`

Tämän jälkeen testain ansiblen toimintaa komennolla:
`ansible all -a 'uptime' -i hosts.ini`

Komento onnistui ja sain vastaukseksi koneen käynissäoloajan.

Ansible näytti myös Python tulkin tunnistamisen liittyvän varoituksen.

<img width="1512" height="982" alt="Screenshot 2026-10-22 at 20 28 22" src="https://github.com/user-attachments/assets/1708cd1b-19bf-4020-a721-9159d3754e52" />

## Ansible asetusten määrittäminen

Seuraavaksi loin `ansible.cfg` tiedoston:
`micro ansible.cfg`

Lisäsin tiedostoon seuraavat asetukset:
`ini
[defaults]
inventory = hosts.ini`

Tämän jälkeen pystyin suorittamaan komennon erillistä inventory tiedoston määrittämistä:
`ansible all -a 'uptime'`

Komento toimi onnistuneesti ja näytti jälleen koneen käynnissäoloajan.

<img width="1512" height="982" alt="Screenshot 2026-10-22 at 21 12 32" src="https://github.com/user-attachments/assets/5330a628-1478-4168-a741-3055c1ff51c4" />

## Ensimmäinen ansible playbookin luominen

Seuraavaksi aloin tekemään ensimmäistä ansible playbookia. Loin `site.yml` tiedoston:
`micro site.yml`

Kirjoitin tiedostoon nämä asetukset:
`- hosts: all
  roles:
    - hello
`

Yritin suorittaa playbookin:
`ansible-playbook site.yml`

Sain kuitenkin virheilmoituksen:
`[ERROR] the role 'hello' was not found`

Virhe ilmoitus myös näytti missä kohtaa `site.yml` tiedostoa virhe on.

<img width="1512" height="982" alt="Screenshot 2026-10-22 at 21 13 31" src="https://github.com/user-attachments/assets/f64a062d-c38e-4c74-bba1-b3d88862cd82" />

## Hello roolin luominen

Loin tarvittavan roolin:
`mkdir -p roles/hello/tasks/`

Tämän jälkeen avasin rooli tiedoston:
`micro roles/hello/tasks/main.yml`

Määrittelin tiedostoon tehtävän jonka tarkoitus on luoda `/tmp/hello-ansible` tiedosto ja kirjoittaa sinne teksti.

Suoritin playbookin uudestaan:
`ansible-playbook site.yml`

Tällä kerralla se onnistui ja sain seuraavan tuloksen:

<img width="1512" height="982" alt="Screenshot 2026-10-22 at 21 15 33" src="https://github.com/user-attachments/assets/51cda7c4-0d73-45f8-ad6e-7818efa12dd9" />

## Lopputuloksen tarkistus

Halusin vielä varmistaa, että ansible oli oikeasti luonut tiedoston ja kirjoittanut sinne oikean tekstin.

Tarkistin tämän SSH yhteyden kautta:
`ssh localhost 'cat /tmp/hello-ansible'`

Sain vastauksen:
`See you at TeroKarvinen.com!`

Tiedosto oli siis luotu onnistuneesti.

<img width="1512" height="982" alt="Screenshot 2026-10-22 at 21 16 44" src="https://github.com/user-attachments/assets/f10c929e-5bd9-42cc-a78d-ec7d1f033aba" />

## Playbookin suorittaminen uudelleen

Tämän jälkeen muokkasin vielä `ansible.cfg` tiedostoa.

Lisäsin sinne asetuksen jonka avulla nään tarkemmin mitä tehtävissä suoritettiin:
`display_args_to_stdout = true`

Suoritin vielä playbookin uudestaan:
`ansible-playbook site.yml`

Tällä kertaa sain tulokseksi:

<img width="1512" height="982" alt="Screenshot 2026-10-22 at 21 19 07" src="https://github.com/user-attachments/assets/72b77012-5fcf-46d1-8fe8-d42b206309a6" />

## Loppukaneetti

Harjoitus meni hyvin. Sain tehtyä tehtävässä vaaditut toimet ja opin luomaan ansible inventory ja asetustiedostot sekä suorittamaan ansible komentoja. Tein myös ensimmäisen playbookkini jolla loin tiedoston kohde tietokoneelle.

Harjoituksen aikana vastaan tuli virheilmoitus puuttuvasta `hello` roolista, mutta sain sen ratkaistua luomalla puuttuvan roolin.

Mielenkiintoista oli nähdä miten ansible toimi kun ajoin komennon uudestaan vaikka muutokset oli jo tehty. Se huomasi että pyydetyt muutokset on jo tehty kohde tietokoneella ja silloin se ei tehnyt mitään.

## Lähteet

Karvinen, Tero. 2026. SSH public key - Login without password. https://terokarvinen.com/ssh-public-key-login-without-password/

Karvinen, Tero. 2026. Hello Ansible. https://terokarvinen.com/hello-ansible/

Tekoälyä käytin markdownin opetteluun: Miten otsikko ja toissijainen otsikko tehdään, miten komento boxit tehdään.

