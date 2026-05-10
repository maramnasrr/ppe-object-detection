# PPE Vision — Détection d'Équipements de Protection Individuelle

---

# Auteurs

- Maram NASR
- Skander HAJ MABROUK

---

# Objectif du projet

L’objectif de ce projet est de développer un système de **détection d’objets** capable de reconnaître automatiquement plusieurs équipements de protection individuelle (EPI) dans des images industrielles.

Le modèle doit :

- localiser les objets via des bounding boxes
- classifier chaque équipement détecté
- fonctionner sur un dataset personnalisé créé from scratch

Le projet s’appuie sur une architecture de détection moderne :

- Faster R-CNN ResNet50 FPN V2
- Fine-tuning à partir de poids pré-entraînés sur COCO

---

# Classes détectées

Le dataset contient 6 classes :

| Classe | Description |
|---|---|
| casque | Safety Helmet |
| gilet | Reflective Vest |
| gants | Protective Gloves |
| goggles | Safety Goggles |
| boots | Safety Boots |
| masque | Safety Mask |

---

# Dataset

## 📌 Construction du dataset

Le dataset a été :

- collecté manuellement depuis des sources libres de droits
- annoté avec Roboflow
- exporté au format CSV compatible PyTorch

Chaque annotation contient :

- image
- largeur/hauteur
- classe
- coordonnées :
  - xmin
  - ymin
  - xmax
  - ymax

---

## Statistiques du dataset

### Répartition globale

| Split | Images | Ratio |
|---|---|---|
| Train | 124 | 70% |
| Validation | 27 | 15% |
| Test | 27 | 15% |
| Total | 178 | 100% |

---

### Distribution des classes (train)

| Classe | Boîtes |
|---|---|
| Boots | 27 |
| Gants | 23 |
| Masque | 22 |
| Goggles | 21 |
| Gilet | 21 |
| Casque | 17 |

---

# Défis du dataset

Le dataset présente plusieurs difficultés :

- Variabilité intra-classe
- Variabilité d’échelle
- Occlusions partielles
- Similarité entre certaines classes
- Faible nombre d’images
- Déséquilibre des classes

---

# Architecture choisie

## Faster R-CNN ResNet50 FPN V2

Architecture utilisée :

```python
fasterrcnn_resnet50_fpn_v2
```

Le modèle repose sur :

- Backbone : ResNet50
- FPN : Feature Pyramid Network
- RPN : Region Proposal Network
- ROI Head : Fast R-CNN Predictor

---

# Pipeline du modèle

## 1. Extraction de features

Le backbone ResNet50 extrait les représentations visuelles.

## 2. Feature Pyramid Network

Le FPN construit des features multi-échelles.

## 3. Region Proposal Network

Le RPN génère des régions candidates.

## 4. Classification finale

Les régions sont classifiées et raffinées.

---

# Fine-Tuning

Le modèle pré-entraîné sur COCO est adapté à nos 7 classes :

```python
NUM_CLASSES = 7
```

(6 classes + background)

---

# Hyperparamètres

## Expérience 1 — Baseline

| Paramètre | Valeur |
|---|---|
| Optimizer | SGD |
| Momentum | 0.9 |
| LR tête | 0.0005 |
| LR backbone | 0.00005 |
| Weight decay | 0.0005 |
| Scheduler | StepLR |
| Batch size | 2 |
| Epochs | 15 |

---

## Expérience 2 — MobileNetV3

Comparaison avec :

```python
fasterrcnn_mobilenet_v3_large_fpn
```

Résultat :

- moins performant
- val loss plus élevée
- backbone moins robuste

---

## Expérience 3 — Fine-tuning avancé

| Paramètre | Valeur |
|---|---|
| Scheduler | CosineAnnealingLR |
| LR initial | 0.0001 |
| Epochs | 20 |
| Batch size | 2 |

---

# Résultats

## Validation Loss

| Modèle | Best Val Loss |
|---|---|
| ResNet50 Baseline | 0.5698 |
| MobileNetV3 | 0.9531 |
| ResNet50 Fine-Tuned | 0.5523 |

---

# Résultats quantitatifs

## mAP@0.5

| Classe | AP@0.5 |
|---|---|
| Boots | 0.6355 |
| Gants | 0.6111 |
| Gilet | 0.5000 |
| Masque | 0.3611 |
| Casque | 0.0000 |
| Goggles | 0.0000 |

## mAP global

```text
mAP@0.5 = 0.3513
```

---

# Points forts du modèle

Le modèle détecte correctement :

- les boots
- les gants
- les gilets

Les objets bien visibles et peu occlus sont mieux détectés.

---

# Limites observées

Le modèle présente plusieurs limites :

- Overfitting
- Dataset réduit
- Déséquilibre des classes
- Confusions :
  - casque ↔ gilet
  - masque ↔ goggles

---

# Résultats qualitatifs

Le notebook d’inférence permet :

- d’afficher les bounding boxes
- de visualiser les scores
- de comparer ground truth et prédictions

---

# Installation

## 1. Cloner le projet

## 2. Installer les dépendances

```bash
pip install torch torchvision pycocotools
```

---

# Utilisation

## Entraînement

Ouvrir :

```bash
ObjectDetection_Train.ipynb
```

---

## Évaluation

Ouvrir :

```bash
ObjectDetection_Train.ipynb
```

---

## Inférence

Ouvrir :

```bash
ObjectDetection_BestModel.ipynb
```

---

# Résultats principaux

## Meilleur modèle

```text
Faster R-CNN ResNet50 FPN V2 Fine-Tuned
```

## Performances

```text
Best Validation Loss : 0.5523
mAP@0.5 : 0.3513
```

---

# Perspectives d’amélioration

Améliorations possibles :

- Ajouter davantage d’images
- Data augmentation
- Équilibrage des classes
- Utilisation de YOLOv8
- Test de DETR
- Backbone plus puissant
- Fine-tuning plus long
- Multi-scale training

---

# Références

## Papers

- Faster R-CNN
- ResNet
- Feature Pyramid Networks

## Librairies

- PyTorch
- Torchvision
- Roboflow

---

#  Conclusion

Ce projet met en œuvre une pipeline complète de détection d’objets :

- création d’un dataset personnalisé
- annotation
- fine-tuning
- évaluation quantitative
- analyse qualitative

Malgré un dataset limité, le modèle obtient des résultats encourageants sur plusieurs classes d’équipements de protection individuelle.
