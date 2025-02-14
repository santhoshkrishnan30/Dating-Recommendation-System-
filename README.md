# Dating-Recommendation-System 💞

Dating Recommendation refers to using data-driven techniques to provide personalized matchmaking suggestions to individuals seeking partners through online dating platforms. These recommendations are based on various factors and preferences, aiming to increase the likelihood of successful and compatible matches. If you want to learn how to recommend profiles for matchmaking, this project is for you. In this article, I’ll take you through the task of Dating Recommendations using Python.

## Dating Recommendations: Process We Can Follow

Dating recommendations involve a combination of data collection, preprocessing, and user profiling techniques to provide tailored matchmaking suggestions to users of dating platforms. The goal is to enhance user experiences by increasing the chances of meaningful and compatible connections.

Here are the steps we can follow for the task of dating recommendations:

1) Gather data from the dating platform, including user profiles, preferences, past interactions, and feedback.
2) Clean and preprocess the collected data.
3) Create meaningful features from the raw data. For example, you might extract features like age, location, interests, and compatibility scores from user profiles.
4) Develop and recommend user profiles by analyzing user behaviour, such as swipes, likes, and messaging patterns.

So, the process of creating a dating recommendation system starts with data collection. I found an ideal dataset for this task.

## Dating Recommendations using Python

Let’s get started with the task of dating recommendations by importing the necessary Python libraries and the dataset:

```python
import pandas as pd
import plotly.express as px

data = pd.read_csv("dating_app_dataset.csv")
print(data.head())

```

![image](https://github.com/user-attachments/assets/744b5de7-4619-4957-83b8-cfc9f3134d06)

Now, let’s explore this data before creating a dating recommendation system. I’ll start with exploring the age distribution in the data by gender:
```python
# age distribution by gender
fig = px.histogram(data, x="Age", color="Gender", nbins=20, 
                   title="Age Distribution by Gender")
fig.update_layout(xaxis_title="Age", yaxis_title="Count")
fig.show()
```
![image](https://github.com/user-attachments/assets/62da3397-caeb-4938-abc8-c46133daf43f)

Now, let’s have a look at the education level distribution by gender:

```python
# education level distribution by gender
education_order = ["High School", "Bachelor's Degree", "Master's Degree", "Ph.D."]
fig = px.bar(data, x="Education Level", color="Gender", 
             category_orders={"Education Level": education_order},
             title="Education Level Distribution by Gender")

fig.update_layout(xaxis_title="Education Level", yaxis_title="Count")
fig.show()
```
![image](https://github.com/user-attachments/assets/e6eb6bb5-c141-414b-b5ef-5fa97e1ef1df)

Now, let’s have a look at the frequency of app usage by gender:

```python
# frequency of app usage distribution
fig = px.bar(data, x="Frequency of Usage", 
             title="Frequency of App Usage Distribution")
fig.update_layout(xaxis_title="Frequency of Usage", 
                  yaxis_title="Count")
fig.show()
```
![image](https://github.com/user-attachments/assets/3e637ab8-e8d7-41c8-8e49-c163c6bb0b9a)

Now, I’ll separate data into male and female profiles:

```python
# Separate data into male and female
male_profiles = data[data['Gender'] == 'Male']
female_profiles = data[data['Gender'] == 'Female']
```
Now, let’s calculate the match score for dating recommendations:

```python

def calculate_match_score(profile1, profile2):
    # Shared interests score (1 point per shared interest)
    interests1 = set(eval(profile1['Interests']))
    interests2 = set(eval(profile2['Interests']))
    shared_interests_score = len(interests1.intersection(interests2))

    # Age difference score (higher age difference, lower score)
    age_difference_score = max(0, 10 - abs(profile1['Age'] - profile2['Age']))

    # Swiping history score (higher swiping history, higher score)
    swiping_history_score = min(profile1['Swiping History'], profile2['Swiping History']) / 100

    # Relationship type score (1 point for matching types)
    relationship_type_score = 0
    if profile1['Looking For'] == profile2['Looking For']:
        relationship_type_score = 1

    # Total match score
    total_score = (
        shared_interests_score + age_difference_score + swiping_history_score + relationship_type_score
    )

    return total_score

# Example: Calculate match score between two profiles
profile1 = male_profiles.iloc[0]
profile2 = female_profiles.iloc[0]
match_score = calculate_match_score(profile1, profile2)
print(f"Match score between User {profile1['User ID']} and User {profile2['User ID']} : {match_score}")
```
![image](https://github.com/user-attachments/assets/e8eace7b-fd5c-4ed6-bcc2-9e818e6fd8eb)

In the above code, we defined a function called calculate_match_score that calculates a compatibility score between two user profiles on a dating app. The goal is to quantify how well two users might match based on various factors. Let’s go through the steps we covered in the above code to calculate the match score:

1) We started by considering the interests or hobbies of both users listed in their profiles. Each shared interest contributes 1 point to the compatibility score. The more shared interests they have, the higher their compatibility score will be. For example, if both users enjoy travelling, that’s a shared interest and adds a point to their score. 
2) Age can play a significant role in compatibility. We calculated a score based on the age difference between the two users. If the age difference is small (within ten years), they receive a higher score. However, if the age difference is large, the score starts decreasing. It ensures that users with similar ages are more likely to have a higher match score.
3) User engagement with the app, as measured by their swiping history (e.g., the number of likes or dislikes they’ve given), is considered. A higher swiping history indicates more active use of the app to find a partner and contributes to a higher score. It encourages matchmaking with users who are actively engaged in the dating process.
4) The type of relationship each user is looking for (e.g., long-term relationship, marriage) is also used to calculate the match score. If both users are seeking the same type of relationship, they receive a point. This score reflects the compatibility of their relationship goals.
5) The final compatibility score is calculated by summing up the individual scores from the previous steps. A higher total score suggests a better potential match, while a lower score indicates a less compatible match.

