
# Install necessary libraries
!pip install pandas openpyxl matplotlib seaborn

# Import libraries
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns




# Load data from Excel file
df = pd.read_excel('/content/BI Interview Test Case Data.xlsx', sheet_name='Case Study Data')
print("Successfully loaded data.")



pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)


df.head()

# Checking for null values

df.isna().sum()

df.nunique()


# Remove duplicates
df.drop_duplicates(inplace=True)



# Count occurrences of each value in the 'HeartDisease' column
heart_disease_counts = df['HeartDisease'].value_counts()

print(heart_disease_counts)

# Create a bar plot of the counts
plt.figure(figsize=(8, 6))
sns.barplot(x=heart_disease_counts.index, y=heart_disease_counts.values)
plt.xlabel('HeartDisease')
plt.ylabel('Count')
plt.title('Count of Responses in HeartDisease Column')

# Add count labels on top of each bar
for i, v in enumerate(heart_disease_counts.values):
    plt.text(i, v + 10, str(v), ha='center', va='bottom') # Adjust 10 for better positioning

plt.show()



# List of categorical columns to plot
categorical_columns = ['HeartDisease', 'Smoking', 'AlcoholDrinking', 'Stroke', 'DiffWalking',
                       'Sex', 'AgeCategory', 'Race', 'Diabetic', 'PhysicalActivity',
                       'GenHealth', 'Asthma', 'KidneyDisease', 'SkinCancer']

# Loop through each categorical column
for column in categorical_columns:
    # Count occurrences of each value in the current column
    counts = df[column].value_counts()

    # Create a bar plot of the counts
    plt.figure(figsize=(10, 6))  # Adjust figure size if needed
    sns.barplot(x=counts.index, y=counts.values)
    plt.xlabel(column)
    plt.ylabel('Count')
    plt.title(f'Count of Responses in {column} Column')
    plt.xticks(rotation=45, ha='right') # Rotate x-axis labels for better readability

    # Add count labels on top of each bar
    for i, v in enumerate(counts.values):
        plt.text(i, v + 10, str(v), ha='center', va='bottom') # Adjust 10 for better positioning

    plt.tight_layout() # Adjust layout to prevent labels from overlapping
    plt.show()


#plot for numerical columns

# List of numerical columns to plot
numerical_columns = ['BMI', 'PhysicalHealth', 'MentalHealth', 'SleepTime']

# Loop through each numerical column
for column in numerical_columns:
    # Create a histogram
    plt.figure(figsize=(8, 6))
    sns.histplot(df[column], kde=True)  # kde=True adds a kernel density estimate
    plt.xlabel(column)
    plt.ylabel('Frequency')
    plt.title(f'Distribution of {column}')
    plt.show()

    # Create a box plot
    plt.figure(figsize=(8, 6))
    sns.boxplot(y=df[column])  # Use 'y=' for vertical boxplot
    plt.ylabel(column)
    plt.title(f'Box Plot of {column}')
    plt.show()


 #bivariate analsysis plot for all variables vs target variable (Heartdisease)


# List of columns to analyze against 'HeartDisease'
columns_to_analyze = ['BMI', 'PhysicalHealth', 'MentalHealth', 'SleepTime', 'Smoking', 'AlcoholDrinking', 'Stroke', 'DiffWalking',
                       'Sex', 'AgeCategory', 'Race', 'Diabetic', 'PhysicalActivity',
                       'GenHealth', 'Asthma', 'KidneyDisease', 'SkinCancer']

# Loop through each column and create a bivariate plot
for column in columns_to_analyze:
    plt.figure(figsize=(8, 6))

    if pd.api.types.is_numeric_dtype(df[column]):  # Check if the column is numerical
        sns.boxplot(x='HeartDisease', y=column, data=df)
        plt.title(f'Box Plot of {column} vs. HeartDisease')
        plt.xlabel("Heart disease")
    else:  # Otherwise, assume it's categorical
        sns.countplot(x=column, hue='HeartDisease', data=df)
        plt.title(f'Count Plot of {column} vs. HeartDisease')
        plt.xticks(rotation=45, ha='right')

        plt.xlabel(column)
    plt.ylabel(column if pd.api.types.is_numeric_dtype(df[column]) else 'Count') #Adjust y label
    plt.show()


 #print the correlation coeffient for numerical

# Calculate the correlation matrix for numerical columns
numerical_columns = ['BMI', 'PhysicalHealth', 'MentalHealth', 'SleepTime']
correlation_matrix = df[numerical_columns].corr()

