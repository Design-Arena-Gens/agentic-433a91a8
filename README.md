# MPIIGaze Gaze Estimation Project

Проект для оценки направления взгляда на основе датасета MPIIGaze, реализованный в формате Jupyter Notebook.

## 🎯 Описание

Этот проект реализует глубокое обучение для оценки направления взгляда (gaze estimation) на основе изображений глаз. Архитектура модели основана на работе GazeNet и статье "MPIIGaze: Real-World Dataset and Deep Appearance-Based Gaze Estimation".

## 🏗️ Структура проекта

```
.
├── gaze_estimation_mpigaze.ipynb  # Основной notebook с полной реализацией
├── requirements.txt                # Зависимости Python
├── README.md                       # Этот файл
├── checkpoints/                    # Сохраненные модели (создается автоматически)
└── MPIIGaze/                      # Директория для датасета (нужно скачать)
    └── Data/
        └── Normalized/
            ├── p00/
            ├── p01/
            └── ...
```

## 📦 Установка

1. Установите зависимости:
```bash
pip install -r requirements.txt
```

2. Скачайте датасет MPIIGaze:
   - Посетите: https://www.mpi-inf.mpg.de/departments/computer-vision-and-machine-learning/research/gaze-based-human-computer-interaction/appearance-based-gaze-estimation-in-the-wild
   - Скачайте "Normalized Data"
   - Распакуйте в папку `./MPIIGaze/Data/Normalized/`

## 🚀 Использование

1. Откройте Jupyter Notebook:
```bash
jupyter notebook gaze_estimation_mpigaze.ipynb
```

2. Выполните ячейки последовательно:
   - **Блок 1-2**: Импорт библиотек и конфигурация
   - **Блок 3**: Загрузка и предобработка данных
   - **Блок 4-5**: Определение архитектур моделей (LeNet и ResNet)
   - **Блок 6-7**: Функции потерь и обучения
   - **Блок 8-11**: Обучение модели
   - **Блок 12-15**: Визуализация и оценка результатов
   - **Блок 16-18**: Экспорт модели и инференс

## 🧠 Архитектура моделей

### LeNet Style
- Входные данные: (1, 36, 60) - изображения глаз в оттенках серого
- Сверточные слои: 20 и 50 фильтров
- Полносвязные слои: 500 нейронов
- Выход: 2D направление взгляда (θ, φ)

### ResNet Style (альтернатива)
- Pre-activation ResNet с 8 слоями
- Блоки остаточных связей
- Batch normalization
- Adaptive average pooling

## 📊 Ожидаемые результаты

Согласно статье MPIIGaze:
- **LeNet**: ~6.5° средняя угловая ошибка
- **ResNet-8**: ~5.7° средняя угловая ошибка

## 🔧 Конфигурация

Основные параметры в `config`:

```python
config = {
    'dataset_path': './MPIIGaze',
    'model_name': 'lenet',  # 'lenet' или 'resnet'
    'batch_size': 64,
    'num_epochs': 40,
    'learning_rate': 0.01,
    'lr_milestones': [20, 30],
    'test_person_id': 'p00',  # для leave-one-person-out валидации
}
```

## 📈 Метрики

- **Loss**: L2 (MSE) между предсказанными и истинными углами
- **Angular Error**: Угловая ошибка в градусах между векторами направления взгляда
- **Evaluation**: Leave-one-person-out cross-validation

## 🔍 Особенности реализации

1. **Загрузка данных**:
   - Поддержка формата .mat и .h5
   - Нормализация изображений
   - Data augmentation (Gaussian blur)

2. **Обучение**:
   - SGD оптимизатор с momentum
   - MultiStep learning rate scheduler
   - Автоматическое сохранение лучшей модели

3. **Визуализация**:
   - Графики обучения
   - Распределение ошибок
   - Примеры предсказаний

4. **Экспорт**:
   - TorchScript для продакшена
   - Функция инференса `predict_gaze()`

## 📝 Структура датасета MPIIGaze

```
MPIIGaze/
└── Data/
    └── Normalized/
        ├── p00/
        │   ├── day01.mat
        │   ├── day02.mat
        │   └── ...
        ├── p01/
        └── ...
```

Каждый .mat/.h5 файл содержит:
- `Data/data` или `image`: изображения глаз (N, 36, 60)
- `Data/label` или `gaze`: направление взгляда (N, 2)

## 🎓 Ссылки

- [MPIIGaze Dataset](https://www.mpi-inf.mpg.de/departments/computer-vision-and-machine-learning/research/gaze-based-human-computer-interaction/appearance-based-gaze-estimation-in-the-wild)
- [Original Paper](https://ieeexplore.ieee.org/document/8122058)
- [PyTorch MPIIGaze Implementation](https://github.com/hysts/pytorch_mpiigaze)
- [Gaze-Net Repository](https://github.com/yihuacheng/Gaze-Net)

## 📄 Лицензия

Этот проект предназначен для исследовательских и образовательных целей. Датасет MPIIGaze имеет собственную лицензию для некоммерческого использования.

## 🤝 Вклад

Проект основан на следующих работах:
- Zhang et al. "MPIIGaze: Real-World Dataset and Deep Appearance-Based Gaze Estimation"
- Реализация архитектур из pytorch_mpiigaze и Gaze-Net

## 🐛 Устранение неполадок

### Проблема: Датасет не найден
**Решение**: Убедитесь, что путь `config['dataset_path']` указывает на правильную директорию с данными

### Проблема: Out of memory
**Решение**: Уменьшите `batch_size` в конфигурации

### Проблема: Slow training
**Решение**: Убедитесь, что используется GPU (`torch.cuda.is_available()` должно вернуть `True`)

## ✅ Чеклист

- [x] Реализация архитектур LeNet и ResNet-8
- [x] Загрузка и предобработка данных MPIIGaze
- [x] Функция расчета угловой ошибки
- [x] Цикл обучения с валидацией
- [x] Визуализация результатов
- [x] Экспорт модели
- [x] Функция инференса
- [x] Документация

---

**Удачи в вашем проекте по оценке направления взгляда! 👁️**