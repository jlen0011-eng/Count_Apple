# Count_Apple
Overview
Count_Apple is a computer vision project that explores automated apple counting from tree images using PyTorch. It processes RGB images alongside corresponding depth images, uses depth information to suppress background pixels, and trains convolutional neural networks to predict apple counts.

The project compares a custom convolutional neural network (CNN) with a transfer-learning approach based on a pre-trained ResNet-18 model. It also evaluates predictions and exports results to an Excel spreadsheet.
Important limitation: The current notebook generates random synthetic count labels because verified apple-count annotations are not supplied by the pipeline. As a result, the reported metrics measure performance against synthetic labels, not real apple counts. The current results should be treated as a pipeline demonstration, not evidence of reliable real-world counting accuracy.

Project Goals
- Prepare summer RGB and depth images for model development.
- Match RGB images with their corresponding depth images.
- Apply depth-based masking to reduce background influence.
- Train and compare a baseline CNN and a ResNet-18 regression model.
- Evaluate predictions using regression loss metrics.
- Visualise sample predictions and export counts to Excel.
- Identify RGB/depth files that do not have matching counterparts.
Repository Contents
File	Description
Apple2210.ipynb	Main notebook containing dataset preparation, model training, evaluation, visualisation, and Excel export.
README.md	Project documentation.
LICENSE	Repository licence.


The image dataset is not included in the repository and must be made available separately.
Technologies Used
- Python
- PyTorch
- Torchvision
- NumPy
- OpenCV
- Pillow (PIL)
- Matplotlib
- Pandas
- Google Colab and Google Drive
Dataset
The notebook expects a dataset folder available in Google Drive at:
/content/drive/MyDrive/Colab_Datasets/Dataset
The current workflow searches for image files and selects files whose names contain Summer. It then separates the images into:
- Summer RGB: colour images, identified by RGB or color in the filename.
- Summer depth: depth images, identified by _D- or depth in the filename.
The notebook output reports 2,176 image files in total, including 1,088 summer images: 544 RGB images and 544 depth images.
Dataset preparation
The notebook copies images into a working directory.
The code currently uses a 75% training, 15% testing, and 10% validation split, with a fixed random seed for shuffling. RGB and depth images are split separately before matching, so the number of successfully matched pairs in each split can be much smaller than the number of images copied. For robust evaluation, matching pairs should ideally be established first and then split together to prevent data leakage and missing pairs.
Image pairing and depth masking
RGB and depth files are matched using the filename prefix before _Summer. The notebook converts RGB images to three-channel colour tensors and depth images to grayscale tensors. The depth map is normalised and multiplied by the RGB tensor to create a depth-gated RGB image.

Images are resized to 256 × 256 pixels for model input.
Models
1. Baseline CNN
The baseline AppleCountRegressionCNN uses convolutional layers with batch normalisation, ReLU activations, and max pooling. A fully connected regression head produces one continuous count prediction per image.
- Loss used during training: Mean Absolute Error (L1 loss)
- Optimiser: Adam
- Learning rate: 0.001
- Training epochs in the notebook: 6
2. Transfer learning with ResNet-18
The improved approach uses a ResNet-18 backbone with pre-trained ImageNet weights. Its final fully connected layer is replaced with a regression head that outputs one value. The training pipeline adds random horizontal flips and mild colour jitter to training images.
- Optimiser: AdamW
- Learning rate: 0.0001
- Weight decay: 0.0001
- Learning-rate scheduler: Cosine Annealing
- Training epochs in the notebook: 6
- Loss: L1 loss in the first transfer-learning experiment; MSE loss in the later experiment
The notebook also saves the model weights associated with the lowest validation loss in the later training section.
Setup and Usage
1. Open the notebook
Open Apple2210.ipynb in Google Colab or run it in a local Jupyter environment with the required packages installed.
2. Clone the repository (optional in Colab)
git clone https://github.com/jlen0011-eng/Count_Apple.git
cd Count_Apple
If the repository is cloned in Colab, confirm the working directory before running notebook cells. The notebook file should be in the directory containing Apple2210.ipynb.
3. Make the dataset available
For Google Colab, mount Google Drive and place the extracted image dataset at:
MyDrive/Colab_Datasets/Dataset
If using a different location, update dataset_path / extract_path in the notebook accordingly.
4. Install dependencies if needed
In a fresh Python environment, install the packages used by the notebook:
pip install torch torchvision numpy opencv-python pillow matplotlib pandas openpyxl
A working installation of PyTorch and Torchvision is required. For local execution, choose a PyTorch build compatible with your operating system and available hardware.
5. Run the notebook
Run the notebook cells in order to:
1. Load and inspect the dataset.
2. Select summer RGB and depth images.
3. Create training, validation, and test folders.
4. Match RGB/depth pairs.
5. Build PyTorch datasets and data loaders.
6. Train the baseline CNN.
7. Train and evaluate the ResNet-18 model.
8. Visualise predictions and export results.
9. Diagnose unmatched RGB/depth files.
The notebook currently uses Google Colab paths such as /content/.... Update these paths if running locally.
Results in the Current Notebook
The following values are from the recorded notebook run and are not validated real-world apple-count results.
Experiment	Recorded result
Baseline CNN test MAE	11.5165 apples
ResNet-18 test MAE (first transfer-learning experiment)	20.0752 apples
ResNet-18 validation MAE at the final epoch of the first experiment	24.4507 apples
Later ResNet-18 test MSE	373.5326


The notebook's target is a mean absolute error (MAE) of no more than 2 apples. This target was not achieved in the recorded experiments. The later training run was interrupted, so its training process did not complete all planned epochs.

Results are affected by the use of synthetic labels, a small number of matched validation and test pairs, and differences between the notebook's split and pairing procedures. They should not be used to compare real-world counting accuracy until these issues are addressed.
Exporting Predictions
The notebook exports predictions for the test set to:
/content/apple_counts.xlsx
The spreadsheet contains the tree ID, predicted apple count, synthetic label, and RGB image path, plus a total row. When running in Colab, download the file from the Colab file browser or copy it to Google Drive if you want to keep it.

Because the current labels are synthetic, the Synthetic Ground Truth Count column is not a verified count of apples on each tree.
Current Limitations and Recommended Next Steps
- Use real annotations: Replace randomly generated labels with manually verified apple counts for each tree image.
- Match before splitting: Pair RGB and depth images first, then split the pairs into train, validation, and test sets.
- Increase evaluation sample size: Check why the current split produces only a small number of matched validation and test pairs.
- Check pairing assumptions: Confirm that each filename prefix uniquely identifies the correct RGB/depth pair.
- Keep labels consistent: Ensure paired inputs retain their corresponding real count labels throughout data augmentation and training.
- Evaluate on held-out data: Report MAE and other suitable metrics against verified counts on an untouched test set.
- Improve reproducibility: Record package versions, random seeds, hardware, and final model settings.
- Save model weights: Save and document the best-performing model checkpoint so predictions can be reproduced without retraining.
Licence
See the LICENSE file for licence information.