print(correlation_matrix)

# Create a heatmap of the correlation matrix
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt=".2f")
plt.title('Correlation Matrix of Numerical Features')
plt.show()


# encode the categorical Variables

# Assuming df is already loaded and preprocessed as in the previous code

from sklearn.preprocessing import LabelEncoder

# Create a LabelEncoder object
le = LabelEncoder()


# List of categorical columns to encode
categorical_cols = ['HeartDisease', 'Smoking', 'AlcoholDrinking', 'Stroke', 'DiffWalking',
                       'Sex', 'AgeCategory', 'Race', 'Diabetic', 'PhysicalActivity',
                       'GenHealth', 'Asthma', 'KidneyDisease', 'SkinCancer']

# Encode each categorical column
for col in categorical_cols:
    df[col] = le.fit_transform(df[col])

# Display the first few rows of the encoded DataFrame
df.head()



from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, classification_report
from sklearn.preprocessing import LabelEncoder


# Define features (X) and target (y)
X = df.drop(columns=['PatientID', 'HeartDisease'], axis=1)
y = df['HeartDisease']

# Split data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)




# Initialize and train a RandomForestClassifier
model = RandomForestClassifier(random_state=42)  # You can adjust hyperparameters here
model.fit(X_train, y_train)

# Make predictions on the test set
y_pred = model.predict(X_test)

# Evaluate the model
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy}")
print(classification_report(y_test, y_pred))

    model = RandomForestClassifier()
    model.fit(X_train, y_train)
    feature_importance = model.feature_importances_
    feature_names = X_train.columns

    # Create a DataFrame for easier viewing
    import pandas as pd
    importance_df = pd.DataFrame({'Feature': feature_names, 'Importance': feature_importance})
    importance_df = importance_df.sort_values(by='Importance', ascending=False)
    print(importance_df)

df['HeartDisease'].value_counts(normalize=True)


from imblearn.over_sampling import SMOTE
from sklearn.model_selection import train_test_split

# Split the data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)

# Apply SMOTE
smote = SMOTE(random_state=42)
X_train_sm, y_train_sm = smote.fit_resample(X_train, y_train)

print("After SMOTE:", y_train_sm.value_counts(normalize=True))


# Initialize and train a RandomForestClassifier
model = RandomForestClassifier(random_state=42)  # You can adjust hyperparameters here
model.fit(X_train_sm, y_train_sm)

# Make predictions on the test set
y_pred = model.predict(X_test)

# Evaluate the model
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy}")
print(classification_report(y_test, y_pred))

# Create a DataFrame from the test set and add predictions
test_df = X_test.copy()
test_df['HeartDisease'] = y_test  # Add the actual values from y_test
test_df['Predicted'] = y_pred  # Add the predicted values

# Write the DataFrame to an Excel file
with pd.ExcelWriter('Test Case Data Predicted.xlsx') as writer:
    test_df.to_excel(writer, index=False)




# Create a DataFrame to store actual vs predicted values
results_df = pd.DataFrame({'Actual': y_test, 'Predicted': y_pred})

# Create the jitter plot
plt.figure(figsize=(8, 6))
sns.stripplot(x='Actual', y='Predicted', data=results_df, jitter=True, marker='o', alpha=0.5)
plt.title('Actual vs. Predicted Heart Disease (Jitter Plot)')
plt.xlabel('Actual Heart Disease (0: No, 1: Yes)')
plt.ylabel('Predicted Heart Disease (0: No, 1: Yes)')
plt.show()


# --- Preferred Alternatives ---

# 1. Confusion Matrix (Much better for evaluation)
from sklearn.metrics import confusion_matrix
import seaborn as sns

cm = confusion_matrix(y_test, y_pred)
plt.figure(figsize=(6, 5))
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues',
            xticklabels=['No', 'Yes'], yticklabels=['No', 'Yes'])
plt.xlabel('Predicted')
plt.ylabel('Actual')
plt.title('Confusion Matrix')
plt.show()


# 2. Grouped Bar Chart (Good for comparison)
results_df['Count'] = 1  # Add a count column
grouped_df = results_df.groupby(['Actual', 'Predicted']).count().reset_index()

plt.figure(figsize=(8, 6))
sns.barplot(x='Actual', y='Count', hue='Predicted', data=grouped_df)
plt.title('Actual vs. Predicted Heart Disease')
plt.xlabel('Actual Heart Disease (0: No, 1: Yes)')
plt.ylabel('Count')
plt.show()
