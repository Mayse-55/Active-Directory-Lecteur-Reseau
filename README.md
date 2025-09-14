# Montage lecteur réseau automatique - Active Directory

Automatisation du mappage de lecteurs réseau selon les groupes utilisateurs dans un environnement Active Directory.

## ⚠️ Prérequis Sécurité

**IMPORTANT** : Désactiver l'héritage des permissions sur les partages pour éviter les blocages :
1. Clic droit sur le dossier partagé → **Propriétés**
2. Onglet **Sécurité** → **Avancé**  
3. **Désactiver l'héritage** → Convertir les autorisations héritées
4. Configurer manuellement les permissions par groupe

## 🚀 Installation

### 1. Placement du script
```bash
# Copier le script dans le dossier netlogon
C:\Windows\SYSVOL\sysvol\votre-domaine.com\scripts\connexion_auto.bat
```

### 2. Configuration AD
**Active Directory Users and Computers** (`dsa.msc`) :
- Clic droit utilisateur → **Propriétés**
- Onglet **Profil** → **Script d'ouverture de session** : `connexion_auto.bat`

<img width="505" height="528" alt="image" src="https://github.com/user-attachments/assets/b07992dc-b2fb-4f84-a3a8-d3eb92b82e1c" />

## 📄 Le Script

```batch
@echo off

net use F: \\serveur\Finances
net use I: \\serveur\Impots 
net use R: \\serveur\Responsable
```

## ⚙️ Configuration

### Personnalisation
- **F:** → Remplacer par votre partage Finances
- **I:** → Remplacer par votre partage Impots
- **R:** → Remplacer par votre partage Responsable
- **\\serveur** → Nom de votre serveur de fichiers

### Sécurité
Les permissions d'accès sont gérées par les **ACL** des partages Windows. Seuls les utilisateurs autorisés accèdent aux ressources.

## 🔧 Dépannage

### Script ne se lance pas
1. Vérifier l'emplacement du fichier dans `netlogon`
2. Contrôler la configuration dans le profil utilisateur AD
3. Tester manuellement : `\\serveur\netlogon\connexion_auto.bat`


## 📋 Résultat

À la connexion, l'utilisateur disposera automatiquement des lecteurs mappés selon ses permissions :
- **F:** → Dossier Finances (si autorisé)
- **I:** → Dossier Impôt (si autorisé)  
- **R:** → Dossier Responsable (si autorisé)

## 📝 Notes

- Compatible Windows Server 2012+
- Permissions gérées par Active Directory
- Script simple et efficace pour environnements sécurisés