Now, here’s how to generate dating recommendations by utilizing the match score we calculated above:

```python
def recommend_profiles(male_profiles, female_profiles):
    recommendations = []

    for _, male_profile in male_profiles.iterrows():
        best_match = None
        best_score = -1

        for _, female_profile in female_profiles.iterrows():
            score = calculate_match_score(male_profile, female_profile)

            if score > best_score:
                best_match = female_profile
                best_score = score

        recommendations.append((male_profile, best_match, best_score))

    return recommendations

# Generate recommendations
recommendations = recommend_profiles(male_profiles, female_profiles)

# Sort recommendations by match score in descending order
recommendations.sort(key=lambda x: x[2], reverse=True)

# Display the top recommendations
for idx, (male_profile, female_profile, match_score) in enumerate(recommendations[:10]):
    print(f"Recommendation {idx + 1}:")
    print(f"Male Profile (User {male_profile['User ID']}): Age {male_profile['Age']}, Interests {male_profile['Interests']}")
    print(f"Female Profile (User {female_profile['User ID']}): Age {female_profile['Age']}, Interests {female_profile['Interests']}")
    print(f"Match Score: {match_score}")
    print()
```
![image](https://github.com/user-attachments/assets/04f32416-d361-4567-91fa-fbac1af822c3)

In the above code, we are creating a recommendation system that suggests potential matches between male and female user profiles. The recommendation system evaluates the compatibility of these profiles based on various factors, including shared interests, age, and other attributes we used to calculate the match score. Let’s go through the steps covered in this code section one by one:

1) The function defined above is responsible for generating recommendations. It takes two sets of user profiles as input, one for males and one for females, and returns a list of recommended matches.
2) It iterates through each male user profile one by one. For each male profile, it searches through the female profiles to find the best potential match. It initially sets best_match to None and best_score to a negative value to ensure that the first female profile encountered becomes the initial best match.
3) As the code iterates through female profiles, it calculates the match score for each male-female profile pair and compares it to the previous best score. If the new score is higher, it updates the best_match and best_score with the current female profile and score.
4) Once the best match is found for a male profile, it is stored as a recommendation in the recommendations list, along with the male and female profiles involved in the match and the match score.
5) After generating recommendations for all male profiles, the code sorts the recommendations based on the match score in descending order. It ensures that the highest-scoring recommendations appear at the top.
6)Finally, it prints out the top recommendations, including information about the male and female profiles and their match scores.

So, this is how we can generate dating recommendations for matchmaking of profiles on a dating app using Python.






