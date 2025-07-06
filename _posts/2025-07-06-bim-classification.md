### 🏗️ BIM Object Classification Using 3D Features and Machine Learning

**Domain**: Construction / 3D Modeling  
**Technologies**: Python, scikit-learn, IFC/BIM, 3D geometry, Flask API  
**Duration**: 4 weeks  
**Status**: Personal project / technical proof of concept

---

#### 🎯 Context

In the construction industry, BIM (Building Information Modeling) files are essential for representing architectural elements in 3D. However, these objects (beams, columns, doors, etc.) are not always well-tagged in the source files — making downstream data usage, automation, or classification difficult.

This project tackles the issue by creating a machine learning pipeline to **automatically identify object types** based on their **3D characteristics**, and then **modify the BIM file** accordingly.

---

#### 💡 Objective

Build a pipeline that:
- Parses `.ifc` BIM files to extract 3D objects
- Computes geometric features (length, volume, etc.)
- Classifies objects using a trained ML model
- Modifies the BIM file to assign the correct object type
- Provides a lightweight API for batch classification

---

#### 🛠️ Technical Overview

1. **IFC Parsing**: Used Python libraries to open `.ifc` files and extract individual 3D objects.
2. **Feature Engineering**: For each object, calculated geometric features:
   - `length`, `width`, `height`
   - `volume`
   - `slenderness_ratio`, `compactness_ratio`, etc.
3. **Labeling**: Built a small labeled dataset by manually classifying known objects.
4. **Model Training**: Trained a RandomForest classifier (also tested SVM) to predict object type.
5. **File Update**: Based on predictions, updated the object types in the BIM structure.
6. **API Deployment**: Developed a simple Flask API for classifying new IFC files and returning results.

---

#### 📊 Example Features

| Feature               | Description                           |
|------------------------|----------------------------------------|
| `length`               | Longest object dimension               |
| `height`               | Smallest vertical dimension            |
| `volume`               | Total volume of the object             |
| `slenderness_ratio`    | Height/width (used to detect beams)    |
| `compactness_ratio`    | Volume / bounding box volume           |

---

#### ✅ Results

- **92% accuracy** on test data
- Successfully classified object types: `Beam`, `Column`, `Wall`, `Door`
- Modified IFC files with accurate semantic tags
- Lightweight REST API built for easy reuse in other workflows

---

#### 🖼️ Visuals

![Example of BIM classification](../assets/images/projects/bim_classification_example.png)

*(Example: Classifying a long, thin object as a beam)*

---

#### 🔗 Resources

- 🧠 [Full source code on GitHub](https://github.com/your-username/bim-object-classification)
- 🌐 Flask API to test classification on new BIM files
- 📄 Sample BIM files (original and updated)

---

#### 🚀 Future Improvements

- Add support for multi-label classification (e.g., structural + architectural)
- Integrate with Revit or Autodesk Forge APIs
- Automate labeling using clustering or AI-assisted tagging
- Build a front-end UI for easier use by non-technical stakeholders

---

This project demonstrates my ability to combine:
- 3D geometry analysis
- Feature engineering
- Machine learning modeling
- Real-world API deployment

in a technically complex, real-world scenario in the AEC (Architecture, Engineering, Construction) domain.
