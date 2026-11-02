# Voileipä

Päivämäärä: 2.11.2026 Panu Torstila

## x) Tiivistelmä

Sudo without password

- `sudo` mahdollistaa komentojen ajamisen pääkäyttäjän oikeuksilla.
- On mahdollista kykeä päälle salasanaton sudo määrittämällä käyttäjälle `NOPASSWD` oikeuden sudoers-asetuksista.
- Salasanaton sudo helpottaa helpottaa esim. automaatioissa, mutta tällöin on tärkeä rajata huolellisesti oikeudet tietoturvan vuoksi.

Pohdinta: Salasanaton sudo helpottaa kaikessa mutta jotenkin pitää kytkeä ettei ulkopuoliset pääse käyttämään tällöin konetta jossa salasanaton sudo on laitettu päälle.

Sandwich

- Sarjakuvassa hahmo pyytää toista tekemään hänelle voileivän ja toinen vastaa "mitä, miksi, tee ite".
- Hahmo kysyy uudelleen lisäämällä sudon siihen eteen ja sitten toinen vastaa vain "okei".

Pohdinta: Sarjakuva näyttää selkeästi miten sudo käytännössä toimii. 

Passwordless sudo with ansible

- Ansiblella voidaan automatisoida käyttäjien ja käyttöoikeuksien hallintaa.
- Käyttäjille määritetään SSH avain ja salasanaton sudo.
- Ensimmäisellä ajokerralla tarvitaan sudo salasana mutta sen jälkeen ei enään.

Pohdinta: Ansiblella voidaan tehdä samat käyttäjä ja oikeusasetukset useille koneille ilman että jokaista määritellään erikseen manuaalisesti.

- Copy: Kopioi tiedostoja kohdekoneelle.
- Apt: Hallitsee ohjelmapaketteja.
- File: Hallitsee tiedostoja, hakemistoja ja linkkejä.
- User: Hallitsee käyttäjätunnuksia.
- Authorizer_key: Hallitsee SSH kirjautimseen hyväksyttyjä julkisia avaimia.

Esimerkki: `-name: Asenna curl
  ansible.builtin.apt:
    name: curl
    state: present`

Pohdinta: ansible-doc on tärkeä, sieltä voi katsoa komentoja jos ja kun ne pääsee unohtumaan.

## Johdanto

Tässä harjoituksessa oli tavoitteena tutustua salasanattomaan sudo ja SSH kirjautumiseen sekä harjoitella ansiblen käyttöä.

## a) Sudoless

Aloitin tarkistamalla SSH palvelun:
`sudo systemctl status ssh`

Palvelu oli käynnissä.

Loin uuden käyttäjän ja ryhmän:
`sudo adduser paunu
sudo groupadd sudoless
sudo usermod -aG sudoless paunu
id paunu`

Käyttäjän luominen onnistui.

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 12 40" src="https://github.com/user-attachments/assets/045caec6-10e5-4b91-8133-d770c79f8d94" />

Sen jälkeen määritin salasanattoman sudon:
`sudo visudo -f /etc/sudoers.d/sudoless`

Lisäsin `%sudoless ALL=(ALL) NOPASSWD: ALL`

Sitten testasin toimiiko salasanaton sudo `sudo echo testi`

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 16 30" src="https://github.com/user-attachments/assets/1b7e6a9b-bbbf-45ee-a327-c677ee73eb72" />


## b) Paunu

Ensin tarkistin olemassaolevan tiedosto rakenteen:
`tree`

Sitten loin uuden `paunu` roolin jossa käytin seuraavia moduuleja:
`group: 
user:
authorized_key:
copy:`

Annoin auth key moduulille oman .pub SSH avaimen jonka sain komennolla `cat ~/.ssh/id_ed25519.pub`

Lisäsin myös tässä välissä `site.yml` tiedostoon roolin sekä `become: true`.

Sitten suoritin playbookin:
`ansible-playbook site.yml --ask-become`

Suoritus onnistui, kuvasta näkee että `Changed=3`

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 40 54" src="https://github.com/user-attachments/assets/86b3fcb8-c3b6-4264-bd4d-a7df7c21229f" />

Sitten testasin SSH kirjautumista ilman salasanaa.

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 44 07" src="https://github.com/user-attachments/assets/bea5634e-1d5c-4b35-815b-a4839873891a" />

## c) Paketit

Asensin ansiblella kaksi uutta ohjelmaa: `sl` ja `cowsay`

