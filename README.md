# Étude de Cas : Contournement de Windows Defender

Ce document est le compte-rendu d'un exercice pratique visant à obtenir un accès initial sur un système Windows 11 Pro à jour et protégé par Windows Defender, en utilisant des techniques de contournement d'antivirus (AV Evasion).

## 🎯 Objectif

Obtenir un reverse shell sur une machine Windows 11 Pro, avec ses défenses actives, en simulant une attaque côté client.

## 🔬 Environnement du Laboratoire

-   **Machine Attaquante :** Kali Linux
-   **Machine Cible :** Windows 11 Pro (avec Windows Defender et Pare-feu actifs)
-   **Réseau :** Réseau NAT privé dans VMware

## 👣 Méthodologie et Obstacles Rencontrés

L'approche a consisté en une série de tentatives pour trouver une méthode fonctionnelle, en s'adaptant aux défenses rencontrées.

### Tentative 1 : Payload Meterpreter "Fileless"

-   **Méthode :** Un payload `windows/x64/meterpreter/reverse_tcp` a été généré en format PowerShell (`.ps1`) avec `msfvenom` et hébergé sur un serveur web Python. Un listener `exploit/multi/handler` a été configuré. La victime exécute une commande PowerShell pour télécharger et exécuter le script en mémoire via `IEX(DownloadString)`.
-   **Obstacle :** L'attaque a été **bloquée silencieusement**.
-   **Analyse :** Le test de connectivité (`Test-NetConnection`) sur le port de l'écouteur a réussi, prouvant que le Pare-feu n'était pas en cause. Le blocage provenait donc d'**AMSI (Antimalware Scan Interface)**, qui a inspecté et bloqué le script malveillant en mémoire.

### Tentative 2 : Contournement d'AMSI

-   **Méthode :** Une technique de bypass d'AMSI connue a été exécutée dans la session PowerShell de la victime juste avant de lancer le payload. Un payload "stageless" (`meterpreter_reverse_tcp`) a été utilisé pour plus de stabilité.
-   **Obstacle :** La connexion s'est établie, mais la session Meterpreter était instable et s'est fermée immédiatement.
-   **Analyse :** Instabilité inhérente du payload Meterpreter face aux protections avancées de Windows, même avec un bypass d'AMSI.

### Tentative 3 : Le Test de Contrôle avec Netcat (Succès)

-   **Méthode :** Pour isoler le problème, Metasploit a été remplacé par un simple listener **Netcat** (`nc -lvnp 4444`). Un script PowerShell générant un reverse shell TCP basique a été créé et hébergé.
-   **Résultat :** **Succès.** La connexion s'est établie et un shell interactif a été obtenu.
-   **Conclusion :** Le réseau et la méthode d'exécution en mémoire sont fonctionnels. La sécurité de Windows 11 cible et bloque spécifiquement les payloads complexes et connus comme Meterpreter, mais peut être contournée par des charges utiles plus simples et moins "bruyantes".

## 🧠 Compétences Démontrées

-   Compréhension des défenses modernes de Windows (Defender, Pare-feu, AMSI).
-   Mise en œuvre de techniques de contournement d'antivirus (AV Evasion) via des attaques "fileless".
-   Utilisation avancée de PowerShell pour l'attaque et le dépannage.
-   Méthodologie de dépannage rigoureuse pour isoler un problème (test de connectivité, changement d'outil).
-   Création et utilisation de reverse shells basiques avec Netcat.
