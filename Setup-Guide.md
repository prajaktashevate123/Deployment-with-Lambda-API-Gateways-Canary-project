Step1. Create a Lambda Function
=

**1.** Go to AWS Console → **Lambda** → **Create function**.

**2.** Choose:

   **Author from scratch**

   Function name: Lambda-canary-Project

   Runtime: Python 3.x or Node.js 18


**3.** Add sample code (example in Python):

import json

        def lambda_handler(event, context):
           # TODO implement
                return {
                   'statusCode': 200,
                   'body': json.dumps('This msessage come from Development server')
                      }

**4.** Click **Deploy**.
<img width="940" height="431" alt="image" src="https://github.com/user-attachments/assets/43ef2d2b-c1af-4ab4-8652-0b651392045d" />



**Step2. Create an API Gateway**
=

**1.** Go to **Amazon API Gateway** → **Create API** →**REST API**.

**2.** Configure:

      API Name: API-Canary-Project

    Integration: Select Lambda Function → Lambda-canary-Project.

**3.** Click Deploy → Choose Stage: prod.

You now have an endpoint like:

       https://<api-id>.execute-api.<region>.amazonaws.com/prod/

<img width="940" height="426" alt="image" src="https://github.com/user-attachments/assets/fc1fa275-b724-4679-9154-beb14aaddc23" />
<img width="940" height="324" alt="image" src="https://github.com/user-attachments/assets/e61aede9-1514-49e9-b3da-1183d5fbfaed" />

Step3. Enable Lambda Versions & Aliases
=

**1.** Go to Lambda → Lambda-canary-Project.

**2.** Click Publish new version → Name it v1.

**3.** Create an Alias (e.g., prod) pointing to version v1.


Step 4. Canary Deployment Setup
=

A Canary deployment allows you to gradually shift traffic from one Lambda version to another.

**1.** In Lambda → Aliases → prod.

**2.** Click Edit traffic shifting.

**3.** Choose:

     Additional version: v2 (publish new version after code update).

     Traffic shifting: e.g., 10% to v2, 90% stays on v1.

**4.** Save changes.

Now requests hitting API Gateway → Lambda prod alias will be split between v1 and v2.

<img width="940" height="411" alt="image" src="https://github.com/user-attachments/assets/fa0adfa4-e06d-4ce7-83aa-60887aab47b8" />


Step 5. Test the Setup
=

**1.** Update Lambda with new code, publish as v2.

   import json

       def lambda_handler(event, context):
          # TODO implement
             return {
                'statusCode': 200,
                    'body': json.dumps('This msessage come from Production server')
                    }


**2.** Invoke API Gateway endpoint:

           curl https://<api-id>.execute-api.<region>.amazonaws.com/prod/


Some responses will show v1.

Some will show v2 (based on canary %).

<img width="940" height="308" alt="image" src="https://github.com/user-attachments/assets/678396c1-4fca-4b73-a4dc-36a71c16daf6" />
