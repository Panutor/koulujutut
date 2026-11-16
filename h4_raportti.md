# Pizza Fantasia

Päivämäärä: 16.11.2026 Panu Torstila

## x) Tiivistelmä

4.12.1 Size and Complexity of Some DSLs

Konfiguraationhallintaan DSL kielet voivat sisältää paljonkin eri toimintoja. Kaikkia niitä ei edes yleensä tarvita perus konfiguraatioissa.

Huomio: Miksi näissä kielissä sitten on näin paljon eri toimitoja jos niitä harvoin kukaan edes tarvitsee? Eikö se ole järjestelmän hieman ylikehittämistä?

4.12.2 Use of DSL Functions in Case Configuration

Tutkimuksessa tarkasteltiin, mitä toimintoja konfiguraatioissa oikeasti tarvitaan. Esim. Paketti, palvelut ja tiedostot.

Huomio: Juuri nämä mainitut on tullut esiin kurssin harjoituksissa.

4.12.3.1 Dependencies Between Main Functions

Toiminnot ovat usein riippuvaisia toisistaan. Esim. ohjelma pitää asentaa ennen kuin palvelu voidaan edes käynnistää.

Huomio: Itselle tämä on jokseenkin itsestään selvää, mutta tämä hyvin selkeyttää sen miksi ansible tehtävien järjestyksellä on niin suuri merkitys.

## Johdanto

Tässä tehtävässä asensin MariaDB tietokantapalvelimen ensin käsin ja sitten automatisoin sen ansiblen avulla.

## a) Räpylä

Valitsin vapaavalintaiseksi demoniksi MariaDBn.

Aloitin asentamalla sen käsin.

```
sudo apt install mariadb-client mariadb-server
```

Tarkistin asennuksen onnistumisen `sudo systemctl status mariadb` avulla joka antoi running statuksen.

Sitten kirjauduin MariaDB sen komennolla `sudo mariadb -u root`

Sitten loin testitietokannan nimeltä `Hats`

```
CREATE DATABASE hats;
SHOW DATABASES;
```

Aluksi oli hieman vaikeuksia sql komentojen kanssa koska unohdin `;` aina komennon lopusta. Mutta lopulta sain sen tehtyä.

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 23 36 24" src="https://github.com/user-attachments/assets/98537175-d054-47d2-bf36-3af0ac0a4d5a" />

## b) Automaattinen Maria

Seuraavaksi lähdin automatisoimaan MariaDB asennuksen ansiblen avulla.

Aloitin luomalla `mariadb` roolin ansible projektiin.

```
mkdir -p roles/mariadb/tasks
```

Sitten loin tasks hakemistoon `main.yml` tiedoston

```
micro roles/mariasb/tasks/main.yml
```

Ja lisäsin `main.yml` tiedostoon seuraavat konfiguraatiot:

```
- apt:
    name:
      - mariadb-client
      - mariadb-server
    state: present

- systemd:
    name: mariadb
    state: started
```

Konfiguraatiot otettu T, Karvinen. 2026. https://terokarvinen.com/apache-ansible/ ja niitä sovellettu tähän sopiviksi.

Sitten lisäsin mariadb roolin `site.yml` tiedostoon jotta playbook ajaa myös sen.

Sitten ajoin playbookin

```
ansible-playbook --ask-become site.yml
```

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 22 14 13" src="https://github.com/user-attachments/assets/4487fc7c-8adc-410c-ab4b-e5daab29af4d" />

Ei tullut virheitä, muttei myöskään muutoksia. Halusin kokeilla että tämä todella toimii joten sammutin mariadb ja kokeilin uudelleen.

```
sudo systemctl stop mariadb
```

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 22 15 40" src="https://github.com/user-attachments/assets/5856c0e3-aa3d-40ea-8415-e94015f5409b" />

Tässä sitten pystyin toteamaan että automaatio onnistui.

## c) Asetukset

Seuraavaksi muokkasin mariadbn asetuksia ansiblen avulla.

Aloitin luomalla mariadbn alle `files` hakemiston. Jonne kopioin yhden mariadbn asetustiedostoista.

```
cp /etc/mysql/mariadb.conf.d/50-server.cnf roles/mariadb/files/50-server.cnf
```
Polku saatu Ansible Docs https://mariadb.com/docs/server/architecture/topologies/single-node-topologies/community-server?utm_source=chatgpt.com

Sitten päätin muuttaa siellä `key_buffer_size` parametriä.

Aluperin se oli asetettu `128M` , muutin sen `64M`

Sen jälkeen lisäsin `tasks/main.yml` tiedostoon `copy` tehtävän.

```
- copy:
    dest: /etc/mysql/mariadb.conf.d/50-server.cnf
    src: 50-server.cnf
    owner: root
    group: root
    mode: "0644"
  notify: Restart Maria
```

Annetut oikeudet tarkoittaa omistaja RW(6) ryhmä R(4) muut R(4)

