# Overview

About the Air Quality Index
The EPA developed the AQI, which reports levels of ozone, particle pollution, and other common air pollutants on the same scale. An AQI reading of 101 corresponds to a level that is above the national air quality standard—the higher the AQI rating, the greater the health impact. The AQI is divided into color-coded categories, and each category is identified by a simple informative descriptor. The descriptors are intended to convey information to the public about how air quality within each category relates to public health. The table below defines the AQI categories.

![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/overall.PNG)

# Data Visualization

Firstly, lets sea how much time we measured AQI quality


 ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/AQI_USA.png)
 
Most clean and the most unclean state of usa

 ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/min_max_AQI.png)
 
 For the next step i find mean value for each month and made sisualizations
 
 
  ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/1980_2000.png)
  
  ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/2000_2010.png)
   
 ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/2000_2022.png)
    
  ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/2010_2022.png)
  
  # Forecasting with SARIMAX
  
  Before building model I devided dataset 3 sections 
  1. old data - I will not gonna use for my model 
  2. Train data - I will use for training use for my model 
  3. Train data - I will use for training use for my model 
  
  ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/train_test_split.png)
    
   Next lets visualise data and test by using adfuller_test for finding seasonal or not 
   
  ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/seasonal_decompose.png)
  
  and our P value not less than 0.05 so our dataset is not non-stationary
  
  For finind my SARIMA parametrs I did hyperparameter tuning so:
  
  ```python
  p = range(0,4)
q = range(0,4)
d = range(0,2)
P = range(0,4)
Q = range(0,4)
D = range(0,2)
pdqPQD_list  = list(itertools.product(p,q,d,P,Q,D))

warnings.filterwarnings('ignore')

rsme = []
order = []

for pqdPQD in tqdm.tqdm(pdqPQD_list):
    try:
        a = pqdPQD[:3]
        b = list(pqdPQD[3:])
        b.append(12)
        b = tuple(b)
        model = sm.tsa.statespace.SARIMAX(train_data['my_val'],order=a,seasonal_order=b).fit()
        pred = model.predict(start=90,end=110,dynamic=True)
        y_true = train_data['my_val'][90:111]
        eror = np.sqrt(mean_squared_error(y_true,pred))
        order.append(pqdPQD)
        rsme.append(eror)
    except:
        print("something wrong with parametr of {}".format(pqdPQD))
 ```

So as result I got : <br>
rsme	            order <br>
3.181601	(1, 0, 0, 1, 0, 1) <br>
3.189619	(3, 0, 0, 1, 0, 1) <br>
3.208493	(1, 0, 1, 1, 0, 1) <br>

so order of (1,0,0,1,0,1) I got minimum mean sqr error so in my model I will use that params and test it 
```python
model=sm.tsa.statespace.SARIMAX(train_data['my_val'],order=(1, 0, 0),seasonal_order=(1,0,1,12))
results=model.fit()
```

![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/forecast_data.png)

 It was good result!
 
 # At the end i did forecasting mean value of AQI all over USA 
 
 ![](https://github.com/tural327/US_Air_Quality_Analysis/blob/main/images/forecast_upcoming.png)
