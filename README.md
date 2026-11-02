# Vulns-Web-Server : rapports de pentest sur machines d'entraînement

Deux rapports de test d'intrusion que j'ai rédigés en novembre 2025 pour m'entraîner à la sécurité offensive et à la restitution écrite. Les cibles sont des **machines virtuelles volontairement vulnérables**, installées en local. Ce sont des exercices d'entraînement, pas des missions pour un client : aucun système tiers n'a été testé.

## Contenu

| Fichier | Cible | Ce que le rapport montre |
|---|---|---|
| [`RAPPORT_PENTEST_WEB.pdf`](RAPPORT_PENTEST_WEB.pdf) | Application web PHP vulnérable (VM d'entraînement) | Exploitation d'une dizaine de vulnérabilités web, avec payloads, captures, analyse et correctifs |
| [`rapport_pentest_win2k8.pdf`](rapport_pentest_win2k8.pdf) | Windows Server 2008 R2, de type Metasploitable 3 | Reconnaissance, exploitation de plusieurs services, recommandations |
| [`scripts/`](scripts) | : | Scripts Python écrits pour l'exercice web (`force.py`, `deep.py`) |

## Rapport web : vulnérabilités traitées

| Vulnérabilité | Technique employée |
|---|---|
| Contournement de filtre d'upload | Fichier PHP envoyé avec un faux type MIME (`image/jpeg`) via cURL |
| Directory traversal | Séquences `../` dans le paramètre `page` pour lire `/etc/passwd` |
| Injection SQL (UNION) | Énumération de `information_schema`, extraction de tables applicatives |
| Fichier sensible exposé | `.htpasswd` trouvé via `robots.txt`, hash MD5 cassé, accès à `/admin` |
| Force brute | Script Python (`force.py`) sur un formulaire sans limitation de tentatives |
| Falsification de cookie | Cookie `I_am_admin` contenant `MD5("false")` remplacé par `MD5("true")` |
| Validation côté client uniquement | Valeur hors plage envoyée en modifiant le HTML du formulaire |
| Injection de contenu | Paramètre `src` inséré tel quel dans une balise `<object>` (URI `data:`) |
| Redirection non contrôlée | Paramètre `site` utilisé sans liste blanche |
| Listage de répertoires | `.hidden/` indexé, exploré récursivement avec un script (`deep.py`) |

Chaque vulnérabilité suit le même plan : **exploitation, analyse, mesures correctives recommandées**.

## Rapport Windows Server 2008 R2 : étapes

1. **Reconnaissance** : scan Nmap (`-sS -sV`), identification des services et des versions.
2. **Elasticsearch 1.1.1** : exécution de code à distance (CVE-2014-3120) via le module Metasploit `script_mvel_rce`.
3. **FTP** : accès avec des identifiants faibles (`vagrant:vagrant`).
4. **Jenkins** : Script Console exposée sans authentification, reverse shell obtenu via Groovy.
5. **MS17-010 (EternalBlue)** : accès au système via le module Metasploit `ms17_010_eternalblue`.
6. **Recommandations** : mise à jour de l'OS, correctifs, authentification Jenkins, suppression des comptes faibles, segmentation réseau.

## Outils utilisés

Kali Linux, Nmap, Metasploit, cURL, outils de développement de Firefox, Python 3.

## Limites, pour être transparent

- Ce sont des **machines d'entraînement** dont les failles sont connues et documentées publiquement. Ces rapports montrent ma méthode et ma façon de restituer, pas la découverte de failles inédites.
- Les **scores de criticité (CVSS v3.1) sont indicatifs** et propres au contexte du lab.
- Le rapport web contient les **flags** de l'exercice en clair, ce qu'on ne ferait pas dans un rapport client.
- Le rapport Windows s'arrête à la compromission initiale. Je n'y ai pas poussé la post-exploitation (élévation de privilèges, mouvement latéral).
- Mon objectif est de progresser sur les tests d'intrusion applicatifs et Active Directory. Je prépare l'eJPT à partir du 16 novembre 2026, puis l'OSCP.

## Contact

Vincent Beaudraps : [LinkedIn](https://www.linkedin.com/in/vbeaudraps-b07b651a9)
