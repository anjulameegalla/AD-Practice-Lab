# AD Pentest Lab

## Active Directory lab creation - Initial steps

### Creating Azure resources

- Resource group : ADLab
- VNet : ADLabNet / default range : 10.0.1.0/24

### Domain controller VM

- VM name : GOTHAM-DC
- Image : Windows Server 2019 Datacenter
- Username : anjula
- Password : labuser123Anjula
- Vnet : ADLabNet (10.0.1.0/24)
- Connection : RDP (3389)

### User machine 1

- VM name : THEBATMAN
- Image : Windows 10 Enterprise
- Username : bwayne
- Password : myPassword01
- Vnet : ADLabNet (10.0.1.0/24)
- Connection : RDP (3389)

### User machine 2

- VM name : WAYNEENT
- Image : Windows 10 Enterprise
- Username : lfox
- Password : myPassword02
- Vnet : ADLabNet (10.0.1.0/24)
- Connection : RDP (3389)

---

RDP into GOTHAM-DC with Username : anjula & Password : labuser123Anjula

![{DA4CEC44-B8D5-40E1-90AB-57BFC38D5772}.png](imgs/DA4CEC44-B8D5-40E1-90AB-57BFC38D5772.png)

### Configuring services

Launch Server manager > Add roles and features > Add roles and features wizard

- Server Roles : Active Directory Domain Service

Install service.

Choose “Promote this server to a domain controller”.

### Promoting VM to domain controller

Active directory domain service configuration wizard > Deployment configuration > Add a new forest

- Root domain name : GOTHAM.local
- Forest function level : Windows Server 2016
- Domain function level : Windows Server 2016
- Password : labuser123Anjula

![photo_6320918058767599684_y.jpg](photo_6320918058767599684_y.jpg)

![photo_6320918058767599685_y.jpg](photo_6320918058767599685_y.jpg)

![photo_6320918058767599687_y.jpg](photo_6320918058767599687_y.jpg)

![photo_6320918058767599688_y.jpg](photo_6320918058767599688_y.jpg)

Reboot machine.

RDP into GOTHAM-DC with domain credentials; Username : GOTHAM\anjula & Password : labuser123Anjula

![photo_6320918058767599691_x.jpg](photo_6320918058767599691_x.jpg)

### Configuring certificate services

Launch Server manager > Add roles and features > Add roles and features wizard

- Server roles : Active Directory Certificate Services

Install service.

Choose “Configure Active Directory certificate services on the destination server”.

![photo_6320918058767599692_y.jpg](photo_6320918058767599692_y.jpg)

![photo_6320918058767599693_y.jpg](photo_6320918058767599693_y.jpg)

AD CS configuration wizard >

- Credentials : GOTHAM\anjula
- Role services : Certification Authority
- Setup type : Enterprise CA
- CA type : Root CA
- Private key > Validity period : 99 years

![photo_6320918058767599694_y.jpg](photo_6320918058767599694_y.jpg)

![photo_6320918058767599695_y.jpg](photo_6320918058767599695_y.jpg)

![photo_6320918058767599696_y.jpg](photo_6320918058767599696_y.jpg)

![photo_6320918058767599697_y.jpg](photo_6320918058767599697_y.jpg)

Configure and reboot machine.

---

### Setting up a share

This PC > C:\ Drive > Create new folder “hackme”

Launch Server manager > File and storage services > Shares > Tasks > New share wizard >

- File share profile : SMB Share - Quick
- Share location > custom path : C:\hackme
- Share name : hackme

![photo_6320918058767599699_y.jpg](photo_6320918058767599699_y.jpg)

Create and close.

---

### Creating domain users

Launch Server manager > Tools > Active Directory users and computers > MARVEL.local

Create new Organizational unit (OU) “Groups”

Move every entry except “Guest” and “anjula” from Users to Groups

![photo_6320918058767599701_y.jpg](photo_6320918058767599701_y.jpg)

![photo_6320918058767599703_x.jpg](photo_6320918058767599703_x.jpg)

![photo_6320918058767599702_y.jpg](photo_6320918058767599702_y.jpg)

MARVEL.local > Users >

Create new user

- First name : Bruce
- Last name : Wayne
- User login name : bwayne
- Password : Password1
- Check “Password never expires”