Loin uuden paketit roolin ja lisäsin sen `main.yml` tiedostoon seuraavat:
   ` - name: asenna ohjelmat
    apt: 
    name:
    - sl
    - cowsay
    state: present`

Sitten suoritin playbookin ja ihmettelin miksei anible tehnyt mitään muutoksia mihinkään.

Huomasin sitten etten muistanut lisätä roolia `site.yml` tiedostoon. 

Lisäsin roolin `site.yml` tiedostoon ja suoritin playbookin uudelleen. Tällä kertaa siellä muutoksia.

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 54 10" src="https://github.com/user-attachments/assets/0fff3cc2-fc7f-4c85-83cd-f209346bd560" />

Sitten testasin tuliko paketit todella koneelle.

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 54 48" src="https://github.com/user-attachments/assets/a82a2200-ddee-4cf2-bb39-3cc990305cd4" />

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 55 06" src="https://github.com/user-attachments/assets/046df209-7164-4a5d-845e-81f352afb8d8" />

Molemmat asentuivat hyvin.

Huomasin pian että cowsay on aika raskas ohjelma oman jaksamisen kannalta joten kävin vaihtamssa paketit `main.yml` tiedostoon `state: absent` Jolla sain molemmat ohjelmat poistettua.

## d) File

Loin ansiblea käyttäen tiedoston `/tmp/testifile.txt` käyttämällä copy moduulia.
   ` - name: luo tiedosto
    copy: 
    dest: /tmp/testifile.txt
    content: "tämä luotiin ansiblella 2.11.2026 klo 20:57"
    owner: paunu
    group: paunu
    mode: "0600"
    `

Tiedoston luominen onnistui roolin lisäämisen `site.yml` tiedostoon jälkeen hyvin.

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 20 58 24" src="https://github.com/user-attachments/assets/d71fa0e8-1010-4544-be29-ad96058d1380" />

Tämän jälkeen yritin avata tiedostoa `cat /tmp/testifile.txt` mutta terminaali ilmoitti ettei oikeuksia ole.

Sitten tarkistin oikeudet ja huomasin ettei minulla ollut siihen oikeuksia joten kävin päivittämässä files roolin `main.yml` tiedostoon `mode: "0644"` Joka antaa myös muille lukuoikeuden.

Sen jälkeen sen avaaminen `cat /tmp/testifile.txt` onnistui.

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 21 00 03" src="https://github.com/user-attachments/assets/6868afee-c61a-45f7-9f9a-ffa47d45fbdb" />

## e) Service

Kokeilin ansiblen `service` moduulia SSH palvelun hallintaan.

Loin `service` roolin: 
    `- name: tarkista että SSH onpäällä
    service:
      name: ssh
      state: started
      enabled: true`

Sitten suoritin playbookin ja sain tuloksen:

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 21 09 33" src="https://github.com/user-attachments/assets/ae4cbc90-13a6-4d19-b1d1-5e16b1f84efd" />

Eli ansible ei muokannut mitään, tämä kävi järkeen kun tiesin että SSH oli käynnissä. Rupesin sitten miettimään miten voin testata tätä käytännössä.

Päädyin testaamaan tätä siten että sammutin SSH palvelun:
`sudo systemctl stop ssh`

Tämän jälkeen ajoin uudestaan playbookin mutta se ei enää saanut yhteyttä kohdekoneeseen joka kävi järkeen sillä se käyttää SSH yhteyttä.

<img width="1512" height="982" alt="Screenshot 2026-11-02 at 21 12 32" src="https://github.com/user-attachments/assets/30fe2639-5a66-43c3-8df7-0abe9bc34b2c" />

Eli en käytännön tasolla päässyt testaamaan tätä service toimintoa.

## Yhteenveto

Tehtävässä opin käyttämään ansiblea käytännön tasolla enemmän ja nyt se käy jo hieman enemmän järkeen kuin viime tehtävää tehdessä.

## Lähteet:

## Lähteet

Karvinen, Tero. 2026. Sudo without password. https://terokarvinen.com/passwordless-sudo/

Karvinen, Tero. 2026. Passwordless Sudo with Ansible. https://terokarvinen.com/passwordless-sudo-with-ansible/

Munroe. 2006. Sandwich. https://xkcd.com/149/

Ansible documentation. https://docs.ansible.com/

Tekoälyä on käytetty markdown oppaana, käytännön ohjeita miten mikäkin elementti saadaan toteutettua.
