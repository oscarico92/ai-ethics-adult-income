# AI Ethics : Adult Income

Projet du cours *Ethics of AI* (ECE Paris, ING5 Data & IA, Farès Fadili) : rendre « éthique by design » un workflow d'IA classique.

**Cas d'usage :** pré-qualification automatisée d'une demande de crédit (le revenu annuel dépasse-t-il 50 000 $ ?). L'évaluation de la solvabilité des personnes est un usage classé à haut risque par l'AI Act.

**Équipe :** Oscar Schwartz, Hugo Bassaget

## Contenu du dépôt

| Fichier | Description |
|---|---|
| `Projet_Ethique_IA_Adult_Income_Colab.ipynb` | Notebook complet (Google Colab, GPU T4) |
| `Projet_Ethique_IA_Adult_Income.pptx` | Présentation de 10 minutes, avec notes de l'orateur |

## Dataset

**Adult Income** (aussi appelé *Census Income*), extrait du recensement américain de **1994** par Barry Becker et Ronny Kohavi.

- Source d'origine : [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/2/adult) (Becker, B. & Kohavi, R., 1996, DOI [10.24432/C5XW20](https://doi.org/10.24432/C5XW20))
- Chargé dans le notebook via [OpenML](https://www.openml.org/d/1590) (`fetch_openml(name="adult", version=2)`), qui réunit les ~48 800 lignes du jeu d'origine
- Cible : revenu annuel `>50K` ou `<=50K`
- Variables utilisées : âge, catégorie d'emploi, diplôme, profession, heures par semaine, **sexe** et **origine déclarée** (variables sensibles, conservées pour l'audit)
- Échantillon stratifié de 3 000 personnes (sexe × cible), découpé en 2 250 pour l'entraînement et 750 pour le test

## Démarche

1. **Audit des biais** par sexe, par origine et par **sexe × origine** (analyse intersectionnelle).
2. **Réduction des biais** par repondération Kamiran–Calders sur le groupe sexe × origine (poids bornés entre 0,5 et 3), plus une variante sans sexe ni origine dans l'entrée.
3. **Adaptation d'un modèle de langage** (DistilBERT, lignes transformées en texte) avec les trois méthodes du cours : fine-tuning complet, LoRA, distillation vers BERT-tiny.
4. **Benchmark** : F1 macro, taux de prédiction positive, écarts de parité démographique et d'égalité des chances, temps, paramètres entraînés, taille sur disque.
5. **Choix du modèle retenu** selon un critère explicite (équité, puis performance, puis sobriété).
6. **Démo contrefactuelle** : même dossier, seuls le sexe et l'origine changent.
7. **Explications** avec LIME et SHAP.

## Résultats

### Audit des données

| Groupe | Taux de >50K |
|---|---|
| Hommes | 30,4 % |
| Femmes | 11,0 % |
| Hommes Asian-Pac-Islander | 40,0 % |
| Femmes noires | 5,7 % |

### Benchmark (jeu de test, 750 personnes, taux réel de >50K : 23,9 %)

| Modèle | Accuracy | F1 macro | Taux prédit >50K | Écart parité sexe | Écart parité sexe × origine | Paramètres entraînés | Taille |
|---|---|---|---|---|---|---|---|
| Full FT (original) | 81,5 % | 0,713 | 16,5 % | 0,248 | 0,257 | 67 M | 268 Mo |
| **Full FT (corrigé)** | **80,8 %** | **0,706** | **17,2 %** | **0,059** | **0,173** | **67 M** | **268 Mo** |
| LoRA (original) | 79,3 % | 0,684 | 17,3 % | 0,260 | 0,266 | 0,74 M | 3 Mo |
| LoRA (corrigé) | 76,9 % | 0,637 | 15,7 % | 0,043 | 0,141 | 0,74 M | 3 Mo |
| LoRA (corrigé, sans attributs) | 78,8 % | 0,656 | 14,1 % | 0,037 | 0,156 | 0,74 M | 3 Mo |
| Distillation (corrigée) | 80,1 % | 0,686 | 15,5 % | 0,051 | 0,178 | 4,4 M | 18 Mo |

**Modèle retenu : fine-tuning complet corrigé.** La repondération divise l'écart de parité par sexe par 4 pour moins d'un point d'accuracy, et ramène l'écart d'égalité des chances (sexe × origine) de 0,53 à 0,001.

### Démo contrefactuelle

Profil : 40 ans, secteur privé, Bachelors, Exec-managerial, 45 h/semaine.

| Profil | P(>50K) avant (LoRA original) | P(>50K) après (modèle retenu) |
|---|---|---|
| Homme blanc | 67,9 % | 70,0 % |
| Femme blanche | 36,7 % | 69,6 % |
| Homme noir | 67,4 % | 69,6 % |
| Femme noire | 36,5 % | 69,2 % |

Écart maximal entre profils : **31,4 points avant, 0,7 point après**. LIME et SHAP confirment que le mot « Female » était le facteur le plus négatif avant correction, et qu'il ne pèse plus après.

## Limites

- Une seule graine aléatoire et 750 exemples de test : les écarts entre modèles peuvent venir en partie du hasard.
- Les groupes d'origine non blancs sont petits dans le test (33 à 44 personnes), et l'écart par origine baisse peu avec LoRA (0,34 → 0,32).
- L'écart d'égalité des chances par origine n'est pas interprétable : un seul groupe atteint 10 vrais >50K dans le test.
- La démo compare le LoRA original au fine-tuning complet corrigé (deux architectures différentes).
- Adult Income est ancien, américain, et reflète des inégalités historiques. Une métrique d'équité plus faible ne rend pas le système juste.

## Exécution dans Google Colab

1. Ouvrir `Projet_Ethique_IA_Adult_Income_Colab.ipynb` dans Colab.
2. **Exécution → Modifier le type d'exécution → GPU T4**.
3. **Exécution → Tout exécuter**. Si Colab demande de redémarrer après l'installation, redémarrer puis relancer toutes les cellules.

Les figures et tableaux sont enregistrés dans `resultats_projet_ethique/`.

## Avertissement éthique

Projet pédagogique uniquement. Les variables sensibles servent à mesurer les biais, pas à justifier une décision réelle concernant une personne.
