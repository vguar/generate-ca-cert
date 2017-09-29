# generate-ca-cert

SOURCE : http://www.linux-france.org/prj/edu/archinet/systeme/ch24s03.html

Etape 1 : La création des certificats
Prev 	Chapter 24. TP sur le serveur WEB sécurisé	 Next
Etape 1 : La création des certificats

Connectez-vous sous root et allez dans le répertoire de configuration de votre serveur Apache2 /etc/apache2 (on peut évidemment choisir un autre répertoire) et créez un répertoire appelé ssl. Vous vous placerez dans ce répertoire afin que les clés et les certificats soient créés à l'intérieur avant d'effectuer les manipulations.

Création du certificat serveur

Génération de la clé privée

On génère la clef privée avec la commande suivante en définissant un nom de fichier :

openssl genrsa 1024 > servwiki.key
La sortie attendue est la suivante :

Generating RSA private key, 1024 bit long modulus
 ..................++++++
 .................................................................++++++
 e is 65537 (0x10001)
Si vous souhaitez que cette clé ait un mot de passe (qui vous sera demandé à chaque démarrage d'apache, donc à éviter !), ajoutez "-des3" après "genrsa".

Ceci a pour effet de créer une clé SSL (fichier servwiki.key), ne la perdez pas... c'est votre clé privée... (ni éventuellement le mot de passe) !

Vous pouvez observer son contenu : less servwiki.key

-----BEGIN RSA PRIVATE KEY-----
MIICXgIBAAKBgQDQG9wvnuLC4aqzaJCAWGA1AxFzg00hjPObhq1mukzsGyuuWBFG
vj/k9vFNYX55DHctb/4cXtsZRWWvgcjtYnCVwRu+DAjFsk//kOMfhplmiv9xQ+ZL
8w/Xrnm8JWdSS+S4LCMnsuIiQtLbhMrQnUV02hAtbIZiSM3k6OjShEZhDQIDAQAB
AoGAHi0cBW+1k+qjFPbBlUq7UJSMUEKmyYmlvVSPCklTZB0gfVxZzPdDTpEcNks/
yo+rLFSD9Vsvy/9LGmLoXruadWlK67PCUnpM5/oRRGgy8t73YKrxflAU5Gtymjvc
ZCf0CAs6wBft3yLU31Qc4WqVM2vTyUH76jebVhxEw8k63OUCQQD/1OmAXV+TxBPG
ZTPFbzUeAE5rQqqOW4aoMNvM61Yn/19h6SzY2MfSQvF1BNns/efCRrqOMeyvPWUG
g1okfogTAkEA0D7pDf/D2Yu5msbOAGF4QBU1erLzpi/s6Rv6VEPYCGnHQlo3jbg9
FZbjHJ4UcYyYaA8jIrkY+FIJM88YlGbWXwJBAILEdvJ5R/CFCkKf2j2yIWmLaIol
En8fw43XI5L0PB7Hxx6KDLVu4XzVYQyahTZBdqR0eMlUNZJBhJE2tO3wi2cCQQCp
JkCFd3es0BrNxqfzlThozRFofcz88za7TldydL0YcFtC4Sb4vWsYizwktZ6jcPEm
rQz8Gl9W7MO+ynwLptB/AkEA1tsnFXoYzI71enmTdugGxbv0RqAd5iQpDYQkDSdn
2LImp/3YnXNJ9qpY91j87tKthh/Oetu6SHlmLg1LOYNIdw==
-----END RSA PRIVATE KEY-----
Protégez votre fichier en faisant : chmod 400 servwiki.key

De multiples autres options sont possibles mais il est inutile d'alourdir le sujet (à creuser pour une éventuelle PTI...)

A partir de votre clé, vous allez maintenant créer un fichier de demande de signature de certificat (CSR Certificate Signing Request).

On génère la demande de certificat avec la commande suivante :

openssl req -new -key servwiki.key > servwiki.csr
Le système va vous demander de saisir des champs ; remplissez-les en adaptant sauf le champ "Common Name" qui doit être identique au nom d'hôte de votre serveur virtuel :

Country Name (2 letter code) [AU]:FR
State or Province Name (full name) [Some-State]:CORSE
Locality Name (eg, city) []:Ajaccio
Organization Name (eg, company) [Internet Widgits Pty Ltd]:LLB
Organizational Unit Name (eg, section) []:BTSINFO
Common Name (eg, YOUR name) []:wiki.domain1.org
Email Address []:
Ce n'est pas la peine de saisir d'autres "extra attributes"...

Ceci a pour effet de créer le formulaire de demande de certificat (fichier servwiki.csr) à partir de notre clé privée préalablement créée.

Ce fichier contient la clé publique à certifier. Un less servwiki.csr nous donne :

-----BEGIN CERTIFICATE REQUEST-----
MIIBsTCCARoCAQAwcTELMAkGA1UEBhMCRlIxFTATBgNVBAgTDENvcnNlIGR1IFN1
ZDEQMA4GA1UEBxMHQWphY2NpbzEMMAoGA1UEChMDTExCMREwDwYDVQQLEwhCVFMg
SU5GTzEYMBYGA1UEAxMPcHJvZi5idHNpbmZvLmZyMIGfMA0GCSqGSIb3DQEBAQUA
A4GNADCBiQKBgQDSUagxPSv3LtgDV5sygt12kSbN/NWP0QUiPlksOkF2NkPfwW/m
f55dD1hSndlOM/5kLbSBo5ieE3TgikF0IktjBWm5xSqewM5QDYzXFt031DrPX63F
vo+tCKTQoVItdEuJPMahVsXnDyYHeUURRWLWwc0BzEgFZGGw7wiMF6wt5QIDAQAB
oAAwDQYJKoZIhvcNAQEEBQADgYEAwwI4UvkfhBvyRrUXtjrLfZXLxZlF9o+URmHJ
ysvrLKfesVBEzdA9mqk1OwIwLfe8Fw2fhip2LGqvcCPxDMoIE/0cDkqGRg9iKp7D
DMuy69lPTEB6UtpVKO/1eage0oug6VqdfGAYMMSGyWFuO9FE4UE6HspVodb20wGV
4H8qZuk=
-----END CERTIFICATE REQUEST-----
Maintenant, deux choix s'offrent à vous :

envoyer le fichier servwiki.csr à un organisme (le tiers de confiance ou l'autorité de certification (CA)) et ainsi obtenir le certificat dûment signé par la clé privée de l'organisme (après avoir payé),

ou bien signer vous-même le certificat.

Ce dernier choix a notre préférence...

Création du certificat de l'autorité de certification

Pour signer un certificat, vous devez devenir votre propre autorité de certification, cela implique donc de réaliser une clé et un certificat auto-signé.

La création de la clé privée de l'autorité de certification se fait comme précédemment :

openssl genrsa -des3 1024 > ca.key
Ce qui a pour effet de créer la clé privée de l'autorité de certification. Dans ce cas, il vaut mieux rajouter l'option -des3 qui introduit l'usage d'une "passphrase" (mot de passe long qui peut même contenir du blanc) car c'est cette clé privée qui signera tous les certificats que l'on emettra ; cette "passphrase" sera donc demandée à chaque utilisation de la clé.

Ensuite, à partir de la clé privée, on crée un certificat x509 pour une durée de validité d'un an auto-signé :

openssl req -new -x509 -days 365 -key ca.key > ca.crt
Il faut saisir la passphrase... puisqu'on utilise "ca.key" que l'on a protégé. Et on donne les renseignements concernant cette fois-ci l'autorité de certification (c'est à dire nous-même).

Attention : le nom de "Common Name" doit être différent de celui qui a été donné pour la clé.

Country Name (2 letter code) [AU]:FR
State or Province Name (full name) [Some-State]:CORSE
Locality Name (eg, city) []:Ajaccio
Organization Name (eg, company) [Internet Widgits Pty Ltd]:LLB
Organizational Unit Name (eg, section) []:BTSINFO
Common Name (eg, YOUR name) []:cert_CA
Email Address []:
C'est notre certificat d'autorité de certification qui va permettre de signer les certificats créés.

La signature du certificat serveur par le CA (Certificate Autority)

La commande qui signe la demande de certificat est la suivante :

openssl x509 -req -in servwiki.csr -out servwiki.crt -CA ca.crt -CAkey ca.key\
-CAcreateserial -CAserial ca.srl
L'option CAcreateserial n'est nécessaire que la première fois.

Il faut saisir la passphrase...

Le certificat signé est le fichier "servwiki.crt". La sortie de la commande attendue est la suivante :

Signature ok
subject=/C=FR/ST=CORSE/L=Ajaccio/O=LLB/OU=BTSINFO/CN=wiki.domain1.org
Getting CA Private Key
Enter pass phrase for ca.key:
Vous avez maintenant un certificat pour votre serveur se nommant servwiki.crt.

less servwiki.crt
-----BEGIN CERTIFICATE-----
MIICVDCCAb0CAQEwDQYJKoZIhvcNAQEEBQAwdDELMAkGA1UEBhMCRlIxFTATBgNV
BAgTDENvcnNlIGR1IFN1ZDEQMA4GA1UEBxMHQWphY2NpbzEMMAoGA1UEChMDTExC
MREwDwYDVQQLEwhCVFMgSU5GTzEbMBkGA1UEAxMSc2VydmV1ci5idHNpbmZvLmZy
MB4XDTA0MDIwODE2MjQyNloXDTA0MDMwOTE2MjQyNlowcTELMAkGA1UEBhMCRlIx
FTATBgNVBAgTDENvcnNlIGR1IFN1ZDEQMA4GA1UEBxMHQWphY2NpbzEMMAoGA1UE
ChMDTExCMREwDwYDVQQLEwhCVFMgSU5GTzEYMBYGA1UEAxMPcHJvZi5idHNpbmZv
LmZyMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDSUagxPSv3LtgDV5sygt12
kSbN/NWP0QUiPlksOkF2NkPfwW/mf55dD1hSndlOM/5kLbSBo5ieE3TgikF0Iktj
BWm5xSqewM5QDYzXFt031DrPX63Fvo+tCKTQoVItdEuJPMahVsXnDyYHeUURRWLW
wc0BzEgFZGGw7wiMF6wt5QIDAQABMA0GCSqGSIb3DQEBBAUAA4GBALD640iwKPMf
pqdYtfvmLnA7CiEuao60i/pzVJE2LIXXXbwYjNAM+7Lov+dFT+b5FcOUGqLymSG3
kSK6OOauBHItgiGI7C87u4EJaHDvGIUxHxQQGsUM0SCIIVGK7Lwm+8e9I2X0G2GP
9t/rrbdGzXXOCl3up99naL5XAzCIp6r5
-----END CERTIFICATE-----
Il faut maintenant installer le certificat de l'autorité de certification dans chaque navigateur client. C'est ce dernier qui va valider le certificat reçu par le client lors de la requête https://.

Installation du certificat d'autorité de certification

Pour installer sur votre navigateur le certificat de l'autorité de certification, fichier ca.crt :

"Arrangez-vous" pour avoir le certificat disponible à partir du client (disquette, clé usb, ftp, ssh, répertoire partagé...)

Sous Windows, un clic droit ou double-clic sur le fichier devrait vous permettre de lancer l'assistant d'installation du certificat dans Internet Explorer. Vous devez l'installer en tant qu'autorité de certification. Vérifiez ensuite que le certificat s'y trouve bien sous le nom de "cert_CA".

sur Mozilla :

Edition/préférence/Confidentialité et Sécurité/Certificats

Bouton de commande : gestion des certificats

Onglet : autorité

Bouton de commande : importer

etc...

Il ne reste plus maintenant qu'à configurer apache2.

Prev 	Up	 Next
Les paquets à installer 	Home	 Etape 2 : configuration d'Apache2
