# Thai Gaming Toxic Detection

โปรเจกต์นี้เป็นระบบตรวจจับข้อความ Toxic ในแชทเกมภาษาไทย โดยใช้ Machine Learning และ Sentence Embedding เพื่อจำแนกข้อความเป็น 2 ประเภท:

* `Normal`
* `Toxic`

โปรเจกต์นี้มีการทดลองเปรียบเทียบโมเดลหลายวิธี และมี Discord Bot Demo สำหรับตรวจข้อความใน Discord server

---

## Project Overview

ปัญหาหลักของแชทเกมภาษาไทยคือข้อความ Toxic มักไม่ได้ดูจากคำหยาบอย่างเดียว แต่ต้องดูบริบทของประโยคด้วย เช่น การด่า, การเหยียด, การกดดันเพื่อนร่วมทีม หรือข้อความที่มีเจตนาทำร้ายผู้เล่นคนอื่น

โปรเจกต์นี้จึงทดลองหลายวิธี ตั้งแต่ Keyword Baseline ไปจนถึง Sentence Embedding และเลือกโมเดลที่ให้ผลดีที่สุดบน clean unseen test set

---

## Dataset

### Main Dataset

ไฟล์:

```text
thai_gaming_toxic_dataset_v2_simple.csv
```

จำนวนข้อมูลทั้งหมด:

```text
1,100 ตัวอย่าง
```

แบ่งเป็น:

```text
Toxic   550
Normal  550
```

### Clean Unseen Test Dataset

ไฟล์:

```text
thai_gaming_toxic_unseen_test_clean_244.csv
```

จำนวนข้อมูลทั้งหมด:

```text
244 ตัวอย่าง
```

แบ่งเป็น:

```text
Toxic   122
Normal  122
```

ชุดทดสอบนี้ถูกลบข้อความที่ซ้ำกับ main dataset แล้ว เพื่อใช้วัดผลกับข้อมูลที่โมเดลไม่เคยเห็นจริง

---

## Best Model

โมเดลที่ให้ผลดีที่สุดคือ:

```text
Sentence Embedding MiniLM + KNN13
```

ผลลัพธ์บน clean unseen test set:

```text
Accuracy: 86.07%
Macro F1-score: 0.8607
```

Classification Report:

```text
              precision    recall  f1-score   support

      Normal       0.86      0.86      0.86       122
       Toxic       0.86      0.86      0.86       122

    accuracy                           0.86       244
   macro avg       0.86      0.86      0.86       244
weighted avg       0.86      0.86      0.86       244
```

---

## Model Comparison

| Method                                          | Accuracy | Macro F1 |
| ----------------------------------------------- | -------: | -------: |
| Keyword / Blacklist basic baseline              |   59.84% |   0.5211 |
| TF-IDF + Logistic Regression                    |   78.69% |   0.7790 |
| TF-IDF + Linear SVM                             |   80.74% |   0.8023 |
| Sentence Embedding MiniLM + Logistic Regression |   81.97% |   0.8187 |
| Sentence Embedding MiniLM + Linear SVM          |   82.79% |   0.8267 |
| Sentence Embedding MiniLM + Random Forest       |   81.97% |   0.8176 |
| Dense Layer + Fixed Seed                        |   82.79% |   0.8271 |
| Sentence Embedding MiniLM + KNN5                |   84.43% |   0.8443 |
| Sentence Embedding MiniLM + KNN9                |   85.66% |   0.8566 |
| Sentence Embedding MiniLM + KNN13               |   86.07% |   0.8607 |

---

## Why KNN13?

จากการทดลอง KNN หลายค่า พบว่า:

```text
KNN5   Accuracy 84.43%
KNN9   Accuracy 85.66%
KNN13  Accuracy 86.07%
```

KNN13 ทำงานได้ดีที่สุด เพราะใช้จำนวนเพื่อนบ้านมากพอที่จะลด noise จากข้อความตัวอย่างบางตัว และยังใช้จำนวนคี่ จึงช่วยลดปัญหาการโหวตเสมอกัน

โมเดลใช้ cosine distance เพราะเหมาะกับ sentence embedding vector มากกว่า Euclidean distance

---

## Files

| File                                           | Description                             |
| ---------------------------------------------- | --------------------------------------- |
| `thai_gaming_toxicity_detector_all_methods.py` | โค้ดทดลองเปรียบเทียบโมเดลทั้งหมด        |
| `thai_gaming_toxicity_detector_final_model.py` | โค้ดโมเดลสุดท้าย KNN13                  |
| `discord_bot.py`                               | Discord Bot สำหรับตรวจข้อความใน server  |
| `knn13_error_analysis.csv`                     | Error Analysis ของโมเดล KNN13           |
| `thai_gaming_toxic_dataset_v2_simple.csv`      | Main dataset                            |
| `thai_gaming_toxic_unseen_test_clean_244.csv`  | Clean unseen test dataset               |
| `requirements.txt`                             | รายชื่อ Python libraries ที่ต้องติดตั้ง |

