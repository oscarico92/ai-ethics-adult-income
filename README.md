# AI Ethics — Adult Income

Projet d’éthique de l’IA consacré à l’audit et à la réduction des biais dans la prédiction du revenu annuel à partir du jeu de données **Adult Income**.

## Objectif

Comparer trois méthodes d’adaptation d’un modèle de langage pour une tâche de classification tabulaire transformée en texte :

- fine-tuning complet de DistilBERT ;
- LoRA ;
- distillation vers un modèle plus compact.

Le projet mesure les écarts entre groupes selon le sexe et l’origine déclarée, applique une repondération des données et explique les décisions grâce à **LIME** et **SHAP**.

## Exécution dans Google Colab

1. Ouvrir `Projet_Ethique_IA_Adult_Income_Colab.ipynb`.
2. Sélectionner **Exécution → Modifier le type d’exécution → GPU T4**.
3. Lancer **Exécution → Tout exécuter**.
4. Si Colab redémarre après l’installation, relancer toutes les cellules.

Durée indicative : 15 à 30 minutes selon le GPU disponible.

## Résultats

Le notebook produit un audit des biais initiaux, un benchmark fine-tuning/LoRA/distillation, une comparaison avant/après repondération et des explications LIME et SHAP.

## Avertissement éthique

Projet pédagogique uniquement. Les variables sensibles servent à mesurer les biais, pas à justifier une décision réelle concernant une personne.
