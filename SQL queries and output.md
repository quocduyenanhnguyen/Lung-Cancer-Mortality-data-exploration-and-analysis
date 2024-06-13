# 1. Lung cancer count and percentage by gender
```
SELECT 
    gender,
    COUNT(DISTINCT id) gender_count,
    COUNT(DISTINCT id) * 100 / (SELECT 
            COUNT(DISTINCT id)
        FROM
            lung_cancer_mortality_data_large) gender_percentage
FROM
    lung_cancer_mortality_data_large
GROUP BY gender;
```
#### Output
![Screen Shot 2024-06-13 at 11 17 54 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/31527fe3-6166-4b1b-9ed0-46fe90efb819)

-> Based on this dataset, it looks like there are slightly more women with cancer than men. 

# 2. Lung cancer count and percentage by country
```
SELECT 
    country,
    COUNT(DISTINCT id) country_count,
    COUNT(DISTINCT id) * 100 / (SELECT 
            COUNT(DISTINCT id)
        FROM
            lung_cancer_mortality_data_large) country_percentage
FROM
    lung_cancer_mortality_data_large
GROUP BY country
order by country_percentage desc;
```
#### Output
![Screen Shot 2024-06-13 at 11 21 40 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/baca4613-6d20-4df4-b5ae-62ef93fb4098)

-> Top 3 countries with high cancer percentage are Latvia, Cyprus, and Malta.

# 3. Lung cancer by age group
```
SELECT DISTINCT
    CASE
        WHEN age < 20 THEN 'Under 20'
        WHEN age BETWEEN 20 AND 29 THEN '20-29'
        WHEN age BETWEEN 30 AND 39 THEN '30-39'
        WHEN age BETWEEN 40 AND 49 THEN '40-49'
        WHEN age BETWEEN 50 AND 59 THEN '50-59'
        WHEN age >= 60 THEN '60 and above'
    END Age_Group,
    COUNT(DISTINCT id) AS age_group_count
FROM
    lung_cancer_mortality_data_large
GROUP BY Age_Group
ORDER BY age_group_count DESC
;
```
#### Output
![Screen Shot 2024-06-13 at 11 23 26 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/7ce83efd-1b37-44be-a383-7b43681abc5e)

-> People that aged 50 to 59 have more cancer than any other age groups.

# 4. Cancer stage by age group
```
SELECT DISTINCT
    CASE
        WHEN age < 20 THEN 'Under 20'
        WHEN age BETWEEN 20 AND 29 THEN '20-29'
        WHEN age BETWEEN 30 AND 39 THEN '30-39'
        WHEN age BETWEEN 40 AND 49 THEN '40-49'
        WHEN age BETWEEN 50 AND 59 THEN '50-59'
        WHEN age >= 60 THEN '60 and above'
    END Age_Group,
    lc.cancer_stage,
    ti.cancer_stage_interpretation,
    COUNT(DISTINCT id) AS cancer_stage_by_age_group_count
FROM
    lung_cancer_mortality_data_large lc
        JOIN
    terminology_intepretation ti ON lc.cancer_stage = ti.cancer_stage
GROUP BY Age_Group , lc.cancer_stage , ti.cancer_stage_interpretation
ORDER BY cancer_stage_by_age_group_count DESC
;
```
#### Output
![Screen Shot 2024-06-13 at 11 27 09 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/212fa391-6099-45c5-bea7-6c5f059c2b6c)

-> Age group 50-59 and 60 and above have the highest stage II cancer, while Age Group 40-49 and under 20 have the highest stage I cancer. Age group 30-39 have the highest stage IV cancer.

# 5. Lung cancer count by family_history
```
SELECT 
    family_history, COUNT(DISTINCT id) AS family_history_count
FROM
    lung_cancer_mortality_data_large
GROUP BY family_history
;
```
#### Output
![Screen Shot 2024-06-13 at 11 32 29 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/abc6b3cb-7b04-4d00-adf3-62271bcdfef8)

-> If a family member has cancer, the chance of getting cancer is higher.

# 6. Lung cancer count by smoking_status
```
SELECT 
    smoking_status, COUNT(DISTINCT id) AS smoking_status_count
FROM
    lung_cancer_mortality_data_large
GROUP BY smoking_status
;
```
#### Output
![Screen Shot 2024-06-13 at 11 35 52 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/da224e91-b173-4437-9190-4602393f9715)

-> Most cancer patients are passive smokers. It is interesting to see that even if they never smoked before, they could still get cancer.

# 7. Lung cancer count by gender, family_history, and smoking_status
```
SELECT 
    gender,
    family_history,
    smoking_status,
    COUNT(DISTINCT id) AS gender_count
FROM
    lung_cancer_mortality_data_large
GROUP BY gender , family_history , smoking_status
;
```
#### Output
![Screen Shot 2024-06-13 at 11 38 52 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/a3655048-53e4-4bc8-a4a2-ed646056a31b)

-> Look at the top 3, passive smokers have the most cancer, especially men with no family history of cancer.

### 7.1. total count
```
SELECT 
    gender, family_history, COUNT(DISTINCT id) AS gender_count
FROM
    lung_cancer_mortality_data_large
GROUP BY gender , family_history
;
```
#### Output
![Screen Shot 2024-06-13 at 11 45 56 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/8a6141df-8a54-417d-9c59-b7c132072c4c)

