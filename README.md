# update-cisco-router
Mise à jour d'un routeur Cisco

![image](https://user-images.githubusercontent.com/83721477/171003092-e5a74061-1289-4ecd-917f-54e9972573a9.png)
![image](https://user-images.githubusercontent.com/83721477/171003562-eb9ffbe1-d058-49c6-8ad9-cf176a5121d6.png)


### Étape 1 : Sélectionnez une image logicielle Cisco IOS
La première étape de la procédure de mise à niveau consiste à sélectionner la version correcte du logiciel Cisco IOS et l'ensemble de fonctionnalités. Cette étape est très importante, et ces facteurs peuvent affecter la décision pour laquelle Cisco IOS vous devez sélectionner :

* Mémoire requise : Le routeur doit avoir suffisamment de disque ou de mémoire flash pour stocker le Cisco IOS.
* Le routeur doit également disposer de suffisamment de mémoire (DRAM) pour exécuter Cisco IOS. Si le routeur n'a pas suffisamment de mémoire (DRAM), le routeur aura des problèmes de démarrage lorsqu'il démarre via le nouveau Cisco IOS.

* Prise en charge des interfaces et des modules : vous devez vous assurer que le nouveau Cisco IOS prend en charge toutes les interfaces et tous les modules du routeur.

* Prise en charge des fonctionnalités logicielles : vous devez vous assurer que le nouveau Cisco IOS prend en charge les fonctionnalités utilisées avec l'ancien Cisco IOS.

### Étape 2 : Téléchargez l'image du logiciel Cisco IOS

### Étape 3 : Identifiez le système de fichiers pour copier l'image
Le type de système de fichiers "flash" ou "disk" est utilisé pour stocker l'image Cisco IOS.<br>
La commande `show file system` affiche la liste des systèmes de fichiers disponibles sur le routeur.<br>
Les systèmes de fichiers « disque/flash » communs pris en charge dans les routeurs Cisco ont des préfixes tels que flash :, slot0 :, slot1 :, disk0 : et disk1 :.<br>
Il doit disposer de suffisamment d'espace pour stocker l'image Cisco IOS.<br>
Vous pouvez utiliser le système de fichiers show ou la commande dir file_system afin de trouver l'espace libre.

### Étape 4 : Préparez la mise à niveau
Vous devriez considérer ces éléments avant que vous amélioriez le Cisco IOS :

* Si le routeur dispose de suffisamment de mémoire (flash, emplacement ou disque), vous pouvez stocker à la fois l'ancien Cisco IOS et le nouveau Cisco IOS.<br>Vous pouvez démarrer le routeur en mode ROMMON et démarrer l'ancien Cisco IOS en cas d'échec de démarrage avec le nouveau Cisco IOS. Cette méthode permet de gagner du temps si vous devez restaurer le Cisco IOS.

* Sauvegardez la configuration du routeur car certaines versions de Cisco IOS ajoutent des configurations par défaut.<br>Cette configuration nouvellement ajoutée peut entrer en conflit avec votre configuration actuelle.

* Comparez la configuration du routeur après la mise à niveau de Cisco IOS avec la configuration sauvegardée avant la mise à niveau. S'il existe des différences dans la configuration, vous devez vous assurer qu'elles n'affectent pas vos besoins.

### Étape 5 : Vérifiez l'image Cisco IOS dans le système de fichiers
```
2600#dir flash:

Directory of flash:/

    1  -rw-    29654656                    <no date>  c2600-adventerprisek9-mz.1
24-12.bin

49807356 bytes total (20152636 bytes free)

2600#verify flash:c2600-adventerprisek9-mz.124-12.bin

Verifying file integrity of flash:c2600-adventerprisek9-mz.124-12.bin...........
................................................................................

................................................................................
.............................Done!
Embedded Hash   MD5 : 1988B2EC9AFAF1EBD0631D4F6807C295
Computed Hash   MD5 : 1988B2EC9AFAF1EBD0631D4F6807C295
CCO Hash        MD5 : 141A677E6E172145245CCAC94674095A

Signature Verified
Verified flash:c2600-adventerprisek9-mz.124-12.bin
```

### Étape 6: vérifier le registre de configuration
Utilisez la commande `show version` afin de vérifier cette valeur.<br>
La valeur est affichée dans la dernière ligne de la sortie show version . Elle doit être défini sur 0x2102.
```
2600#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z. 
2600(config)#config-register 0x2102
2600(config)#^Z
```

### Étape 7 : Activation du nouveau IOS au démarrage
Si le premier fichier dans la flash n'est pas l'image logicielle de Cisco IOS, mais un fichier de configuration, ou quelque chose d'autre, alors vous devez configurer une déclaration de système de démarrage afin de démarrer l'image spécifiée.<br>
Sinon, le routeur essaie de démarrer avec le fichier de configuration ou le premier fichier du Flash, ce qui ne fonctionne pas. S'il n'y a qu'un seul fichier dans le Flash et qu'il s'agit de l'image du logiciel Cisco IOS, cette étape n'est pas nécessaire.

```
2600#show run | include boot
boot system flash:c2600-adventerprisek9-mz.123-21.bin

2600#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z. 
2600(config)#no boot system
2600(config)#boot system flash:c2600-adventerprisek9-mz.124-12.bin
2600(config)#^Z
```

### Étape 8 : Sauvegardez la configuration et rechargez le routeur

```
2600# write memory
2610# reload
Proceed with reload? [confirm]
Jan 24 20:17:07.787: %SYS-5-RELOAD: Reload requested by console. Reload Reason:
Reload Command.
```

### Étape 9 : Vérifiez la mise à niveau de l'IOS
Vérifiez que le routeur fonctionne avec l'image appropriée.

Une fois le rechargement terminé, le routeur doit exécuter l'image logicielle Cisco IOS souhaitée.<br>
Utilisez la commande `show version` afin de vérifier le logiciel Cisco IOS.

```
2600#show version
00:22:25: %SYS-5-CONFIG_I: Configured from console by console
Cisco IOS Software, C2600 Software (C2600-ADVENTERPRISEK9-M), Version 12.4(12),
RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2006 by Cisco Systems, Inc.
Compiled Fri 17-Nov-06 11:18 by prod_rel_team

ROM: System Bootstrap, Version 12.2(8r) [cmong 8r], RELEASE SOFTWARE (fc1)

2610 uptime is 22 minutes
System returned to ROM by reload
System image file is "flash:c2600-adventerprisek9-mz.124-12.bin"
```


