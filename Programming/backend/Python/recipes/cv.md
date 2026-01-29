#python #recipes #package

#### 🔹 Система распознавания лиц

```python
import cv2
import dlib
from imutils import face_utils

# Загрузка изображения
image = cv2.imread('example.jpg')

# Создание детектора лиц dlib
detector = dlib.get_frontal_face_detector()

# Обнаружение лиц на изображении
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
faces = detector(gray)

# Отображение результатов
for rect in faces:
  (x, y, w, h) = face_utils.rect_to_bb(rect)
  cv2.rectangle(image, (x, y), (x + w, y + h), (0, 255, 0), 2)

cv2.imshow("Faces", image)
cv2.waitKey(0)
cv2.destroyAllWindows()

# Загрузка предобученной модели
predictor = dlib.shape_predictor("shape_predictor_68_face_landmarks.dat")

# Извлечение признаков лица
for rect in faces:
  shape = predictor(gray, rect)
  shape = face_utils.shape_to_np(shape)
  for (x, y) in shape:
    cv2.circle(image, (x, y), 2, (0, 0, 255), -1)

cv2.imshow("Landmarks", image)
cv2.waitKey(0)
cv2.destroyAllWindows()

import face_recognition

# Загрузка изображения известного лица
known_image = face_recognition.load_image_file("known_person.jpg")
known_encoding = face_recognition.face_encodings(known_image)[0]

# Загрузка изображения для распознавания
unknown_image = face_recognition.load_image_file("unknown_person.jpg")
unknown_encoding = face_recognition.face_encodings(unknown_image)[0]

# Сравнение лиц
results = face_recognition.compare_faces([known_encoding], unknown_encoding)  

if results[0]:
  print("Это лицо принадлежит известному человеку!")
else:
  print("Это неизвестное лицо.")
```
---