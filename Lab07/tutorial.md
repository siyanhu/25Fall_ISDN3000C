# Lab 07: White Box AI

Build an intelligent toaster system that can predict the optimal toasting time for bread based on various features using explainable machine learning models.

## Submission

1. It is a **`group`** assignment. One submission per group.
2. Fork Github repository [ISDN3000C_Lab07_template](https://github.com/siyanhu/ISDN3000C_Lab07_template).
3. You may choose to work in Google Colab or on your local computer.
4. Submit the forked Github link to Canvas.

### Option A: Using Google Colab

Google Colab provides a free, cloud-based Jupyter Notebook environment that requires no local installation.

1.  **Open Colab:** Go to [colab.research.google.com](https://colab.research.google.com).
2.  **Open Notebook from GitHub:**
    *   In the pop-up window, click on the **GitHub** tab. The first time you do this, you'll need to grant Colab permission to post to your GitHub account.
    *   Paste the URL of **your forked repository** into the search bar and press Enter.
    *   A list of notebooks will appear. Click on `ISDN3000C_Lab07.ipynb` to open it.
3.  **Install Libraries:** Most libraries are pre-installed on Colab. However, `xgboost` might need to be installed. Add a new code cell at the top of the notebook and run the following command:
    ```
    !pip install plotly
    !pip install scikit-learn   
    !pip install xgboost
    ```
4.  **Backup Your Work (Optional):** As you work, save your progress by going to **File > Save a copy in Drive**. This ensures you don't lose your work.
5.  **Save Your Work to Github:** Make sure you have completed and run all the cells in your notebook. And do the following steps:
    1.  Go to **File > Save a copy in GitHub**.
    2.  A dialog box will appear. Configure it as follows:
        *   **Repository:** Use the dropdown menu to select your forked repository (e.g., `<YOUR_USERNAME>/ISDN3000C_Lab07.git`).
        *   **File path:** Ensure the file name is `ISDN3000C_Lab07.ipynb`. **Do not change this.**
        *   **Commit message:** This is a short description of the changes you made.
        *   Make sure the "Include a link to Colaboratory" checkbox is **checked**. This is helpful for your further checkup.

### Option B: Running Locally

1.  **Clone Your Forked Repository:** Open your terminal or command prompt and clone the repository to your local folder.
2.  **Create a Virtual Environment and Install Required Libraries:**
    ```bash
    pip install -r requirements.txt
    ```
    Click on `ISDN3000C_Lab07.ipynb` to open and edit it.
3.  **Save Your Work to Github:** Make sure you have completed and run all the cells in your notebook. Then you please stage the changes, commit them and push to Github. Please remember to work on `.gitignore` to avoid submitting your virtual environment, `.DS_Store`, or `__pycache__` folder to Github.
