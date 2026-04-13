# 🛡️ Hardening Linux — Ansible (Conformité ANSSI BP-028)

[![Ansible](https://img.shields.io/badge/Ansible-2.20+-ee0000.svg?style=for-the-badge&logo=ansible)](https://www.ansible.com/)
[![Target](https://img.shields.io/badge/Targets-Debian%20%7C%20Ubuntu-orange.svg?style=for-the-badge&logo=linux)](https://www.debian.org/)
[![Security](https://img.shields.io/badge/Security-ANSSI%20BP--028-blue.svg?style=for-the-badge)](https://www.ssi.gouv.fr/)
[![License](https://img.shields.io/badge/Licence-MIT-green.svg?style=for-the-badge)](LICENSE)

> Automatisation du **durcissement (hardening)** et de l'**audit de sécurité** de serveurs GNU/Linux (Debian & Ubuntu) selon les recommandations de l'**ANSSI BP-028**.

## 📑 Table des matières

- [Fonctionnalités](#-fonctionnalités)
- [Points de contrôle](#-points-de-contrôle)
- [Prérequis](#-prérequis)
- [Installation](#️-installation)
- [Utilisation](#-utilisation)
- [Exemple de rapport d'audit](#-exemple-de-rapport-daudit)
- [Structure du projet](#️-structure-du-projet)
- [Roadmap](#️-roadmap)
- [Licence](#️-licence)

## 🚀 Fonctionnalités

| Fonctionnalité | Description |
| -------------- | ----------- |
| 🔒 **Durcissement système** | ASLR, paramètres sysctl (rôle `systeme`) |
| 🌐 **Durcissement réseau** | Restrictions SSH, firewall (rôle `reseau`) |
| 📦 **Gestion des paquets** | Mises à jour de sécurité automatisées (rôle `paquets`) |
| 👤 **Gestion des utilisateurs** | Comptes, droits, sudoers (rôle `utilisateurs`) |
| 🗂️ **Permissions & montages** | Sécurisation `/dev/shm`, `/tmp` (rôle `fichiers`) |
| 📊 **Audit de conformité** | Vérification avec calcul de score (rôle `audit`) |
| 📝 **Rapports Markdown** | Générés dans `playbooks/reports/`, lisibles sur GitHub ou votre éditeur préféré |

## ✅ Points de contrôle

Des rapports d'audit réels générés sur des machines de test sont disponibles dans le dossier [`playbooks/reports/`](playbooks/reports/) :

- 🟢 [`audit_vm-debian_2026-04-11_freshinstall.md`](playbooks/reports/audit_vm-debian_2026-04-11_freshinstall.md) — Debian, état initial (fresh install)
- 🔵 [`audit_vm-debian_2026-04-11_afterplaybook.md`](playbooks/reports/audit_vm-debian_2026-04-11_afterplaybook.md) — Debian, après application du playbook
- 🟢 [`audit_vm-ubuntu_2026-04-11_freshinstall.md`](playbooks/reports/audit_vm-ubuntu_2026-04-11_freshinstall.md) — Ubuntu, état initial (fresh install)
- 🔵 [`audit_vm-ubuntu_2026-04-11_after-playbook.md`](playbooks/reports/audit_vm-ubuntu_2026-04-11_after-playbook.md) — Ubuntu, après application du playbook

## 📦 Prérequis

- Ansible **2.20+**
- Python **3.11+** sur le contrôleur
- Collections `posix` et `community.general` (à installer avec la commande `ansible-galaxy collection install -r requirements.yml`)
- Accès SSH avec privilèges `sudo` vers les cibles
- Distributions supportées : **Debian 12/13**, **Ubuntu 22.04/24.04**

## 🛠️ Installation

### Cloner le projet

```bash
git clone https://github.com/Makaveli81/ansible-linux-hardening.git
cd ansible-linux-hardening
```

### Configurer l'inventaire

```bash
cp inventories/production.ini.exemple inventories/production.ini
nano inventories/production.ini
```

### Utiliser un environnement python3 virtuel

L'utilisation d'un environnement virtuel python3 permet d'avoir les dépendances directement dans le dépôt plutôt que de les installer sur votre système.

Avec cet utilisation, les versions des dépendances seront résolues grâce aux fichier `requirements.txt`, permettant une idempotence entre les systèmes.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Pour installer les dépendances d'ansible, exécutez la commande `ansible-galaxy` suivante :

```bash
ansible-galaxy collection install -r requirements.yml
ansible-galaxy collection list

# en avril 2026, les versions sont les suivantes :
# Collection                               Version
# ---------------------------------------- -------
# ansible.posix                            2.1.0  
# community.general                        12.5.0 
```

## 🕹️ Utilisation

### Exécuter l'audit uniquement

```bash
ansible-playbook -i inventories/your-inventories.ini playbooks/audit_system.yml -K
```

### Appliquer le durcissement complet

```bash
ansible-playbook -i inventories/your-inventories.ini playbooks/harden_linux.yml -K
```

### Vérifier la syntaxe avant exécution

```bash
ansible-playbook -i inventories/your-inventories.ini playbooks/harden_linux.yml --syntax-check
```

### Lancer le formatage des fichiers yaml

```bash
# à exécuter à la racine du projet
yamllint .
```

### Lancer le formatage ansible-lint

```bash
# à exécuter à la racine du projet
ansible-lint --yamllint .yamllint --config-file .ansible-lint .
```

> [!WARNING]
> Testez toujours le durcissement sur un environnement de **staging** avant la production.

## 📊 Exemple de rapport d'audit

Les rapports sont générés automatiquement dans le dossier `reports/` après chaque exécution d'audit.

> [!TIP]
> Le rapport est au format Markdown, ce qui permet une lecture parfaite directement sur GitHub ou avec votre éditeur préféré.

**Aperçu du rendu :**

Des rapports d'audit réels générés sur des machines de test sont disponibles dans le dossier [`playbooks/reports/`](playbooks/reports/) :

- 🟢 [`audit_vm-debian_2026-04-11_freshinstall.md`](playbooks/reports/audit_vm-debian_2026-04-11_freshinstall.md) — Debian, état initial (fresh install)
- 🔵 [`audit_vm-debian_2026-04-11_afterplaybook.md`](playbooks/reports/audit_vm-debian_2026-04-11_afterplaybook.md) — Debian, après application du playbook
- 🟢 [`audit_vm-ubuntu_2026-04-11_freshinstall.md`](playbooks/reports/audit_vm-ubuntu_2026-04-11_freshinstall.md) — Ubuntu, état initial (fresh install)
- 🔵 [`audit_vm-ubuntu_2026-04-11_after-playbook.md`](playbooks/reports/audit_vm-ubuntu_2026-04-11_after-playbook.md) — Ubuntu, après application du playbook

## ⚙️ Structure du projet

```bash
.
├── group_vars/
│   └── all.yml                     # Variables globales partagées
├── inventories/
│   ├── dev.ini                     # Inventaire environnement de développement
│   └── production.ini.exemple      # Modèle d'inventaire production
├── playbooks/
│   ├── reports/                    # Rapports d'audit générés (ignorés par Git)
│   ├── audit_system.yml            # Playbook d'audit de conformité
│   └── harden_linux.yml            # Playbook de durcissement complet
├── roles/
│   ├── audit/                      # Logique d'audit et calcul de score
│   ├── fichiers/                   # Gestion des permissions et montages
│   ├── paquets/                    # Gestion des paquets et mises à jour
│   ├── reseau/                     # Durcissement réseau
│   ├── systeme/                    # Durcissement OS (sysctl, ASLR…)
│   └── utilisateurs/               # Gestion des comptes et accès
├── .gitignore
├── ansible.cfg                     # Configuration Ansible
└── README.md
```

## 🗺️ Roadmap

Le projet couvre actuellement les niveaux **Minimal (M)** et **Intermédiaire (I)** du référentiel ANSSI BP-028. Les niveaux supérieurs sont en cours d'implémentation selon la feuille de route ci-dessous.

> [!NOTE]
> Le référentiel ANSSI BP-028 v2.0 définit 4 niveaux cumulatifs : **M** (Minimal) → **I** (Intermédiaire) → **R** (Renforcé) → **E** (Élevé).

### 🟣 Niveau R — Renforcé *(à planifié)*

> Pour les systèmes à fort besoin de sécurité ou hébergeant plusieurs applications isolées.

### 🔴 Niveau E — Élevé *(à planifié)*

> Nécessite un personnel qualifié et un maintien régulier. Apporte un gain de sécurité important pour les systèmes les plus critiques.

### 📄 Rapports au format PDF *(à planifié)*

Les rapports d'audit sont actuellement générés en **Markdown**. L'objectif est de proposer également un **export PDF** automatique à la fin de chaque exécution, avec une mise en page soignée (logo, tableau de conformité, score, date d'audit).

## ⚖️ Licence

Distribué sous licence **MIT**. Voir [`LICENSE`](LICENSE) pour plus d'informations.

---

Développé avec ❤️ pour un web plus sécurisé.

Référence : [ANSSI BP-028](https://messervices.cyber.gouv.fr/documents-guides/fr_np_linux_configuration-v2.0.pdf)