---

## Installation

ติดตั้ง dependencies:

```bash
pip install -r requirements.txt
```

หรือถ้าติดตั้งเอง:

```bash
pip install pandas numpy scikit-learn sentence-transformers discord.py
```

---

## Run Final Model

```bash
python thai_gaming_toxicity_detector_final_model.py
```

---

## Discord Bot Demo

โปรเจกต์นี้มี Discord Bot สำหรับตรวจข้อความ Toxic ใน Discord server

### Important

ก่อนรันบอท ให้ไปที่ Discord Developer Portal แล้วเปิด:

```text
Bot > Privileged Gateway Intents > Message Content Intent
```

และ invite bot เข้า server พร้อม permissions:

```text
View Channels
Send Messages
Read Message History
Moderate Members
```

ถ้าต้องการให้บอท timeout สมาชิกเมื่อเจอข้อความ Toxic ต้องให้ role ของบอทอยู่สูงกว่า role ของสมาชิกที่ต้องการจัดการ

---

## Discord Token Setup

ในไฟล์ `discord_bot.py` ให้ใส่ Discord Bot Token ของตัวเองตรงนี้:

```python
DISCORD_TOKEN = "ใส่ Discord Bot Token ของตัวเองตรงนี้"
```


---

## Run Discord Bot

```bash
python discord_bot.py
```

ถ้าสำเร็จ จะเห็นข้อความประมาณนี้:

```text
Logged in as Thai Gaming Toxic Detector#9612
Bot is ready and listening to messages.
```

จากนั้นพิมพ์ข้อความใน Discord server ได้เลย

ตัวอย่าง:

```text
เล่นกากมาก ทีมพังหมด
```

บอทจะตรวจข้อความและตอบผลลัพธ์ เช่น:

```text
Checked: Toxic
Normal: 15.38% | Toxic: 84.62%
```

ถ้าเปิดระบบ timeout ไว้ และข้อความเข้าเงื่อนไข Toxic บอทจะ timeout ผู้ส่งชั่วคราว

---

## Error Analysis

ไฟล์:

```text
knn13_error_analysis.csv
```

ใช้สำหรับวิเคราะห์ข้อความที่โมเดลทายผิด เช่น:

* False Positive: ข้อความจริงเป็น Normal แต่โมเดลทายว่า Toxic
* False Negative: ข้อความจริงเป็น Toxic แต่โมเดลทายว่า Normal
* Uncertain Cases: ข้อความที่โมเดลไม่มั่นใจสูง

Error Analysis ช่วยให้เห็นว่าโมเดลยังสับสนกับข้อความแบบไหน และสามารถนำไปปรับปรุง dataset ต่อได้

---

## จุดเด่น

จากการวิเคราะห์ dataset พบว่า:

* Dataset มีความสมดุลระหว่าง Normal และ Toxic
* ความยาวข้อความของ Normal และ Toxic ใกล้เคียงกันมาก จึงใช้ความยาวแยก class ไม่ได้
* คำบางคำพบได้ทั้ง Normal และ Toxic เช่น “เล่น”, “ทีม”, “ไม่”, “มึง”
* Keyword อย่างเดียวไม่เพียงพอสำหรับงานนี้
* Sentence Embedding ช่วยให้โมเดลเข้าใจบริบทของข้อความได้ดีกว่า TF-IDF และ Keyword Baseline

---

## ข้อจำกัด

โมเดลนี้ยังเป็น prototype และยังมีข้อจำกัด เช่น:

* Dataset มีขนาดไม่ใหญ่มาก
* ข้อความ Toxic บางแบบต้องอาศัยบริบทของบทสนทนาก่อนหน้า
* KNN probability เป็นสัดส่วนการโหวตของเพื่อนบ้าน ไม่ใช่ calibrated probability
* โมเดลอาจมี False Positive หรือ False Negative ได้
* ไม่ควรใช้ลงโทษผู้ใช้จริงโดยไม่มี human review ในระบบ production

---

## แนวทางการพัฒนา

แนวทางพัฒนาต่อ:

* เพิ่ม dataset จากข้อความจริงที่หลากหลายขึ้น
* เพิ่ม label ย่อย เช่น Insult, Harassment, Threat, Spam
* ทดลอง fine-tune transformer model
* เพิ่ม confidence threshold ก่อนลงโทษ
* เพิ่มระบบ human review
* ทำ slash command `/check`
* Deploy bot บน cloud server แทน Colab
* เพิ่ม logging dashboard สำหรับ admin

---


