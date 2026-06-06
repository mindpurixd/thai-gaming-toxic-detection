# Thai Gaming Toxic Detection
### โปรเจกต์นี้ใช้โมเดล Machine Learning ในการตรวจจับข้อความท็อกซิกในแชทเกมไทย
## Dataset
- Main dataset: 1,100 ตัวอย่าง = `thai_gaming_toxic_dataset_v2_simple.csv`
  - Toxic: 550
  - Normal: 550
- Clean unseen test: 244 คัวอย่าง = `thai_gaming_toxic_unseen_test_clean_244.csv`
  - Toxic: 122
  - Normal: 122
  
  ## Files

- `thai_gaming_toxicity_detector_all_methods.py`  
  รันการทดลองเปรียบเทียบโมเดลทั้งหมด

- `thai_gaming_toxicity_detector_final_model.py`  
  รันการทดลองเปรียบเทียบโมเดลตัวสุดท้าย (KNN13)

- `knn13_error_analysis.csv`  
  Error Analysis ของโมเดลสุดท้าย (KNN13)

## Best Model

Sentence Embedding MiniLM + KNN13

- Accuracy: 86.07%
- Macro F1-score: 0.86

## โมเดลอื่นๆที่ทดลอง

1. Keyword / Blacklist baseline
2. TF-IDF + Logistic Regression
3. TF-IDF + Linear SVM
4. Sentence Embedding MiniLM + Logistic Regression
5. Sentence Embedding MiniLM + Linear SVM
6. Sentence Embedding MiniLM + Random Forest
7. Dense Layer + Fixed Seed
8. Sentence Embedding MiniLM + KNN5
9. Sentence Embedding MiniLM + KNN9
10. Sentence Embedding MiniLM + KNN13
