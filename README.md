🌫️ Air Quality Index (AQI) Prediction for Indian Cities
🧭 Project Overview

This project predicts the Air Quality Index (AQI) for major Indian cities using machine learning and deep learning techniques.
The goal is to identify the key air pollutants that affect air quality and build a predictive model capable of estimating AQI based on pollutant concentrations.

Air pollution is a major environmental issue in India, causing serious health risks. This project uses data-driven modeling to forecast AQI and help policymakers, environmental authorities, and citizens understand pollution trends more effectively.

📊 Dataset Description

The dataset contains air pollutant concentration levels collected from different monitoring stations across multiple Indian cities.

Key features include:

🧪 PM2.5, PM10 — Particulate matter concentrations

🧪 NO₂, SO₂, CO, O₃, NH₃ — Gaseous pollutants

🏙️ City — Name of the city

🎯 AQI — Target variable (Air Quality Index)

Data Source:
Central Pollution Control Board (CPCB) and publicly available AQI datasets from Kaggle.

⚙️ Machine Learning Models Implemented

We applied and compared several regression algorithms to predict AQI values:

Model	Type	Description
Linear Regression	Baseline	Simple linear model for comparison
Random Forest Regressor	Ensemble (Bagging)	Combines multiple trees for better stability
Gradient Boosting Regressor	Ensemble (Boosting)	Builds trees sequentially to reduce residuals
AdaBoost Regressor	Ensemble (Boosting)	Focuses on harder-to-predict samples
XGBoost Regressor	Advanced Boosting	Highly optimized gradient boosting
LightGBM Regressor	Gradient Boosting	Faster and more efficient than traditional GBMs
CatBoost Regressor	Gradient Boosting	Handles categorical data efficiently
Naive Bayes Regression	Probabilistic	Used for comparison baseline
🤖 Deep Learning Model

A Feedforward Neural Network (FNN) was also built using TensorFlow/Keras to capture complex, non-linear relationships between pollutants and AQI.

Model Architecture:

Input layer (features: pollutant levels, city)

Dense (128 neurons, ReLU activation)

Dropout (0.2)

Dense (64 neurons, ReLU activation)

Output layer (1 neuron, Linear activation)

Optimizer: Adam

Loss: Mean Squared Error (MSE)

Epochs: 50, Batch Size: 32

📈 Model Evaluation

Models were compared based on R² Score, RMSE, and MAE.

Model	R²	RMSE	MAE
XGBoost	0.93	10.5	8.7
LightGBM	0.92	10.8	8.9
CatBoost	0.91	11.2	9.0
Random Forest	0.88	12.2	9.6
Deep Learning (MLP)	0.90	11.3	9.2
Linear Regression	0.78	15.4	12.0

✅ XGBoost delivered the highest predictive performance and was chosen as the final model.
✅ The Deep Learning model performed competitively, capturing complex feature interactions.

🔍 Feature Importance (XGBoost)

Feature importance analysis revealed:

PM2.5 and PM10 are the most influential pollutants in determining AQI.

Gaseous pollutants such as CO, NO₂, and O₃ also contribute significantly.

<p align="center"> <img src="your_image_link_here.png" alt="XGBoost Feature Importance" width="600"> </p>
🧠 Technologies Used

🐍 Python

📘 Pandas, NumPy, Scikit-learn

⚙️ XGBoost, LightGBM, CatBoost

🧠 TensorFlow / Keras (for Deep Learning)

📊 Matplotlib, Seaborn (for visualization)

☁️ Google Colab

💾 Joblib / Pickle (for saving models)

💾 Project Files
AQI_Prediction_India/
│
├── AQI_Prediction_India.ipynb       # Main Colab Notebook
├── cleaned_dataset.csv               # Preprocessed Dataset
├── best_xgboost_model.pkl            # Saved XGBoost Model
├── requirements.txt                  # Dependencies
└── README.md                         # Project Documentation

🚀 How to Run the Project

Clone the repository

git clone https://github.com/YourUsername/AQI_Prediction_India.git


Open the notebook in Google Colab or Jupyter Notebook

AQI_Prediction_India.ipynb


Run all cells sequentially to:

Train and evaluate models

View AQI prediction results

Generate feature importance plots

📊 Results Summary

✅ Best Model: XGBoost
✅ Top Features: PM2.5, PM10, CO, NO₂
✅ Accuracy: R² = 0.93
✅ Deep Learning model closely matched ensemble performance, proving neural networks’ potential in environmental prediction tasks.

🧭 Conclusion

This project demonstrates that machine learning and deep learning methods can effectively predict Air Quality Index (AQI) across Indian cities.
Particulate matter — particularly PM2.5 and PM10 — were identified as the primary pollutants influencing air quality.

By leveraging data-driven models, this approach can support:

Real-time AQI forecasting

Air pollution management

Policy planning and public health awareness

💡 Future Enhancements

Include meteorological features (temperature, humidity, wind speed)

Use LSTM networks for time-series forecasting

Build a real-time AQI prediction web app using Streamlit or Flask

Deploy the model via AWS / Google Cloud

👨‍💻 Author

Raghvendra Choubey
📧 raghvendra.choubey.stats@gmail.com



This project is released under the MIT License — feel free to use and modify it with proper attribution.

