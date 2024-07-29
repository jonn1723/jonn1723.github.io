The diet problem is one of the earliest large-scale optimization problems studied in practice. During the 1930s and 1940s, the Army aimed to meet the nutritional needs of its soldiers while minimizing costs. In this project, I'll be using linear optimization with various constraints to meet the minimum and maximum nutritional requirements with the objective function to minimize operational costs.

![image](https://github.com/user-attachments/assets/6b724c2c-a6fe-49f3-b981-ecdcb1011bc9)
![image](https://github.com/user-attachments/assets/d3194985-a9db-42ba-8ba8-dd95bfdcd7c1)

After selecting the first 65 rows of the data frame, we convert it into a list for immediate use. Each food in the list is then mapped to its respective cost and nutrient dictionaries. “dmin” and “dmax” are then defined as stated in the Excel spreadsheet. Meanwhile, “food_vars” represents the quantity of each food item, but when initialized, only displays values of zero or higher. Lastly, the objective function is the total cost of selected foods which the model attempts to minimize when solving.

![image](https://github.com/user-attachments/assets/2da7797d-1495-4d03-8e49-1009150eaaa5)

