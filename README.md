# SIMILIS Data-Centric Summary

Основной ноутбук проекта: [similis_full.ipynb](similis_full.ipynb)

## Короткий итог

### Основные поля

В качестве главного поля для сравнения стратегий использовалось `name_norm`

В качестве вспомогательных полей использовались:
- `material_group`
- `fragm_norm`

### Экспериментальный протокол и split'ы

Использовались фиксированные group-aware split'ы:
- `train_seed` для начального обучения модели
- `val_gold` для выбора чекпоинта и честного сравнения стратегий
- `test_open` как открытый отложенный набор
- `pool_candidate` как пул кандидатов на доразметку и active learning симуляцию

Размеры split'ов:
- `train_seed`: 871 изображение, 461 группа
- `val_gold`: 203 изображения, 126 групп
- `test_open`: 155 изображений, 126 групп
- `pool_candidate`: 158 изображений, 126 групп

Пересечений по `group_key` между split'ами нет

Файлы split'ов:
- [splits/train_seed.csv](splits/train_seed.csv)
- [splits/val_gold.csv](splits/val_gold.csv)
- [splits/test_open.csv](splits/test_open.csv)
- [splits/pool_candidate.csv](splits/pool_candidate.csv)
- [splits/all_splits.csv](splits/all_splits.csv)

### Baseline

В качестве фиксированного baseline использовалась multi-task модель на основе `resnet18` с projection head и дообучением `layer4`

Главная метрика сравнения: `macro-F1` по полю `name_norm`

Baseline артефакты:
- [artifacts/checkpoints/baseline_best.pt](artifacts/checkpoints/baseline_best.pt)
- [artifacts/reports/task10_baseline_summary.csv](artifacts/reports/task10_baseline_summary.csv)
- [artifacts/reports/task11_summary.csv](artifacts/reports/task11_summary.csv)
- [artifacts/reports/task11_learning_curves.png](artifacts/reports/task11_learning_curves.png)
- [artifacts/reports/task11_confusion_matrix.png](artifacts/reports/task11_confusion_matrix.png)
- [artifacts/reports/task11_class_breakdown_full.csv](artifacts/reports/task11_class_breakdown_full.csv)

### Uncertainty-сигналы

Для ранжирования кандидатов считались:
- `max_probability`
- `entropy`
- `margin`

Артефакты:
- [artifacts/reports/task12_signal_definitions.csv](artifacts/reports/task12_signal_definitions.csv)
- [artifacts/reports/task12_signal_rankings.csv](artifacts/reports/task12_signal_rankings.csv)
- [artifacts/active_learning/task16_group_uncertainty_scores.csv](artifacts/active_learning/task16_group_uncertainty_scores.csv)

### Сравниваемые query-стратегии

Сравнивались три стратегии:
- `random`
- `highest_entropy`
- `hybrid_entropy_diversity`

Артефакты queried ids:
- [artifacts/active_learning/random_budget_50.csv](artifacts/active_learning/random_budget_50.csv)
- [artifacts/active_learning/random_budget_100.csv](artifacts/active_learning/random_budget_100.csv)
- [artifacts/active_learning/highest_entropy_budget_50.csv](artifacts/active_learning/highest_entropy_budget_50.csv)
- [artifacts/active_learning/highest_entropy_budget_100.csv](artifacts/active_learning/highest_entropy_budget_100.csv)
- [artifacts/active_learning/hybrid_budget_50.csv](artifacts/active_learning/hybrid_budget_50.csv)
- [artifacts/active_learning/hybrid_budget_100.csv](artifacts/active_learning/hybrid_budget_100.csv)

### Использованные бюджеты

В симуляции использовались два фиксированных бюджета:
- `50`
- `100`

### Победившая стратегия

Лучший результат показала стратегия `hybrid_entropy_diversity` при бюджете `100`

Ключевые метрики:
- baseline `macro-F1` = `0.6749`
- best `macro-F1` = `0.7436`
- улучшение относительно baseline = `+0.0687`
- улучшение относительно `random@100` = `+0.0553`

Сводные результаты:
- [artifacts/active_learning/task18_simulation_results.csv](artifacts/active_learning/task18_simulation_results.csv)
- [artifacts/reports/task19_results_table.csv](artifacts/reports/task19_results_table.csv)
- [artifacts/reports/task19_learning_curves.png](artifacts/reports/task19_learning_curves.png)
- [artifacts/reports/task19_query_composition.csv](artifacts/reports/task19_query_composition.csv)

### Главные выводы о качестве корпуса

1. Значимая доля ошибок связана не только с моделью, но и с качеством разметки и структуры корпуса
   В данных много конфликтующих `group_key` и uncertain-разметки, поэтому нормализация словарей и ручной review действительно влияют на итоговое качество

2. Сильный nuisance-эффект дают особенности карточек изображений
   `multi-view`, `close-up`, служебные подписи, scale bar и большой пустой фон заметно влияют на uncertainty и сложность примеров, поэтому такие флаги полезно хранить в metadata

3. Гибрид `uncertainty + diversity` полезнее чистого uncertainty
   Он уменьшает избыточность query и приносит в доразметку более разнообразные объекты, что дало лучший прирост при одинаковом бюджете

Поддерживающие артефакты:
- [artifacts/reports/task20_problem_map.csv](artifacts/reports/task20_problem_map.csv)
- [artifacts/reports/task20_curator_recommendations.csv](artifacts/reports/task20_curator_recommendations.csv)
- [artifacts/review/task14_review_table.csv](artifacts/review/task14_review_table.csv)

### Прикладываемые артефакты

Review-таблица:
- [artifacts/review/task14_review_table.csv](artifacts/review/task14_review_table.csv)
- [artifacts/review/task14_review_examples.csv](artifacts/review/task14_review_examples.csv)
- [artifacts/review/task14_review_examples.png](artifacts/review/task14_review_examples.png)

Queried ids:
- [artifacts/active_learning/random_budget_50.csv](artifacts/active_learning/random_budget_50.csv)
- [artifacts/active_learning/random_budget_100.csv](artifacts/active_learning/random_budget_100.csv)
- [artifacts/active_learning/highest_entropy_budget_50.csv](artifacts/active_learning/highest_entropy_budget_50.csv)
- [artifacts/active_learning/highest_entropy_budget_100.csv](artifacts/active_learning/highest_entropy_budget_100.csv)
- [artifacts/active_learning/hybrid_budget_50.csv](artifacts/active_learning/hybrid_budget_50.csv)
- [artifacts/active_learning/hybrid_budget_100.csv](artifacts/active_learning/hybrid_budget_100.csv)

Learning curves:
- [artifacts/reports/task11_learning_curves.png](artifacts/reports/task11_learning_curves.png)
- [artifacts/reports/task19_learning_curves.png](artifacts/reports/task19_learning_curves.png)

Лучший чекпоинт:
- [artifacts/checkpoints/sim_hybrid_entropy_diversity_budget_100.pt](artifacts/checkpoints/sim_hybrid_entropy_diversity_budget_100.pt)

Итоговый csv инференса:
- [artifacts/preds/final_pool_candidate_inference.csv](artifacts/preds/final_pool_candidate_inference.csv)

Дополнительно:
- [artifacts/reports/full_artifacts_index.json](artifacts/reports/full_artifacts_index.json)
