# Named Entity Recognition System using CRF

## Overview
A Named Entity Recognition (NER) system built using Conditional Random Fields (CRF) with Gradio UI integration. The system identifies and classifies named entities in text such as persons, organizations, locations, and more.

## 🌟 Features
- 🤖 CRF-based NER model
- 📊 Comprehensive feature engineering
- 🔄 Multiple training algorithms (AP & LBFGS)
- 📈 Performance metrics and evaluation
- 🎯 Interactive web interface using Gradio
- 🔍 Support for multiple entity types
- 📱 Easy-to-use API

## 🏷️ Supported Entity Types
- Person (PER)
- Organization (ORG)
- Geographic Location (GEO)
- Time (TIM)
- Art (ART)
- Geopolitical Entity (GPE)
- Event (EVE)
- Nationality (NAT)

## 🛠️ Technical Requirements

### Dependencies
```
pandas
sklearn-crfsuite
scikit-learn
plotly
gradio
```

### Installation
```bash
pip install -r requirements.txt
```

## 📊 Dataset Structure

The system expects a CSV file with the following columns:
```
Sentence #   Word    POS     Tag
```

Example:
```
Sentence: 1  Thousands   NNS     O
Sentence: 1  of          IN      O
Sentence: 1  demonstrators NNS    O
```

## 🚀 Usage

### 1. Data Loading and Preprocessing
```python
import pandas as pd
from sklearn.model_selection import train_test_split

# Load dataset
df = pd.read_csv('ner_dataset.csv', encoding='latin1')

# Preprocess
df['Sentence #'] = df['Sentence #'].fillna(method='ffill')
sentences = df.groupby('Sentence #')['Word'].apply(list).values
tags = df.groupby('Sentence #')['Tag'].apply(list).values
```

### 2. Feature Engineering
```python
def word2features(sent, i):
    word = sent[i]
    features = {
        'bias': 1.0,
        'word.lower()': word.lower(),
        'word[-3:]': word[-3:],
        'word[-2:]': word[-2:],
        'word.isupper()': word.isupper(),
        'word.istitle()': word.istitle(),
        'word.isdigit()': word.isdigit(),
    }
    # Add contextual features
    if i > 0:
        word1 = sent[i-1]
        features.update({
            '-1:word.lower()': word1.lower(),
            '-1:word.istitle()': word1.istitle(),
            '-1:word.isupper()': word1.isupper(),
        })
    # ... [Additional feature code]
    return features
```

### 3. Model Training

#### Averaged Perceptron (AP)
```python
crf = sklearn_crfsuite.CRF(
    algorithm='ap',
    max_iterations=100,
    all_possible_transitions=False
)
```

#### LBFGS (Optimized)
```python
crf = sklearn_crfsuite.CRF(
    algorithm='lbfgs',
    max_iterations=450,
    c1=0.1,  # L1 regularization
    c2=0.1,  # L2 regularization
    all_possible_transitions=False
)
```

### 4. Prediction
```python
def predict_ner(sentence, model):
    words = sentence.split()
    features = sent2features(words)
    predicted_tags = model.predict_single(features)
    return list(zip(words, predicted_tags))
```

### 5. Gradio UI Integration
```python
import gradio as gr

iface = gr.Interface(
    fn=predict_ner,
    inputs=gr.Textbox(lines=2, placeholder="Enter a sentence..."),
    outputs=gr.JSON(),
    title="Named Entity Recognition (NER)",
    description="Enter a sentence to get NER tags.",
    examples=[["Barak Obama born in Hawaii"]]
)

iface.launch()
```

## 📈 Performance Metrics

### LBFGS Model Results
- F1 Score: 0.97
- Overall Accuracy: 97%

Entity-wise Performance:
```
Entity      Precision   Recall   F1-Score
O           0.99        0.99     0.99
B-per       0.84        0.82     0.83
I-per       0.85        0.89     0.87
B-org       0.80        0.71     0.75
B-geo       0.86        0.91     0.88
B-tim       0.93        0.88     0.90
```

## 🔧 Model Parameters

### Hyperparameters
- Algorithm: LBFGS/AP
- Max Iterations: 450 (LBFGS), 100 (AP)
- L1 Regularization (c1): 0.1
- L2 Regularization (c2): 0.1

## 🚧 Limitations
- Limited handling of unseen entities
- Sensitive to text formatting
- Requires proper sentence splitting
- Memory intensive for large datasets

## 🔄 Future Improvements
1. Deep learning integration (BERT, Transformer)
2. Multi-language support
3. Custom entity type addition
4. Batch processing capability
5. Model versioning and deployment

## 🐛 Troubleshooting

Common issues and solutions:

1. **Memory Issues**
   - Reduce batch size
   - Implement data streaming
   - Optimize feature extraction

2. **Performance Issues**
   - Tune hyperparameters
   - Add more relevant features
   - Increase training data

## 📚 References
1. CRF Suite Documentation
2. Sklearn-crfsuite Documentation
3. Gradio Documentation

## 🤝 Contributing
Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## 📄 License
[Specify your license here]

## 📞 Support
For issues or questions:
- Open an issue
- [Contact Information]
