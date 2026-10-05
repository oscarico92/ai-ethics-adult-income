# AI Ethics : Adult Income

Projet du cours *Ethics of AI* (ECE Paris, ING5 Data & IA) : rendre « éthique by design » un workflow d'IA classique.

**Cas d'usage :** pré-qualification automatisée d'une demande de crédit (revenu > 50 000 $ ?), système classé à haut risque par l'AI Act.

## Démarche

1. **Audit des biais** du dataset Adult Income par sexe, par origine et par **sexe × origine** (analyse intersectionnelle).
2. **Réduction des biais** par repondération Kamiran–Calders sur le groupe intersectionnel, plus une variante sans attributs sensibles dans l'entrée.
3. **Adaptation d'un modèle de langage** (DistilBERT) avec les trois méthodes du cours : fine-tuning complet, LoRA, distillation vers BERT-tiny.
4. **Benchmark** : F1 macro, taux de prédiction positive, écarts de parité et d'égalité des chances, temps, paramètres entraînés, taille sur disque.
5. **Choix du modèle retenu** selon un critère explicite (équité, puis performance, puis sobriété).
6. **Démo contrefactuelle** : même dossier, seuls le sexe et l'origine changent, avant / après correction.
7. **Explications** LIME et SHAP sur la démo.

## Exécution dans Google Colab

1. Ouvrir `Projet_Ethique_IA_Adult_Income_Colab.ipynb` dans Colab.
2. **Exécution → Modifier le type d'exécution → GPU T4**.
3. **Exécution → Tout exécuter**. Si Colab demande de redémarrer après l'installation, redémarrer puis relancer toutes les cellules.

Les figures et tableaux sont enregistrés dans `resultats_projet_ethique/`.

## Résultats

*À compléter après exécution : tableau du benchmark, modèle retenu, principaux écarts avant / après.*

## Équipe

*À compléter.*

## Avertissement éthique

Projet pédagogique uniquement. Les variables sensibles servent à mesurer les biais, pas à justifier une décision réelle concernant une personne.