Create another user

- First name : Lucious
- Last name : Fox
- User login name : lfox
- Password : Password2
- Check “Password never expires”

![photo_6320918058767599704_y.jpg](photo_6320918058767599704_y.jpg)

![photo_6320918058767599705_x.jpg](photo_6320918058767599705_x.jpg)

![photo_6320918058767599707_x.jpg](photo_6320918058767599707_x.jpg)

![photo_6320918058767599706_x.jpg](photo_6320918058767599706_x.jpg)

Copy user “anjula” to create another user

- First name : SQL
- Last name : Service
- User login name : SQLService
- Password : MYpassword123#

SQLService > properties >

- Description : “Password is MYpassword123#”

![photo_6320918058767599708_y.jpg](photo_6320918058767599708_y.jpg)

![photo_6320918058767599709_x.jpg](photo_6320918058767599709_x.jpg)

![photo_6320918058767599711_x.jpg](photo_6320918058767599711_x.jpg)

---

### Setting up SPNs

Launch Command prompt as administrator.

Setup SPN for SQLService account

```bash
**setspn -a GOTHAM-DC/SQLService.GOTHAM.local:60111 GOTHAM\SQLService**
```

Confirm SPN.

```bash
**setspn -T GOTHAM.local -Q */***
```

![photo_6320918058767599712_y.jpg](d1317293-f3a1-4a5d-b028-a16ac54999de.png)

![photo_6320918058767599713_y.jpg](photo_6320918058767599713_y.jpg)

### **Domain machine IP address**

```bash
**ipconfig**
```

- **IP : 10.0.1.4/24**

---

### Connecting to user machines

RDP into THEBATMAN with Username : bwayne and Password : myPassword01

Launch File explorer > Network

Turn on Network discovery.

Launch Settings > Network & internet > Ethernet > Change adapter settings

Choose Ethernet > properties > Internet Protocol version 4 (TCP/IPv4)

- Preferred DNS server : 10.0.1.4

![photo_6320918058767599724_y.jpg](photo_6320918058767599724_y.jpg)

RDP connection will be lost. Reconnect to the same user “bwayne”

Launch Settings > Accounts > Access work or school > Connect

Choose “Join this device to a local Active Directory domain”.

- Domain name : GOTHAM.local

Join a domain >

- User : anjula
- Password : labuser123Anjula

![photo_6320918058767599725_w.jpg](photo_6320918058767599725_w.jpg)

![photo_6320918058767599726_x.jpg](photo_6320918058767599726_x.jpg)

Restart machine.

RDP into WAYNEENT with Username : lfox and Password : myPassword02

Repeat the same process above.

### Configuring Domain users to user machines

Launch GOTHAM-DC > Server manager > Tools > Active Directory users and computers > MARVEL.local > Computers

Both user computers “THEBATMAN” and “WAYNEENT” is listed under the domain.

![photo_6320918058767599728_y.jpg](photo_6320918058767599728_y.jpg)

RDP into THEBATMAN with admin credentials; Username : GOTHAM\anjula and Password : labuser123Anjula

Go to Computer management > Local users and groups > Groups > Administrators.

Choose “Add”

Select Users, computers, service accounts or groups >

- Enter object names to select : bwayne

Check names, then apply.

![photo_6320918058767599729_y.jpg](photo_6320918058767599729_y.jpg)

![photo_6320918058767599732_x.jpg](photo_6320918058767599732_x.jpg)

RDP into WAYNEENT with admin credentials; Username : GOTHAM\anjula and Password : labuser123Anjula

Go to Computer management > Local users and groups > Groups > Administrators.

Choose “Add”

Select Users, computers, service accounts or groups >

- Enter object names to select : lfox

Check names & choose “Add” again

- Enter object names to select : bwayne

Check names, then apply.

![photo_6320918058767599733_x.jpg](photo_6320918058767599733_x.jpg)

---

### Re-connecting to machines with Domain user accounts

RDP into THEBATMAN machine with;

- Username : GOTHAM\bwayne
- Password : Password1

RDP into WAYNEENT machine with both;

- Username : GOTHAM\bwayne
- Password : Password1

and

- Username : GOTHAM\lfox
- Password : Password2

---

## Setting up the Attacker machine - steps

to be continued…
