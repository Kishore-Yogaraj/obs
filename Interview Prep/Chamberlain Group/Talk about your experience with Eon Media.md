During my time at Eon Media, I worked as an AI/ML developer, primarily focusing on integrating machine learning models into their media platform. One of the key projects I worked on was building a **facial recognition system** using **FaceNet**. This involved training the model to recognize and verify faces with high accuracy. I was responsible for the full pipeline—everything from preprocessing the images and training the model, to deploying it within the platform.

In addition to facial recognition, I worked on optimizing and fine-tuning other AI models to improve the platform’s ability to analyze and categorize large amounts of media content. This experience not only sharpened my skills in machine learning but also exposed me to the challenges of integrating AI models into real-world, production-level systems.

One of the key things I learned from my experience at Eon Media was the importance of balancing model performance with scalability. Working in a fast-paced media environment required our models to run efficiently on large datasets without sacrificing accuracy. It was a great learning experience in applying machine learning in a real-world setting, and I gained valuable insights into building models that can be scaled and deployed in production.

### How Does FaceNet Work
FaceNet is a deep learning model used for facial recognition and verification, but it works differently from traditional classification models. Instead of simply classifying faces, FaceNet maps each face into a **high-dimensional embedding space**, where the distance between embeddings represents the similarity between faces.

Here’s how it works:

- **Embedding Generation**: When an image of a face is passed through FaceNet, it doesn’t just classify the face; instead, it generates a 128-dimensional vector called an **embedding**. This embedding captures the unique features of the face.
    
- **Distance-Based Comparison**: Once the embeddings are created, the similarity between two faces is measured by calculating the **Euclidean distance** between their embeddings. If the distance is small, the faces are similar (i.e., the same person); if the distance is large, they belong to different people.
    
- **Training with Triplet Loss**: FaceNet is trained using a technique called **triplet loss**. During training, the model is given three types of images:
    
    1. **Anchor**: the reference face.
    2. **Positive**: a face of the same person as the anchor.
    3. **Negative**: a face of a different person.
    
    The goal is to minimize the distance between the anchor and positive embeddings while maximizing the distance between the anchor and negative embeddings. This helps the model learn to cluster faces of the same person closely together in the embedding space and push faces of different people further apart.
    

By using this embedding-based approach, FaceNet is not only great for recognizing who a person is but also for verifying whether two faces belong to the same person. This makes it highly effective for tasks like facial verification, clustering, and identification