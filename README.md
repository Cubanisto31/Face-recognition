# Face-recognition

*L'objet de ce dépôt est de partager une tentative de programme permettant de reconnaitre les visages des personnes sur une video.*

1) Le first commit a permis de partager le premier programme fonctionnel qui encadre le visage de Ronan (visage dans Ronan.jpg) avec un carré vert.

2) Le résultat :
  
   ![output_0_0](https://github.com/user-attachments/assets/fd77e652-7f49-4682-8e9c-b1e748c400dd)

3) Le second commit a permis de partage le programme qui permet de verifier si une le visage d'une photo est bien celui de Ronan ou pas

4) Le résultat

   
**NB** : L'importation du package *face_recognition* a été laborieuse car (i) il nécessite le package *dlib* qui (ii) nécessite le package *CMake* qui (iii) nécessite un compilateur C++ pour tourner.
Il a donc fallu télécharger un compilateur C++ puis run CMake et dlib. Ensuite il a fallu integrer le Kernel de jupyter notebook dans le PATH qui contenait dlib afin de pouvoir importer le package *face_recognition*.
