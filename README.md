# Face-recognition

*L'objet de ce dépôt est de partager une tentative de programme permettant de reconnaitre les visages des personnes sur une video.*
_Il s'agit d'un cas d'usage du package [face_recognition](https://github.com/ageitgey/face_recognition?tab=readme-ov-file)._

## Premier objectif : repérer un visage sur une image

``` python
import face_recognition
import cv2
import matplotlib.pyplot as plt

# Charger l'image
image_path = r"your_file_direction.jpg"
image = face_recognition.load_image_file(image_path)

# Détecter les positions des visages
face_locations = face_recognition.face_locations(image)

# Charger l'image dans un format compatible avec OpenCV (RGB à BGR)
image_cv2 = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)

# Dessiner des rectangles autour de chaque visage détecté
for face_location in face_locations:
    top, right, bottom, left = face_location
    # Dessiner un rectangle vert
    cv2.rectangle(image_cv2, (left, top), (right, bottom), (0, 255, 0), 2)
    

# Afficher l'image avec matplotlib
plt.imshow(cv2.cvtColor(image_cv2, cv2.COLOR_BGR2RGB))  # Convertir en RGB pour l'affichage correct
plt.axis('off')  # Cacher les axes
plt.show()
```

![Capture d’écran 2024-11-11 135759](https://github.com/user-attachments/assets/b2554c18-52fd-45a2-9f90-68a38ce811dd)

## Deuxième objectif : vérifier la correspondance d'un visage

``` python

import matplotlib.image as mpimg

# Chemin de l'image
image_path = r"your_file_directory_unknown1"

# Charger et afficher l'image
img = mpimg.imread(image_path)
plt.imshow(img)
plt.axis('off')  # Masquer les axes
plt.show()

import face_recognition

picture_of_ronan = face_recognition.load_image_file(r"your_file_directory")
ronan_face_encoding = face_recognition.face_encodings(picture_of_ronan)[0]

# my_face_encoding contient désormais un « encodage » universel des traits du visage qui peut être comparé à n'importe quelle autre photo de visage !

unknown_picture = face_recognition.load_image_file(r"your_file_directory_unknown1") 
unknown_face_encoding = face_recognition.face_encodings(unknown_picture)[0]

# Maintenant nous pouvons voir que les deux encodages de visage sont de la même personne avec `compare_faces` !

results = face_recognition.compare_faces([ronan_face_encoding], unknown_face_encoding)

if results[0] == True:
    print("C'est Ronan !")
else:
    print("Ce n'est pas Ronan !")
```

![Capture d’écran 2024-11-11 140629](https://github.com/user-attachments/assets/20ee3eb1-862b-419e-ae8e-f54cc8c0983e)


## Troisième objectif : repérer un visage sur une vidéo

Cette étape a été plus difficile notamment en raison de la problématique des ressources de calcul nécessaires pour faire tourner le code. La solution proposée consiste à faire la reconnaissance faciale _frame_ par _frame_ puis réagreger le résultat pour en faire une vidéo dans laquelle les visages sont repérés. 

``` python
# Repérer les visages dans une vidéo
import face_recognition
import cv2

# Chemin de la vidéo
video_path = r"your_file_directory"
video_capture = cv2.VideoCapture(video_path)

# Obtenir les dimensions et la fréquence de la vidéo
frame_width = int(video_capture.get(cv2.CAP_PROP_FRAME_WIDTH))
frame_height = int(video_capture.get(cv2.CAP_PROP_FRAME_HEIGHT))
fps = int(video_capture.get(cv2.CAP_PROP_FPS))

# Définir le codec et créer un objet VideoWriter pour enregistrer la vidéo de sortie
output_path = r"your_file_destination_directory.avi"
fourcc = cv2.VideoWriter_fourcc(*'XVID')
output_video = cv2.VideoWriter(output_path, fourcc, fps, (frame_width, frame_height))

# Lire chaque frame de la vidéo
while video_capture.isOpened():
    ret, frame = video_capture.read()
    if not ret:
        break  # Sortir de la boucle si la vidéo est terminée

    # Convertir la frame en RGB (face_recognition utilise RGB, OpenCV utilise BGR)
    rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    # Détection des visages
    face_locations = face_recognition.face_locations(rgb_frame)

    # Dessiner des rectangles autour des visages détectés
    for (top, right, bottom, left) in face_locations:
        cv2.rectangle(frame, (left, top), (right, bottom), (0, 255, 0), 2)

    # Ajouter la frame avec les visages détectés à la vidéo de sortie
    output_video.write(frame)

    # Afficher la frame avec les visages détectés (facultatif pour l'enregistrement)
    cv2.imshow("Video", frame)

    # Quitter la vidéo si 'q' est pressé
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# Libérer les ressources
video_capture.release()
output_video.release()  # Ne pas oublier de libérer l'objet d'enregistrement vidéo
cv2.destroyAllWindows()
```


https://github.com/user-attachments/assets/7e6b34a7-7878-4690-bbe4-e24a53df515a


## Quatrième objectif : repérer Ronan dans une vidéo


**NB** : L'importation du package *face_recognition* a été laborieuse car (i) il nécessite le package *dlib* qui (ii) nécessite le package *CMake* qui (iii) nécessite un compilateur C++ pour être exécuté .
Il a donc fallu télécharger un compilateur C++ puis run _CMake_ et _dlib_. Ensuite il a fallu integrer le Kernel de jupyter notebook dans le PATH qui contenait dlib afin de pouvoir importer le package *face_recognition*.
Plus d'informations sur les usages et conditions d'installation de _dlib_ [ici](http://dlib.net/). 
