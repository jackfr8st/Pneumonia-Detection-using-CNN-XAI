# Pneumonia Detection using CNN

A deep learning model for pneumonia detection from chest X-ray images using Convolutional Neural Networks. Features include model training, visualization, and explainability techniques (LIME and GradCAM) to understand model predictions.

## Architecture Overview

```python
Model Architecture:

1. CNN Layers:
   ├── Conv2D (32 filters, 3x3 kernel) + ReLU
   ├── MaxPooling2D (2x2)
   ├── Conv2D (64 filters, 3x3 kernel) + ReLU
   ├── MaxPooling2D (2x2)
   ├── Conv2D (128 filters, 3x3 kernel) + ReLU
   ├── MaxPooling2D (2x2)
   └── Flatten

2. Dense Layers:
   ├── Dense (128 neurons) + ReLU
   ├── Dropout (p=0.2)
   └── Dense (1 neuron) + Sigmoid
```

## Key Features

- Binary classification of chest X-rays (Normal vs. Pneumonia)
- Data augmentation for improved model generalization
- Explainable AI techniques:
  - LIME (Local Interpretable Model-agnostic Explanations)
  - GradCAM (Gradient-weighted Class Activation Mapping)
- Model visualization and performance monitoring
- GPU-accelerated training

## Performance Metrics

- Training accuracy: 95%+ (after 10 epochs)
- Validation accuracy: 90%+
- Binary cross-entropy loss minimization
- Visualized learning curves

## Quick Start

```bash
# Install required dependencies
pip install tensorflow lime opencv-python matplotlib

# Load the pre-trained model
from tensorflow.keras.models import load_model
model = load_model('pneumonia_detection_model.h5')

# Make predictions on new images
from tensorflow.keras.preprocessing import image
import numpy as np

img_path = 'path/to/chest_xray.jpeg'
img = image.load_img(img_path, target_size=(128, 128))
img_array = image.img_to_array(img) / 255.0
img_array = np.expand_dims(img_array, axis=0)

prediction = model.predict(img_array)
result = "PNEUMONIA" if prediction[0][0] > 0.5 else "NORMAL"
```

## Training Pipeline

```python
# Example training configuration
training_config = {
    'batch_size': 32,
    'num_epochs': 10,
    'image_size': (128, 128),
    'optimizer': 'adam',
    'loss': 'binary_crossentropy',
    'data_augmentation': True
}
```

## Example Usage

```python
# Generate explanation for model prediction
from lime import lime_image
from skimage.segmentation import mark_boundaries

# Run LIME analysis
def explain_prediction_with_lime(image_path, model):
    # Load and preprocess image
    img = image.load_img(image_path, target_size=(128, 128))
    img_array = image.img_to_array(img) / 255.0
    
    # Create explainer
    explainer = lime_image.LimeImageExplainer()
    
    # Get explanation
    explanation = explainer.explain_instance(
        img_array, 
        model_predict,  # Custom prediction function
        top_labels=2, 
        hide_color=0, 
        num_features=100000,
        num_samples=1000
    )
    
    # Visualize explanation
    temp, mask = explanation.get_image_and_mask(
        explanation.top_labels[0], 
        positive_only=True, 
        num_features=5, 
        hide_rest=False
    )
    
    # Display results
    plt.figure(figsize=(15, 6))
    plt.subplot(1, 3, 1)
    plt.imshow(img)
    plt.title('Original Image')
    
    plt.subplot(1, 3, 2)
    plt.imshow(mark_boundaries(temp, mask))
    plt.title('LIME Explanation')
    
    plt.subplot(1, 3, 3)
    # Display heatmap
    # ...
    
    plt.tight_layout()
    plt.show()
```

## Dataset Format

The model is trained on the Chest X-Ray Images (Pneumonia) dataset with the following structure:
```
chest_xray/
├── train/
│   ├── NORMAL/
│   └── PNEUMONIA/
├── val/
│   ├── NORMAL/
│   └── PNEUMONIA/
└── test/
    ├── NORMAL/
    └── PNEUMONIA/
```

## Model Architecture Details

CNN Architecture:
```python
model = Sequential([
    Conv2D(32, (3,3), activation='relu', input_shape=(128,128,3)),
    MaxPooling2D((2,2)),
    Conv2D(64, (3,3), activation='relu'),
    MaxPooling2D((2,2)),
    Conv2D(128, (3,3), activation='relu'),
    MaxPooling2D((2,2)),
    Flatten(),
    Dense(128, activation='relu'),
    Dropout(0.2),
    Dense(1, activation='sigmoid')
])
```

## Explainability Features

1. LIME Explanation:
   - Highlights regions that contribute to classification decision
   - Shows feature importance heatmap
   - Provides probability distribution for classes

2. GradCAM Visualization:
   - Generates class activation maps
   - Shows which regions activate neurons
   - Creates heat overlay on original image


## Dependencies

- TensorFlow 2.x
- NumPy
- Pandas
- Matplotlib
- LIME
- OpenCV
- scikit-image