Configuraatiot otettu ja sovellettu T, Karvinen. 2026. https://terokarvinen.com/apache-ansible/

Koska aina kun asetuksia muokataan ja ne kopioidaan mariadb asetuksiin, niin halutaan tietenkin käynnistää se uudestaan, joten tarvitaan handleri.

Loin handler hakemiston mariadb roolin alle `roles/mariadb/handler` ja sen alle `main.yml` tiedoston.

Tiedostoon laitoin seuraavat configuraatiot:

```
- name: Restart Maria
  systemd:
    name: mariadb
    status: restarted
```

Ja sitten ajoin playbookin.

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 22 42 14" src="https://github.com/user-attachments/assets/8cb9160b-864d-47c1-aef5-bc117521bc1d" />

Ajettuani playbookin huomasin että se teki muutoksia mutta sain silti tämän ongelman. Huomasin kuitenkin ansiblen antamasta vikakoodista että olin vahingossa kirjoittanut `status` enkä `state`. Kävin tämän korjaamassa ja kokeilin uudelleen. 

```
- name: Restart Maria
  systemd:
    name: mariadb
    state: restarted
```
^toimivat configuraatiot

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 22 46 02" src="https://github.com/user-attachments/assets/fe275959-a2d8-49a5-9249-673f6ff825c5" />

Sitten playbook toimi muttei tehnyt muutoksia? Kävin muokkaamassa asetustiedostoa koska arvelin että aikaisemmalla ajokerralla se päivitti muutokset mutta handler ei toiminut ja nyt muutoksia asetustiedostoon ei tehty.

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 22 47 03" src="https://github.com/user-attachments/assets/9e5e55a6-d103-4f82-bf68-110bcaf0782f" />

Nyt se teki kaksi muutosta, päivitti asetukset ja handler käynnisti palvelun uudelleen. Eli kaikki toimi.

Varmistin vielä mariadb asetuksista että tämä todella otti uuden `key_buffer_size` parametrin käyttöön.

```
sudo mariadb -u root

SHOW VARIABLES LIKE 'key_buffer_size'
```

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 23 00 07" src="https://github.com/user-attachments/assets/1c46356d-9bd2-4c8e-94cc-6208b3aaf8a2" />

Ja kyllä vain. Uusi buffer size oli päivittynt.

## d) Remontin paikka

Seuraavaksi kokeilin koko helahoitoa eli pystyykö ansible korjaamaan rikotun asennuksen.

Ensin poistin kaikki mariadb paketit

```
sudo apt purge mariadb-server mariadb-client
```

Tämän jälkeen tarkistin ettei asennusta oltu tehty eli poistaminen onnistui.

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 23 02 47" src="https://github.com/user-attachments/assets/d9579ee4-3c67-4126-8e96-9b08b586c385" />

Ja tästä voidaan todeta että purge todella tepsi.

Sitten ajoin playbookin 

```
ansible-playbook --ask-become site.yml
```

Ja sain tämän 

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 23 03 15" src="https://github.com/user-attachments/assets/5ff1115c-4f78-4770-9f71-7877b5ba7888" />

Eli jotain muutoksi ainakin tapahtui, sitten tarkistin vielä että mariadb on käynnissä ja asennettu yms.

```
sudo systemctl status mariadb
```

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 23 03 30" src="https://github.com/user-attachments/assets/9bc9526e-1e53-4ffa-a185-7e6700ee2987" />

Ja kävin vielä tämän jälkeen tarkistmassa että `key_buffer_size` oli edelleen se minkä olin sille antanut. Ja kyllä oli.

## e) Impotentti

Sitten tarkistin että onhan automaatio impotentti, eli ettei se tee turhia muutoksia jos niille ei ole tarvetta.

Eli ajoin vain uudestaan playbookin

```
ansible-playbook --ask-become site.yml
```

Ja sain tämän tulosteen.

<img width="1512" height="982" alt="Screenshot 2026-11-16 at 23 04 30" src="https://github.com/user-attachments/assets/1687259d-7de6-416c-b86c-18498f070e2b" />

Eli voin todeta sen olevan impotentti.

## Yhteenveto

Tehtävässä opin eritysesti miten sql komennot toimivat sillä se oli täysin uusi tuttavuus itselle.

Ongelmia syntyi kun ei keskittynyt tarpeeksi omaan kirjoitukseen...

## Lähteet:

T, Karvinen. 2026. Apache installed with Ansible. https://terokarvinen.com/apache-ansible/ Luettu: 16.11.2026

T, Karvinen. 2018. Install MariaDB on Ubuntu 18.04 https://terokarvinen.com/2018/install-mariadb-on-ubuntu-18-04-database-management-system-the-new-mysql/ Luettu: 14.11.2026

Ansible Docs https://mariadb.com/docs/server/architecture/topologies/single-node-topologies/community-server?utm_source=chatgpt.com Luettu: 16.11.2026

Tekoäly toiminut .md oppaana.
