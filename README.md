# Uzdevums un Ansible struktūras apraksts

> Ir doti 5 Linux hosti:
> hostname1, hostname2, hostname3, hostname4, hostname5
> hostname1, hostname4 atrodas datucentrā AAA
> hostname2, hostname3, hostname5  atrodas datucentrā ZZZ
Fails "inventory".

> Pieeja uz Internet tīklu ierobežota un ir pieejami tikai pakotņu repozitoriji
Tiek pieņemts, ka tiek izmantota iekšēja repozitoriju krātuve, kura "kešo" operētājsistēmu un citus attālinātos repozitorijus: https://artifactory.company.local
Repozitoriji, kas nāk komplektā ar operētājsistēmu tiek noņemti.
Tiek pieņemts, ka kompānijā tiek izmantoti gan CentOS, gan Debian serveri.

> Izmantojot ansible, veikt sekojošus uzdevumus:
> 1. Lietotāja pievienošana. Lietotājam ir sudo tiesības.
"base" role.
Lietotāja vārdu var norādīt "base/defaults/main.yml" failā.
> 2. Uzinstalēt un nokonfigurēt chrony laika sinhronizāciju. Katram datucentram pieejams savs NTP serveris
"chrony" role.
Serveris tiek iestatīts "group_vars/AAA" un "group_vars/ZZZ".
> 3. Uzinstalēt un nokonfigurēt Zabbix agent2.
"zabbix-agent2" role.
Zabbix serveri var iestatīt "base/defaults/main.yml" failā.


# Serveru sagatavošanas piemērs

Uzģenerēt sertifikātu un novietot to ~/.ssh folderī
```
ssh-keygen -t ed25519 -f ~/.ssh/ansible_key -C "Ansible Key"
```
Eksportēt ansible_key publisko sertifikātu uz linux serveriem, kuri tiks menedžēti ar Ansible.
```
cat ~/.ssh/ansible_key.pub | ssh -i ~/.ssh/existing_remote_key.pem  admin@hostname1 "cat - >> ~/.ssh/authorized_keys" &&
cat ~/.ssh/ansible_key.pub | ssh -i ~/.ssh/existing_remote_key.pem  admin@hostname2 "cat - >> ~/.ssh/authorized_keys" &&
cat ~/.ssh/ansible_key.pub | ssh -i ~/.ssh/existing_remote_key.pem  centos@hostname3 "cat - >> ~/.ssh/authorized_keys"
```

Ja esošajiem serveriem ir pieeja tikai ar lietotājvārdu un paroli, tad var izmantot ssh-copy-id
```
ssh-copy-id -i ~/.ssh/ansible_key.pub user@hostname
```
Ceļu uz privāto sertifikātu norāda ansible.cfg failā.

Palaiž ar
```
ansible-playbook main.yml
```
vai papildina ar vajadzīgajām vērtībām.