-> Interestingly, women have slightly more lung cancer than men, whether they have a family history or not.

# 8. average days waiting to get treatment 
```
SELECT 
    round(avg(DATEDIFF(beginning_of_treatment_date,
            diagnosis_date)),2) AS average_days_waiting
FROM
    lung_cancer_mortality_data_large
;
```
#### Output
![Screen Shot 2024-06-13 at 11 50 52 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/14d104a2-ccd8-478a-a9e2-0a9ee9dd6c70)

-> On average, people wait 9.5 days to get treatment.

### 8.1 by age group
```
SELECT DISTINCT
    CASE
        WHEN age < 20 THEN 'Under 20'
        WHEN age BETWEEN 20 AND 29 THEN '20-29'
        WHEN age BETWEEN 30 AND 39 THEN '30-39'
        WHEN age BETWEEN 40 AND 49 THEN '40-49'
        WHEN age BETWEEN 50 AND 59 THEN '50-59'
        WHEN age >= 60 THEN '60 and above'
    END Age_Group,
    ROUND(AVG(DATEDIFF(beginning_of_treatment_date,
                    diagnosis_date)),
            2) AS days_waiting_on_average
FROM
    lung_cancer_mortality_data_large
GROUP BY Age_Group
;
```
#### Output
![Screen Shot 2024-06-13 at 11 52 52 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/5f93886f-9c33-4019-a911-dfedb9969a1a)

-> On average, Under 20 Age group wait the longest compared to other age groups, while 30-39 age group wait the shortest (since they have more stage IV cancer than any other age groups).

# 9. Lung Cancer count by BMI
```
SELECT DISTINCT
    CASE
        WHEN age > 20 AND bmi < 18.5 THEN 'Underweight'
        WHEN age > 20 AND bmi BETWEEN 18.5 AND 24.9 THEN 'Healthy Weight'
        WHEN age > 20 AND bmi BETWEEN 25.0 AND 29.9 THEN 'Overweight'
        WHEN age > 20 AND bmi >= 30.0 THEN 'Obesity'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi < 19.2
        THEN
            'Underweight'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi < 27.1
        THEN
            'Healthy Weight'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi < 30.8
        THEN
            'Overweight'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi >= 30.8
        THEN
            'Obesity'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi < 17.9
        THEN
            'Underweight'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi < 26.5
        THEN
            'Healthy Weight'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi < 31.9
        THEN
            'Overweight'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi >= 31.9
        THEN
            'Obesity'
    END BMI_interpretation,
    COUNT(DISTINCT id) AS lung_cancer_count_by_bmi
FROM
    lung_cancer_mortality_data_large
GROUP BY BMI_interpretation
;
```
#### Output
![Screen Shot 2024-06-13 at 11 55 38 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/7fce4363-639d-4857-a066-ca3923d81d6c)

-> Obese people are more likely to get lung cancer than any other weights, but even people with healthy weight can get cancer as well, if not more so than overweight and underweight people.

### 9.1. Lung Cancer count by BMI and age group
```
SELECT DISTINCT
    CASE
        WHEN age < 20 THEN 'Under 20'
        WHEN age BETWEEN 20 AND 29 THEN '20-29'
        WHEN age BETWEEN 30 AND 39 THEN '30-39'
        WHEN age BETWEEN 40 AND 49 THEN '40-49'
        WHEN age BETWEEN 50 AND 59 THEN '50-59'
        WHEN age >= 60 THEN '60 and above'
    END Age_Group,
    CASE
        WHEN age > 20 AND bmi < 18.5 THEN 'Underweight'
        WHEN age > 20 AND bmi BETWEEN 18.5 AND 24.9 THEN 'Healthy Weight'
        WHEN age > 20 AND bmi BETWEEN 25.0 AND 29.9 THEN 'Overweight'
        WHEN age > 20 AND bmi >= 30.0 THEN 'Obesity'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi < 19.2
        THEN
            'Underweight'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi < 27.1
        THEN
            'Healthy Weight'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi < 30.8
        THEN
            'Overweight'
        WHEN
            age <= 20 AND gender = 'Male'
                AND bmi >= 30.8
        THEN
            'Obesity'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi < 17.9
        THEN
            'Underweight'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi < 26.5
        THEN
            'Healthy Weight'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi < 31.9
        THEN
            'Overweight'
        WHEN
            age <= 20 AND gender = 'Female'
                AND bmi >= 31.9
        THEN
            'Obesity'
    END BMI_interpretation,
    COUNT(DISTINCT id) AS lung_cancer_count
FROM
    lung_cancer_mortality_data_large
GROUP BY Age_Group , BMI_interpretation
;
```
#### Output
![Screen Shot 2024-06-13 at 11 59 01 AM](https://github.com/quocduyenanhnguyen/Lung-Cancer-Mortality-data-exploration-and-analysis/assets/92205707/8c982d55-3811-4af8-9f7e-0c5a4d3ba093)

-> Obese people aged 40 and above tend to get more lung cancer than other age groups. Hence, old age and obesity increase the likelihood of getting lung cancer.
