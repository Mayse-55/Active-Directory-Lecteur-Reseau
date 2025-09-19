# Montage lecteur réseau automatique - Active Directory

Automatisation du mappage de lecteurs réseau selon les groupes utilisateurs dans un environnement Active Directory.

## ⚠️ Prérequis Sécurité

### 1. Configuration du partage avancé (Facultatif)
**Important** : Retirer le groupe "Utilisateurs" des permissions de partage pour éviter que les utilisateurs hors groupe puissent voir les partages :
1. Clic droit sur le dossier → **Propriétés** → **Partage** → **Partage avancé**
2. **Autorisations** → Supprimer le groupe **"Utilisateurs"**
3. Ajouter uniquement les groupes spécifiques (Finances, Impôt, Responsable)

### 2. Configurer le réseau en PRIVÉ (solution recommandée)
```powershell
Set-NetConnectionProfile -NetworkCategory Private
```

**Pourquoi PRIVÉ ?**
- **Réseau PUBLIC** : Windows bloque la découverte réseau et le partage (sécurité max)
- **Réseau PRIVÉ** : Windows autorise les partages réseau (réseaux de confiance)
- **Impact** : En PUBLIC, les commandes `net use` peuvent être bloquées par le pare-feu
- **Environnement AD** : Le réseau DOIT être en PRIVÉ pour le bon fonctionnement

### 3. Désactiver l'héritage des permissions
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

Pour Paul Ouri (Administrateur)
```batch
@echo off

net use F: \\serveur\Finances
net use I: \\serveur\Impots 
net use R: \\serveur\Responsable
```
Pour le Groupe Finances
```batch
@echo off

net use F: \\serveur\Finances
```
Pour le Groupe Impots
```batch
@echo off

net use I: \\serveur\Impots 
```
ou
```
@echo off

net use * /delete /y

if /i "%username%"=="pouri" (
    net use F: \\serveur\Finances
    net use I: \\serveur\Impots
    net use R: \\serveur\Responsable
    goto :fin
)

whoami /groups | find /i "Finance" >nul
if %errorlevel%==0 net use F: \\serveur\Finances

whoami /groups | find /i "Impots" >nul
if %errorlevel%==0 net use I: \\serveur\Impots
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
