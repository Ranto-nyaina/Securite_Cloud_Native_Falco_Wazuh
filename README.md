# 🛡️ Sécurité Cloud-Native — Détection et Réponse avec Falco et Wazuh

Projet pédagogique — Master 1, mention Objets Connectés et Cybersécurité (OCC), année universitaire 2025-2026.

Mise en place d'une plateforme de surveillance et de réponse automatisée aux attaques dans un environnement Kubernetes, combinant détection comportementale (Falco) et centralisation SIEM/XDR (Wazuh) sur un cluster K3s.

Ce dépôt réunit la présentation du projet et le rapport (guide d'installation et de mise en place).

---

## 🎯 Objectif du projet

Les architectures Cloud-Native et conteneurisées exposent les infrastructures à une nouvelle génération de menaces : attaques ciblant Kubernetes, exploitation de vulnérabilités de conteneurs, accès illégitime aux données, logs dispersés et difficiles à corréler. Ce projet répond à ce besoin en couvrant toute la chaîne, de la détection à la réponse :

- déployer un cluster Kubernetes léger (K3s) comme environnement cible ;
- détecter en temps réel les comportements suspects à l'intérieur des pods (Falco) ;
- centraliser, corréler et qualifier la gravité des alertes (Wazuh) ;
- déclencher une réponse automatisée à l'incident (Active Response) ;
- documenter une procédure d'installation reproductible.

---

## 📌 Problématique

Comment surveiller en continu un environnement Kubernetes, détecter des comportements anormaux au niveau des conteneurs, et automatiser une réponse à l'incident, dans une architecture Cloud-Native où la centralisation et l'analyse des logs sont naturellement complexes ?

---

## 🏗️ Architecture du projet

```text
┌────────────┐     ┌─────────────┐     ┌─────────────────┐     ┌───────────────────┐     ┌────────────────────┐
│   Falco    │ ──> │ Wazuh Agent │ ──> │  Wazuh Indexer  │ ──> │   Wazuh Manager   │ ──> │  Active Response   │
│ (K3s/pod)  │     │ (collecte)  │     │ (stock./indexe) │     │ (analyse/corrèle) │     │ (isole/recrée pod) │
└────────────┘     └─────────────┘     └─────────────────┘     └───────────────────┘     └────────────────────┘
  Détection           Collecte              Stockage                Corrélation                 Réponse
  d'anomalie         de l'alerte           des alertes               & gravité                à l'incident
```

Le détail de chaque étape (installation, configuration, test) est documenté dans `rapport/GUIDE_INSTALLATION.pdf`.

---

## 📁 Structure du dépôt

```text
.
├── presentation/
│   └── SECURITE_CLOUD_NATIVE.pptx        # support de soutenance
├── rapport/
│   └── GUIDE_INSTALLATION.pdf            # installation, configuration, tests, dépannage
├── .gitignore
└── README.md
```

---

## 🚀 Installation et déploiement

Le déploiement complet (K3s, Falco, Wazuh, intégration, test de bout en bout) est détaillé dans `rapport/GUIDE_INSTALLATION.pdf`. Résumé des grandes étapes :

```bash
# 0. Cloner le dépôt
git clone https://github.com/Ranto-nyaina/Securite_Cloud_Native_Falco_Wazuh
cd Securite_Cloud_Native_Falco_Wazuh

# 1. Cluster K3s
curl -sfL https://get.k3s.io | sh -

# 2. Falco (détection comportementale) via Helm
helm repo add falcosecurity https://falcosecurity.github.io/charts
helm install falco falcosecurity/falco --namespace falco --create-namespace

# 3. Wazuh (Manager + Indexer + Dashboard)
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
sudo bash ./wazuh-install.sh -a

# 4. Agent Wazuh sur le nœud K3s, puis intégration Falco → Wazuh
# (voir rapport/GUIDE_INSTALLATION.pdf, sections 5 et 6)
```

> ⚠️ Ce résumé est volontairement partiel. Les valeurs d'IP, de version de paquet et les blocs de configuration exacts sont dans le rapport — ne pas exécuter ces commandes sans l'avoir lu.

La présentation (`presentation/`) et le rapport (`rapport/`) sont des fichiers statiques (`.pptx`, `.pdf`) : aucune installation n'est nécessaire pour les consulter, il suffit de les ouvrir.

---

## 🔄 Contenu couvert

| Thème | Où le trouver |
|---|---|
| Contexte et menaces Cloud-Native | présentation, section Introduction |
| Présentation des outils (K3s, Falco, Wazuh) | présentation ; rapport, section 1 |
| Prérequis matériels et réseau | rapport, section 2 |
| Installation K3s | rapport, section 3 |
| Installation Falco | rapport, section 4 |
| Installation Wazuh (Manager, Indexer, Dashboard, Agent) | rapport, section 5 |
| Intégration Falco → Wazuh | rapport, section 6 |
| Test de bout en bout | rapport, section 7 |
| Dépannage | rapport, section 8 |
| Conclusion et bilan | présentation, section Conclusion ; rapport, section 9 |

---

## ⚠️ Avertissement méthodologique

Ce projet est une démonstration pédagogique, pas une architecture de production :

- l'installation décrite repose sur un nœud K3s unique et un serveur Wazuh à nœud unique (Manager, Indexer, Dashboard réunis) ;
- aucune haute disponibilité, ni durcissement réseau avancé n'est mis en œuvre ;
- l'intégration Falco → Wazuh utilise la lecture directe de fichier de log, une méthode simple mais moins robuste que Falcosidekick en production ;
- les règles d'Active Response fournies sont des exemples pédagogiques, à valider avant tout usage réel.

Voir `rapport/GUIDE_INSTALLATION.pdf` pour le détail des limites et des points de vigilance.

---

## 🚀 Perspectives d'amélioration

### Infrastructure

- passer à une architecture Wazuh distribuée (Manager, Indexer et Dashboard séparés) pour la haute disponibilité ;
- déployer K3s en cluster multi-nœuds plutôt qu'en nœud unique.

### Détection et réponse

- enrichir les règles Falco avec des règles personnalisées adaptées au contexte applicatif ;
- remplacer la lecture directe de logs par Falcosidekick pour une intégration plus robuste ;
- documenter et tester davantage de scénarios d'Active Response.

### Exploitation

- ajouter un tableau de bord de supervision (Wazuh Dashboard personnalisé ou Grafana) ;
- automatiser le déploiement complet via un pipeline d'infrastructure as code (Ansible, Terraform).

---

## 📚 Compétences mises en œuvre

- Sécurité des environnements Cloud-Native et Kubernetes
- Détection comportementale (Falco)
- SIEM / XDR et corrélation d'événements (Wazuh)
- Automatisation de la réponse à incident
- Rédaction de documentation technique
- Git / GitHub

---

## 👨‍🎓 Contexte académique

- **Formation :** Master 1, mention Objets Connectés et Cybersécurité (OCC)
- **Année universitaire :** 2025-2026

---

## 📌 Conclusion

Ce dépôt réunit une chaîne complète Détection → Collecte → Corrélation → Réponse, de la présentation du contexte et des outils jusqu'à une procédure d'installation reproductible et testée de bout en bout.

Sa valeur pédagogique repose sur la démonstration d'une démarche de sécurité opérationnelle complète — pas sur une architecture prête pour la production, dont les limites sont assumées et documentées plutôt que passées sous silence.

---

## 📄 Licence / usage

Projet académique. Usage pédagogique uniquement.
