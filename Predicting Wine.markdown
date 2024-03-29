# Predicting Wine: Artificial Neural Networks with Keras

**Using Artificial Neural Networks to predict wine type and quality based on its chemical properties.**

# 1. About Deep Learning
**Deep Learning**  

Deep Learning, a.k.a. Artificial Neural Networks (ANN), is a subfield of machine learning that is a set of algorithms that are inspired by the structure and function of the brain and is a popular fields in data science with many case studies involving robotics, image recognition and Artificial Intelligence (AI).  

One of the most powerful Python libraries for developing and evaluating deep learning models is Keras; It wraps the efficient numerical computation libraries Theano and TensorFlow.  

**Perceptrons**  

The “perceptron” is a neural network, which, in its simplest form, consists of a single neuron. Much like biological neurons, that have dendrites and axons, the single artificial neuron is a simple tree structure that has input nodes and a single output node, and is connected to each input node.  

**Here’s a visual comparison of the two:**  
![NUERON-VS-ANN](/assets/media/wine/nueron-vs-ann.png)

There are six components to artificial neurons. From left to right, they are:

1. `Input nodes`. Each input node is associated with a numerical value, which can be any real number.

2. `Connections`. Each connection that departs from the input node has a weight associated with it and this can also be any real number.

3. All the values of the input nodes and weights of the connections are brought together: they are used as inputs for a `weighted sum`.

4. The result will be the input for a `transfer` or `activation function`.

5. As a result, we have the `output node`, which is associated with the function (such as the sigmoid function) of the weighted sum of the input nodes.

6. Finally, the `bias`, which we can actually consider as the weight associated with an additional input node that is permanently set to 1. The bias value is important because it allows us to shift the activation function to the left or right, which can determine the success of our learning.

The logical consequence of this model is that perceptrons only work with numerical data. This means that we need to convert any nominal data into a numerical format.  

The perceptron can be used for classification purposes to determine that any output above a certain treshold indicates that an instance belongs to one class, while an output below the treshold might result in the input being a member of the other class. The straight line where the output equals the treshold is then the boundary between the two classes.  

**Multi-Layer Perceptrons**  

Networks of perceptrons are multi-layer perceptrons, and this is what we will implement in Python with the help of Keras. Multi-layer perceptrons are also known as “feed-forward neural networks”. These are more complex networks than the perceptron, as they consist of multiple neurons that are organized in layers. The number of layers is usually limited to two or three, but theoretically, there is no limit.  

The layers act very much like the biological neurons, the outputs of one layer serve as the inputs for the next layer.  

Among the layers, we can distinguish an input layer, hidden layers and an output layer. Multi-layer perceptrons are often fully connected. This means that there’s a connection from each perceptron in a certain layer to each perceptron in the next layer.  

While the perceptron could only represent linear separations between classes, the multi-layer perceptron overcomes that limitation and can also represent more complex decision boundaries.  

# 2. The Data

**Can we predict whether a wine is red or white by looking at its chemical properties?**  

The data consists of two datasets that are related to red and white variants of the Portuguese “Vinho Verde” wine. Below is the Data Dictionary for the 12 variables that are included in the data:  

1. `Fixed acidity`: acids are major wine properties and contribute greatly to the wine’s taste. Usually, the total acidity is divided into two groups: the volatile acids and the nonvolatile or fixed acids. Among the fixed acids that we can find in wines are the following: tartaric, malic, citric, and succinic. This variable is expressed in g(tartaricacidtartaricacid)/dm3dm3 in the data sets.
2. `Volatile acidity`: the volatile acidity is basically the process of wine turning into vinegar. In the U.S, the legal limits of Volatile Acidity are 1.2 g/L for red table wine and 1.1 g/L for white table wine. In these data sets, the volatile acidity is expressed in g(aceticacidaceticacid)/dm3dm3.
3. `Citric acid` is one of the fixed acids that we will find in wines. It’s expressed in g/dm3dm3 in the two data sets.
4. `Residual sugar` typically refers to the sugar remaining after fermentation stops, or is stopped. It’s expressed in g/dm3dm3 in the red and white data.
5. `Chlorides` can be a major contributor to saltiness in wine. Here, we will see that it’s expressed in g(sodiumchloridesodiumchloride)/dm3dm3.
6. `Free sulfur dioxide`: the part of the sulphur dioxide that is added to a wine and that is lost into it is said to be bound, while the active part is said to be free. Winemaker will always try to get the highest proportion of free sulphur to bind. This variables is expressed in mg/dm3dm3 in the data.
7. `Total sulfur dioxide` is the sum of the bound and the free sulfur dioxide (SO2). Here, it’s expressed in mg/dm3dm3. There are legal limits for sulfur levels in wines: in the EU, red wines can only have 160mg/L, while white and rose wines can have about 210mg/L. Sweet wines are allowed to have 400mg/L. For the US, the legal limits are set at 350mg/L and for Australia, this is 250mg/L.
8. `Density` is generally used as a measure of the conversion of sugar to alcohol. Here, it’s expressed in g/cm3cm3.
9. `pH` or the potential of hydrogen is a numeric scale to specify the acidity or basicity the wine. As we might know, solutions with a pH less than 7 are acidic, while solutions with a pH greater than 7 are basic. With a pH of 7, pure water is neutral. Most wines have a pH between 2.9 and 3.9 and are therefore acidic.
10. `Sulphates` are to wine as gluten is to food. We might already know sulphites from the wine have been known to cause headaches. They are a regular part of winemaking and are considered necessary to produce wine. In this case, they are expressed in g(potassiumsulphatepotassiumsulphate)/dm3dm3.
11. `Alcohol`: wine is an alcoholic beverage and as we know, the percentage of alcohol can vary from wine to wine. It's expressed as % vol in the red and white data.
12. `Quality`: wine experts graded the wine quality between 0 (very bad) and 10 (very excellent). The eventual number is the median of at least three evaluations made by those same wine experts.


```python
# Import numpy
import numpy as np
# Import pandas 
import pandas as pd

# Read in white wine data 
white = pd.read_csv("http://archive.ics.uci.edu/ml/machine-learning-databases/wine-quality/winequality-white.csv", sep=';')

# Read in red wine data 
red = pd.read_csv("http://archive.ics.uci.edu/ml/machine-learning-databases/wine-quality/winequality-red.csv", sep=';')
```

# 3. Exploratory Data Analysis


```python
# Print info on white wine
print("White Wine:")
print(white.info())

# Print info on red wine
print("\n""Red Wine:")
print(red.info())
```

    White Wine:
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4898 entries, 0 to 4897
    Data columns (total 12 columns):
    fixed acidity           4898 non-null float64
    volatile acidity        4898 non-null float64
    citric acid             4898 non-null float64
    residual sugar          4898 non-null float64
    chlorides               4898 non-null float64
    free sulfur dioxide     4898 non-null float64
    total sulfur dioxide    4898 non-null float64
    density                 4898 non-null float64
    pH                      4898 non-null float64
    sulphates               4898 non-null float64
    alcohol                 4898 non-null float64
    quality                 4898 non-null int64
    dtypes: float64(11), int64(1)
    memory usage: 459.3 KB
    None
    
    Red Wine:
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1599 entries, 0 to 1598
    Data columns (total 12 columns):
    fixed acidity           1599 non-null float64
    volatile acidity        1599 non-null float64
    citric acid             1599 non-null float64
    residual sugar          1599 non-null float64
    chlorides               1599 non-null float64
    free sulfur dioxide     1599 non-null float64
    total sulfur dioxide    1599 non-null float64
    density                 1599 non-null float64
    pH                      1599 non-null float64
    sulphates               1599 non-null float64
    alcohol                 1599 non-null float64
    quality                 1599 non-null int64
    dtypes: float64(11), int64(1)
    memory usage: 150.0 KB
    None


View the first 5 rows of `red`: 


```python
red.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fixed acidity</th>
      <th>volatile acidity</th>
      <th>citric acid</th>
      <th>residual sugar</th>
      <th>chlorides</th>
      <th>free sulfur dioxide</th>
      <th>total sulfur dioxide</th>
      <th>density</th>
      <th>pH</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7.4</td>
      <td>0.70</td>
      <td>0.00</td>
      <td>1.9</td>
      <td>0.076</td>
      <td>11.0</td>
      <td>34.0</td>
      <td>0.9978</td>
      <td>3.51</td>
      <td>0.56</td>
      <td>9.4</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7.8</td>
      <td>0.88</td>
      <td>0.00</td>
      <td>2.6</td>
      <td>0.098</td>
      <td>25.0</td>
      <td>67.0</td>
      <td>0.9968</td>
      <td>3.20</td>
      <td>0.68</td>
      <td>9.8</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7.8</td>
      <td>0.76</td>
      <td>0.04</td>
      <td>2.3</td>
      <td>0.092</td>
      <td>15.0</td>
      <td>54.0</td>
      <td>0.9970</td>
      <td>3.26</td>
      <td>0.65</td>
      <td>9.8</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11.2</td>
      <td>0.28</td>
      <td>0.56</td>
      <td>1.9</td>
      <td>0.075</td>
      <td>17.0</td>
      <td>60.0</td>
      <td>0.9980</td>
      <td>3.16</td>
      <td>0.58</td>
      <td>9.8</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7.4</td>
      <td>0.70</td>
      <td>0.00</td>
      <td>1.9</td>
      <td>0.076</td>
      <td>11.0</td>
      <td>34.0</td>
      <td>0.9978</td>
      <td>3.51</td>
      <td>0.56</td>
      <td>9.4</td>
      <td>5</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



View the last 5 rows of `white`:


```python
white.tail()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fixed acidity</th>
      <th>volatile acidity</th>
      <th>citric acid</th>
      <th>residual sugar</th>
      <th>chlorides</th>
      <th>free sulfur dioxide</th>
      <th>total sulfur dioxide</th>
      <th>density</th>
      <th>pH</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4893</th>
      <td>6.2</td>
      <td>0.21</td>
      <td>0.29</td>
      <td>1.6</td>
      <td>0.039</td>
      <td>24.0</td>
      <td>92.0</td>
      <td>0.99114</td>
      <td>3.27</td>
      <td>0.50</td>
      <td>11.2</td>
      <td>6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4894</th>
      <td>6.6</td>
      <td>0.32</td>
      <td>0.36</td>
      <td>8.0</td>
      <td>0.047</td>
      <td>57.0</td>
      <td>168.0</td>
      <td>0.99490</td>
      <td>3.15</td>
      <td>0.46</td>
      <td>9.6</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4895</th>
      <td>6.5</td>
      <td>0.24</td>
      <td>0.19</td>
      <td>1.2</td>
      <td>0.041</td>
      <td>30.0</td>
      <td>111.0</td>
      <td>0.99254</td>
      <td>2.99</td>
      <td>0.46</td>
      <td>9.4</td>
      <td>6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4896</th>
      <td>5.5</td>
      <td>0.29</td>
      <td>0.30</td>
      <td>1.1</td>
      <td>0.022</td>
      <td>20.0</td>
      <td>110.0</td>
      <td>0.98869</td>
      <td>3.34</td>
      <td>0.38</td>
      <td>12.8</td>
      <td>7</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4897</th>
      <td>6.0</td>
      <td>0.21</td>
      <td>0.38</td>
      <td>0.8</td>
      <td>0.020</td>
      <td>22.0</td>
      <td>98.0</td>
      <td>0.98941</td>
      <td>3.26</td>
      <td>0.32</td>
      <td>11.8</td>
      <td>6</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Sample 5 rows of `red`:


```python
red.sample(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fixed acidity</th>
      <th>volatile acidity</th>
      <th>citric acid</th>
      <th>residual sugar</th>
      <th>chlorides</th>
      <th>free sulfur dioxide</th>
      <th>total sulfur dioxide</th>
      <th>density</th>
      <th>pH</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1116</th>
      <td>7.0</td>
      <td>0.69</td>
      <td>0.07</td>
      <td>2.5</td>
      <td>0.091</td>
      <td>15.0</td>
      <td>21.0</td>
      <td>0.99572</td>
      <td>3.38</td>
      <td>0.60</td>
      <td>11.3</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1099</th>
      <td>8.6</td>
      <td>0.52</td>
      <td>0.38</td>
      <td>1.5</td>
      <td>0.096</td>
      <td>5.0</td>
      <td>18.0</td>
      <td>0.99666</td>
      <td>3.20</td>
      <td>0.52</td>
      <td>9.4</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>680</th>
      <td>13.3</td>
      <td>0.43</td>
      <td>0.58</td>
      <td>1.9</td>
      <td>0.070</td>
      <td>15.0</td>
      <td>40.0</td>
      <td>1.00040</td>
      <td>3.06</td>
      <td>0.49</td>
      <td>9.0</td>
      <td>5</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1123</th>
      <td>10.7</td>
      <td>0.40</td>
      <td>0.37</td>
      <td>1.9</td>
      <td>0.081</td>
      <td>17.0</td>
      <td>29.0</td>
      <td>0.99674</td>
      <td>3.12</td>
      <td>0.65</td>
      <td>11.2</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1509</th>
      <td>7.9</td>
      <td>0.18</td>
      <td>0.40</td>
      <td>1.8</td>
      <td>0.062</td>
      <td>7.0</td>
      <td>20.0</td>
      <td>0.99410</td>
      <td>3.28</td>
      <td>0.70</td>
      <td>11.1</td>
      <td>5</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



`Summary Statistics` about our data to assess its quality:


```python
# Describe `white`
white.describe()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fixed acidity</th>
      <th>volatile acidity</th>
      <th>citric acid</th>
      <th>residual sugar</th>
      <th>chlorides</th>
      <th>free sulfur dioxide</th>
      <th>total sulfur dioxide</th>
      <th>density</th>
      <th>pH</th>
      <th>sulphates</th>
      <th>alcohol</th>
      <th>quality</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.000000</td>
      <td>4898.0</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>6.854788</td>
      <td>0.278241</td>
      <td>0.334192</td>
      <td>6.391415</td>
      <td>0.045772</td>
      <td>35.308085</td>
      <td>138.360657</td>
      <td>0.994027</td>
      <td>3.188267</td>
      <td>0.489847</td>
      <td>10.514267</td>
      <td>5.877909</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.843868</td>
      <td>0.100795</td>
      <td>0.121020</td>
      <td>5.072058</td>
      <td>0.021848</td>
      <td>17.007137</td>
      <td>42.498065</td>
      <td>0.002991</td>
      <td>0.151001</td>
      <td>0.114126</td>
      <td>1.230621</td>
      <td>0.885639</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>min</th>
      <td>3.800000</td>
      <td>0.080000</td>
      <td>0.000000</td>
      <td>0.600000</td>
      <td>0.009000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>0.987110</td>
      <td>2.720000</td>
      <td>0.220000</td>
      <td>8.000000</td>
      <td>3.000000</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>6.300000</td>
      <td>0.210000</td>
      <td>0.270000</td>
      <td>1.700000</td>
      <td>0.036000</td>
      <td>23.000000</td>
      <td>108.000000</td>
      <td>0.991723</td>
      <td>3.090000</td>
      <td>0.410000</td>
      <td>9.500000</td>
      <td>5.000000</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>6.800000</td>
      <td>0.260000</td>
      <td>0.320000</td>
      <td>5.200000</td>
      <td>0.043000</td>
      <td>34.000000</td>
      <td>134.000000</td>
      <td>0.993740</td>
      <td>3.180000</td>
      <td>0.470000</td>
      <td>10.400000</td>
      <td>6.000000</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>7.300000</td>
      <td>0.320000</td>
      <td>0.390000</td>
      <td>9.900000</td>
      <td>0.050000</td>
      <td>46.000000</td>
      <td>167.000000</td>
      <td>0.996100</td>
      <td>3.280000</td>
      <td>0.550000</td>
      <td>11.400000</td>
      <td>6.000000</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>max</th>
      <td>14.200000</td>
      <td>1.100000</td>
      <td>1.660000</td>
      <td>65.800000</td>
      <td>0.346000</td>
      <td>289.000000</td>
      <td>440.000000</td>
      <td>1.038980</td>
      <td>3.820000</td>
      <td>1.080000</td>
      <td>14.200000</td>
      <td>9.000000</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



It is very important to be aware of differences amongst the variables. We can see that some of the variables have a lot of difference in their `min` and `max` values.

Double check for `null` values:


```python
print("White Wine:")
print(pd.isnull(white).count())
print("\n""Red Wine:")
print(pd.isnull(red).count())
```

    White Wine:
    fixed acidity           4898
    volatile acidity        4898
    citric acid             4898
    residual sugar          4898
    chlorides               4898
    free sulfur dioxide     4898
    total sulfur dioxide    4898
    density                 4898
    pH                      4898
    sulphates               4898
    alcohol                 4898
    quality                 4898
    dtype: int64
    
    Red Wine:
    fixed acidity           1599
    volatile acidity        1599
    citric acid             1599
    residual sugar          1599
    chlorides               1599
    free sulfur dioxide     1599
    total sulfur dioxide    1599
    density                 1599
    pH                      1599
    sulphates               1599
    alcohol                 1599
    quality                 1599
    dtype: int64


## 3.1 Visualizing The Data
Let's take a look at the distribution of some of the dataset’s variables and make scatter plots to see possible correlations.

**Alcohol**


```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots(1, 2)

ax[0].hist(red.alcohol, 10, facecolor='red', alpha=0.5, label="Red wine")
ax[1].hist(white.alcohol, 10, facecolor='white', ec="black", lw=0.5, alpha=0.5, label="White wine")

fig.subplots_adjust(left=0, right=1, bottom=0, top=0.5, hspace=0.05, wspace=1)
ax[0].set_ylim([0, 1000])
ax[0].set_xlabel("Alcohol in % Vol")
ax[0].set_ylabel("Frequency")
ax[1].set_xlabel("Alcohol in % Vol")
ax[1].set_ylabel("Frequency")
#ax[0].legend(loc='best')
#ax[1].legend(loc='best')
fig.suptitle("Distribution of Alcohol in % Vol")

plt.show()
```


![png](/assets/media/wine/WineType_21_0.png)


We can see that the alcohol levels between the red and white wine are mostly the same: they have around 9% of alcohol. There are also a considerable amount of observations that have 10% or 11% of alcohol percentage.


```python
# print(np.histogram(red.alcohol, bins=[7,8,9,10,11,12,13,14,15]))
# print(np.histogram(white.alcohol, bins=[7,8,9,10,11,12,13,14,15]))
```

**Sulphates**  
One thing that we should look at is the relationship between the `sulphates` and the `quality` of the wine. High levels of sulphate are known to cause headaches and may influence the quality of the wine as well as its rating.  

Let's take a look:


```python
fig, ax = plt.subplots(1, 2, figsize=(8, 4))

ax[0].scatter(red['quality'], red["sulphates"], color="red")
ax[1].scatter(white['quality'], white['sulphates'], color="white", edgecolors="black", lw=0.5)

ax[0].set_title("Red Wine")
ax[1].set_title("White Wine")
ax[0].set_xlabel("Quality")
ax[1].set_xlabel("Quality")
ax[0].set_ylabel("Sulphates")
ax[1].set_ylabel("Sulphates")
ax[0].set_xlim([0,10])
ax[1].set_xlim([0,10])
ax[0].set_ylim([0,2.5])
ax[1].set_ylim([0,2.5])
fig.subplots_adjust(wspace=0.5)
fig.suptitle("Wine Quality by Amount of Sulphates")

plt.show()
```


![png](/assets/media/wine/WineType_25_0.png)


Red wine seems to contain more sulphates than white wine, which only has a couple of exceptions that fall just above 1 g/dm3dm3. This could maybe explain why red wine causes headaches.  

In regards to the quality, we can clearly see that there is white wine with a relatively low amount of sulphates that gets a score of 9, but for the rest it’s difficult to interpret the data correctly at this point.  

We need to take into account that the difference in observations could also affect the graphs and how we might interpret them.

**Acidity**  
Apart from the sulphates, the acidity is one of the major and important wine characteristics that is necessary to achieve quality wines. Great wines often balance out acidity, tannin, alcohol and sweetness. In quantities of 0.2 to 0.4 g/L, volatile acidity doesn’t affect a wine’s quality. At higher levels, however, volatile acidity can give wine a sharp, vinegary tactile sensation. Extreme volatile acidity signifies a seriously flawed wine.  

Let’s put the data to the test and make a scatter plot that plots the alcohol versus the volatile acidity. The data points will be colored according to their rating or `quality` label:


```python
np.random.seed(570)

redlabels = np.unique(red['quality'])
whitelabels = np.unique(white['quality'])

fig, ax = plt.subplots(1, 2, figsize=(8, 4))
redcolors = np.random.rand(6,4)
whitecolors = np.append(redcolors, np.random.rand(1,4), axis=0)

for i in range(len(redcolors)):
    redy = red['alcohol'][red.quality == redlabels[i]]
    redx = red['volatile acidity'][red.quality == redlabels[i]]
    ax[0].scatter(redx, redy, c=redcolors[i])
for i in range(len(whitecolors)):
    whitey = white['alcohol'][white.quality == whitelabels[i]]
    whitex = white['volatile acidity'][white.quality == whitelabels[i]]
    ax[1].scatter(whitex, whitey, c=whitecolors[i])
    
ax[0].set_title("Red Wine")
ax[1].set_title("White Wine")
ax[0].set_xlim([0,1.7])
ax[1].set_xlim([0,1.7])
ax[0].set_ylim([5,15.5])
ax[1].set_ylim([5,15.5])
ax[0].set_xlabel("Volatile Acidity")
ax[0].set_ylabel("Alcohol")
ax[1].set_xlabel("Volatile Acidity")
ax[1].set_ylabel("Alcohol") 
#ax[0].legend(redlabels, loc='best', bbox_to_anchor=(1.3, 1))
ax[1].legend(whitelabels, loc='best', bbox_to_anchor=(1.3, 1))
#fig.suptitle("Alcohol - Volatile Acidity")
fig.subplots_adjust(top=0.85, wspace=0.7)

plt.show()
```


![png](/assets/media/wine/WineType_28_0.png)


The colors in this image are randomly chosen with the help of the numpy `random` module. We can always change this by passing a list to the `redcolors` or `whitecolors` variables.  

Keep in mind that the white wine data has one unique `quality` value more than the red wine data.

## 3.2 Data Observation

- Some of the variables of our data sets have values that are considerably far apart
- We have an ideal scenario: there are no null values in the data sets
- Most wines that were included in the data set have around 9% of alcohol
- Red wine seems to contain more sulphates than the white wine, which has less sulphates above 1 g/dm3dm3
- Most wines had a volatile acidity of 0.5 and below. At the moment, there is no direct relation to the quality of the wine

# 4. Preprocess Data
Let’s preprocess the data so we can start building our neural network.


```python
# Add `type` column to `red` with value 1
red['type'] = 1

# Add `type` column to `white` with value 0
white['type'] = 0

# Append `white` to `red`
wines = red.append(white, ignore_index=True)
```

We set `ignore_index` to `True` in this case because we don’t want to keep the index labels of `white` when we are appending the data to `red`: we want the labels to continue from where they left off in `red`, not duplicate index labels from joining both data sets together.

**Correlation Matrix**  
Since it can be somewhat difficult to interpret graphs, it’s also a good idea to plot a correlation matrix. This will give insights more quickly about which variables correlate:


```python
import seaborn as sns
corr = wines.corr()
sns.heatmap(corr, 
            xticklabels=corr.columns.values,
            yticklabels=corr.columns.values)
sns.plt.show()
```


![png](/assets/media/wine/WineType_35_0.png)


As we would expect, there are some variables that correlate, such as `density` and `residual sugar`. Also `volatile acidity` and `type` are more closely connected than we originally thought.

# 5. Train and Test Sets
Imbalanced data typically refers to a problem with classification problems where the classes are not represented equally. Most classification data sets do not have exactly equal number of instances in each class, but a small difference often does not matter. 

We need to make sure that all two classes of wine are present in the training model. The amount of instances of all two wine types needs to be more or less equal so that we do not favor one class over the other in our predictions.  

We can import the `train_test_split` from `sklearn.model_selection` and assign the data and the target labels to the variables `X` and `y`. We will see that we need to flatten the array of target labels in order to be completely ready to use the `X` and `y` variables as input for the `train_test_split()` function.


```python
# Import `train_test_split` from `sklearn.model_selection`
from sklearn.model_selection import train_test_split

# Specify the data 
X=wines.iloc[:,0:11]

# Specify the target labels and flatten the array
y= np.ravel(wines.type)

# Split the data up in train and test sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)
```

# 6. Standardize The Data
Some of the values in the `white` and `red` data sets are far apart and we need to do some standardization to work with these values.


```python
# Import `StandardScaler` from `sklearn.preprocessing`
from sklearn.preprocessing import StandardScaler

# Define the scaler 
scaler = StandardScaler().fit(X_train)

# Scale the train set
X_train = scaler.transform(X_train)

# Scale the test set
X_test = scaler.transform(X_test)
```

# 7. Model Data
Since we only have two classes, white and red, we are going to do a binary classification. We have already encoded red as 1 and white as 0.  

A type of network that performs well on such a problem is a multi-layer perceptron. We are looking to build a fairly simple stack of fully-connected layers to solve our problem and we will use the `relu` activation fuction.

A quick way to get started on building our multi-layer perceptron is to use the Keras Sequential model: it’s a linear stack of layers. We can easily create the model by passing a list of layer instances to the constructor, which is set up by running `model = Sequential()`.    

When making our model, it’s important to take into account that our first layer needs to make the input shape clear. The model needs to know what input shape to expect and that’s why we will always find the `input_shape`, `input_dim`, `input_length`, or `batch_size` arguments from the [documentation](https://keras.io/getting-started/sequential-model-guide/).  

In this case, we have to use a `Dense` layer, which is a fully connected layer. Dense layers implement the following operation: `output = activation(dot(input, kernel) + bias)`. Note that without the activation function, our Dense layer would only consist of two linear operations: a dot product and an addition.  

In the first layer, the `activation` argument takes the value `relu`. Next, we also see that the `input_shape` has been defined. This is the `input` of the operation that we have just seen: the model takes as input arrays of shape `(12,)`, or `(*, 12)`. Finally, we can see that the first layer has `12` as a first value for the `units` argument of `Dense()`, which is the dimensionality of the output space, which are actually 12 hidden units. This means that the model will output arrays of shape `(*, 12)`: this is is the dimensionality of the output space. 

The `units` actually represents the `kernel` of the above formula or the weights matrix, composed of all weights given to all input nodes, created by the layer. Note that we don’t include any bias because we haven’t included the `use_bias` argument and set it to `TRUE`, which is also a possibility.  

The intermediate layer also uses the `relu` activation function. The output of this layer will be arrays of shape `(*,8)`.  

We are ending the network with a `Dense` layer of size 1. The final layer will also use a `sigmoid` activation function so that our output is actually a probability; This means that this will result in a score between 0 and 1, indicating how likely the sample is to have the target “1”, or how likely the wine is to be red.


```python
# Import `Sequential` from `keras.models`
from keras.models import Sequential

# Import `Dense` from `keras.layers`
from keras.layers import Dense

# Initialize the constructor
model = Sequential()

# Add an input layer 
model.add(Dense(12, activation='relu', input_shape=(11,)))

# Add one hidden layer 
model.add(Dense(8, activation='relu'))

# Add an output layer 
model.add(Dense(1, activation='sigmoid'))
```

    Using TensorFlow backend.


We see that there are two key architecture decisions that we need to make to build our model:  
- How many layers we are going to use and 
- How many “hidden units” we will chose for each layer  

In this case, we picked `12` hidden units for the first layer of our model: this is the dimensionality of the output space. We are setting the amount of freedom that we are allowing the network to have when it’s learning representations. If we would allow more hidden units, our network will be able to learn more complex representations but it will also be a more expensive operations that can be prone to overfitting. Overfitting occurs when the model is too complex: it will describe random error or noise and not the underlying relationship that it needs to describe.


```python
# Model output shape
model.output_shape

# Model summary
model.summary()

# Model config
model.get_config()

# List all weight tensors 
model.get_weights()
```

    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    dense_1 (Dense)              (None, 12)                144       
    _________________________________________________________________
    dense_2 (Dense)              (None, 8)                 104       
    _________________________________________________________________
    dense_3 (Dense)              (None, 1)                 9         
    =================================================================
    Total params: 257
    Trainable params: 257
    Non-trainable params: 0
    _________________________________________________________________





    [array([[ 0.2896654 ,  0.01695609, -0.1637314 , -0.4055706 , -0.1897687 ,
              0.05104744, -0.47552979,  0.04375035,  0.14994401,  0.18585712,
              0.04605234,  0.4203161 ],
            [ 0.09246093, -0.00486219,  0.15394145, -0.3556453 ,  0.37075537,
             -0.09201503,  0.22433394,  0.49172276,  0.02939814, -0.00528288,
             -0.28554958, -0.29101855],
            [ 0.13772285,  0.10540932, -0.16185051, -0.13713309, -0.30931568,
              0.29137081,  0.22610158, -0.03789827,  0.03900152, -0.13670495,
              0.02206749,  0.07224816],
            [ 0.12972063, -0.46136355, -0.1358273 , -0.03706533,  0.21573442,
              0.21198452, -0.02762926, -0.19320443,  0.24703926, -0.42241281,
              0.29690397, -0.07473344],
            [-0.06937274,  0.13956004, -0.04767117,  0.03820831,  0.27192348,
             -0.47782913, -0.09866273, -0.42932004, -0.04532751, -0.17295876,
             -0.34326571,  0.48160571],
            [ 0.06270897,  0.10312521,  0.34606183, -0.12457022, -0.16116223,
              0.03542614,  0.15460205, -0.48483256, -0.21644399,  0.17074299,
             -0.31874129, -0.00338858],
            [-0.12783119, -0.04838416,  0.20839161,  0.33239806,  0.10142457,
             -0.30532336, -0.38364607,  0.38588023,  0.01854885,  0.05628592,
              0.19293177, -0.05149972],
            [-0.43119535, -0.27155614, -0.46491459, -0.25988257,  0.09503597,
             -0.5009988 , -0.15289614, -0.41986507,  0.49207467,  0.00157392,
             -0.16561732,  0.24532884],
            [-0.17463535,  0.08331698,  0.27836359, -0.14132953, -0.30007723,
             -0.32280877, -0.1596224 ,  0.32339281, -0.29511583,  0.41530758,
             -0.45230752, -0.4677496 ],
            [ 0.00880128,  0.33655512, -0.45286989,  0.43754292, -0.04207084,
             -0.3803001 ,  0.21270508,  0.43465519,  0.46255559, -0.18048412,
             -0.29980046,  0.45833129],
            [-0.31532359, -0.26553845, -0.21347395, -0.48159593,  0.50684947,
              0.29534268,  0.16204995,  0.30744392,  0.02605116, -0.42019787,
             -0.2871666 ,  0.01862645]], dtype=float32),
     array([ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.], dtype=float32),
     array([[ 0.13802433, -0.1397422 ,  0.37863284, -0.15413004,  0.52855718,
             -0.13628149,  0.03936303, -0.25822985],
            [-0.49231729, -0.38898361, -0.35550442, -0.08421832, -0.25332853,
             -0.01050067,  0.28014278,  0.45117211],
            [ 0.12644804, -0.07388979,  0.54314303,  0.32438159,  0.36960304,
              0.03181666, -0.4164722 , -0.5394448 ],
            [ 0.4500739 ,  0.19391423, -0.21727106, -0.30200374, -0.48891065,
              0.42746753, -0.34170333, -0.21399096],
            [-0.53348607, -0.34708104, -0.44702923,  0.31050652, -0.52960402,
              0.4368825 , -0.46614015,  0.21192652],
            [-0.50510532,  0.35415596, -0.37515181, -0.2976757 , -0.45574439,
             -0.53875005,  0.50481069,  0.12087846],
            [ 0.49851513, -0.30494523,  0.10521847, -0.46860957,  0.2679804 ,
             -0.04077768, -0.00542253,  0.31112713],
            [-0.34450638, -0.16991124, -0.38176683, -0.38524371, -0.49763155,
              0.37959671,  0.42052734,  0.38327682],
            [-0.36905417,  0.40031129, -0.1917389 ,  0.21109205,  0.33099413,
              0.1206221 , -0.02995032, -0.21631998],
            [ 0.0382123 ,  0.51903856,  0.31960887, -0.01158792, -0.05701348,
              0.49415326, -0.36317486, -0.18171829],
            [ 0.42716026,  0.4860462 ,  0.29231024, -0.49826044,  0.38350588,
              0.51668561, -0.35148156,  0.00427073],
            [ 0.14594537, -0.22531378,  0.36939985, -0.14032108, -0.48043332,
             -0.00821644,  0.54728377, -0.17121464]], dtype=float32),
     array([ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.], dtype=float32),
     array([[ 0.76886392],
            [ 0.16227716],
            [ 0.38850141],
            [-0.30456817],
            [ 0.39023936],
            [-0.56345725],
            [-0.12128478],
            [ 0.20257616]], dtype=float32),
     array([ 0.], dtype=float32)]



# 8. Compile and Fit
Now we need to compile our model and fit it to the data.


```python
model.compile(loss='binary_crossentropy',
              optimizer='adam',
              metrics=['accuracy'])
                   
model.fit(X_train, y_train,epochs=20, batch_size=1, verbose=1)
```

    Epoch 1/20
    4352/4352 [==============================] - 14s - loss: 0.0901 - acc: 0.9692    
    Epoch 2/20
    4352/4352 [==============================] - 16s - loss: 0.0221 - acc: 0.9959    
    Epoch 3/20
    4352/4352 [==============================] - 15s - loss: 0.0195 - acc: 0.9966    
    Epoch 4/20
    4352/4352 [==============================] - 14s - loss: 0.0166 - acc: 0.9972    
    Epoch 5/20
    4352/4352 [==============================] - 15s - loss: 0.0152 - acc: 0.9975    
    Epoch 6/20
    4352/4352 [==============================] - 15s - loss: 0.0141 - acc: 0.9972    
    Epoch 7/20
    4352/4352 [==============================] - 15s - loss: 0.0133 - acc: 0.9972    
    Epoch 8/20
    4352/4352 [==============================] - 16s - loss: 0.0152 - acc: 0.9972    
    Epoch 9/20
    4352/4352 [==============================] - 14s - loss: 0.0116 - acc: 0.9977    
    Epoch 10/20
    4352/4352 [==============================] - 15s - loss: 0.0110 - acc: 0.9977    
    Epoch 11/20
    4352/4352 [==============================] - 15s - loss: 0.0113 - acc: 0.9977    
    Epoch 12/20
    4352/4352 [==============================] - 15s - loss: 0.0110 - acc: 0.9975    
    Epoch 13/20
    4352/4352 [==============================] - 15s - loss: 0.0102 - acc: 0.9979    
    Epoch 14/20
    4352/4352 [==============================] - 15s - loss: 0.0098 - acc: 0.9982    
    Epoch 15/20
    4352/4352 [==============================] - 14s - loss: 0.0106 - acc: 0.9979    
    Epoch 16/20
    4352/4352 [==============================] - 14s - loss: 0.0108 - acc: 0.9977    
    Epoch 17/20
    4352/4352 [==============================] - 16s - loss: 0.0094 - acc: 0.9979    
    Epoch 18/20
    4352/4352 [==============================] - 15s - loss: 0.0096 - acc: 0.9979    
    Epoch 19/20
    4352/4352 [==============================] - 15s - loss: 0.0100 - acc: 0.9979    
    Epoch 20/20
    4352/4352 [==============================] - 15s - loss: 0.0082 - acc: 0.9984    





    <keras.callbacks.History at 0x127d38cc0>



By compiling, we are configure the model with the `adam` optimizer and the `binary_crossentropy` loss function. Additionally, we can also monitor the accuracy during the training by passing `['accuracy']` to the `metrics` argument.  

The `optimizer` and the `loss` are two arguments that are required if we want to compile the model. Some of the most popular optimization algorithms used are the Stochastic Gradient Descent (SGD), ADAM and RMSprop. Depending on whichever algorithm we choose, we will need to tune certain parameters, such as learning rate or momentum. The choice for a loss function depends on the problem we are trying to solve.  

We will train the model for 20 epochs or iterations over all the samples in `X_train` and `y_train`, in batches of 1 sample. By setting the `verbose` argument to `1`, we are indicate that we want to see the progress bar logging.  

An epoch is a single pass through the entire training set, followed by testing of the verification set. The batch size that we specify in the code above defines the number of samples that going to be propagated through the network. Also, by doing this, we optimize the efficiency because we are making sure that we don't load too many input patterns into memory at the same time.

# 9. Predict Values
We can make predictions for the labels of the test set by using `predict()` and passing the test set to it. In this case, the result is stored in `y_pred`:


```python
y_pred = model.predict(X_test)
```

Before we go and evaluate our model, we can already get a quick idea of the accuracy by checking how `y_pred` and `y_test` compare:


```python
print("y_pred:")
print(y_pred[:5])
print("\n""y_test:")
print(y_test[:5])
```

    y_pred:
    [[  1.23926289e-02]
     [  8.67547810e-01]
     [  2.65277573e-04]
     [  1.73703957e-05]
     [  1.84766762e-07]]
    
    y_test:
    [0 1 0 0 0]


# 10. Evaluate Model
We will use `evaluate()` and pass in the test data and test labels to evaluate our model's performance.


```python
score = model.evaluate(X_test, y_test,verbose=1)

print(score)
```

    1504/2145 [====================>.........] - ETA: 0s[0.027259037624980813, 0.99487179487179489]


The score is a list that holds the combination of the loss and the accuracy. In this case, we see that both seem very great, but we also need to remember that our data is somewhat imbalanced, we had more white wine than red wine observations. The accuracy might just be reflecting the class distribution of our data because it will just predict `white` because those observations are abundantly present.  

Before we start re-arranging the data and putting it together in different ways, it’s always a good idea to try out different evaluation metrics. For this, we will test out some basic classification evaluation techniques, such as:  

- The `confusion matrix`, which is a breakdown of predictions into a table showing correct predictions and the types of incorrect predictions made. Ideally, we will only see numbers in the diagonal, which means that all our predictions were correct.
- `Precision` is a measure of a classifier’s exactness. The higher the precision, the more accurate the classifier.
- `Recall` is a measure of a classifier’s completeness. The higher the recall, the more cases the classifier covers.
- The `F1 Score` or `F-score` is a weighted average of precision and recall.
- The `Kappa` or `Cohen’s kappa` is the classification accuracy normalized by the imbalance of the classes in the data.


```python
# Import the modules from `sklearn.metrics`
from sklearn.metrics import confusion_matrix, precision_score, recall_score, f1_score, cohen_kappa_score

# Confusion matrix
print("Confusion Matrix:")
print(confusion_matrix(y_test, y_pred))
```

array([[1585,    3],
       [   8,  549]])


```python
# Precision 
print("\n""Precision:")
print(precision_score(y_test, y_pred))
```

0.994565217391


```python
# Recall
print("\n""Recall:")
print(recall_score(y_test, y_pred))
```

0.98563734290843807


```python
# F1 score
print("\n""F1 Score:")
print(f1_score(y_test,y_pred))
```

0.99008115419296661


```python
# Cohen's kappa
print("\n""Cohen's Kappa:")
print(cohen_kappa_score(y_test, y_pred))
```

0.98662321692498967

All of our scores are very good, we have made a pretty accurate model despite the fact that we have considerably more rows that are of the white wine type.

# 11. Further Modeling 
We have successfully built our first model, but we can go even further with experimenting. The two key architectural decisions that we need to make involve the layers and the hidden nodes. We can start by testing the following:  

- Try to use 2 or 3 hidden layers
- Use layers with more hidden units or less hidden units
- Take the quality column as the target labels and the rest of the data including the encoded type column as our data. Which leaves us with a multi-class classification problem to solve.  
- Try changing out the activation function by using the `tanh` activation function instead of `relu`.

# 12. Predicting Wine Quality

** Lets build a neural networks to predict wine quality based on its chemical properties.**  

Here we assume that `quality` is a continuous variable: the task is then not a `binary classification` task but an `ordinal regression` task. Ordinal Regression is a type of regression that is used for predicting an ordinal variable: the `quality` value exists on an arbitrary scale where the relative ordering between the different `quality` values is significant. The quality scale 0-10 for “very bad” to “very good” is an example.  

Our target labels are going to be the `quality` column in our `red` and `white` DataFrames and will require some additional preprocessing.

## 12.1 Preprocess Data

Since the `quality` variable becomes our target class, we will now need to isolate the quality labels from the rest of the data set. We will put `wines.quality` in a different variable `y` and will put the rest of the wine data in a variable `x`.  

We will use `StandardScaler` to make sure that our data is in a good place before we fit the data to the model.  

Then we will split the data into train and test sets by utilizing `k-fold` validation, which requires us to split up the data into `K` partitions. Usually, `K` is set at 4 or 5. Finally, we can instantiate identical models and train each one on a partition, while also evaluating on the remaining partitions. The validation score for the model is then an average of the `K` validation scores obtained.  


```python
# Isolate target labels
y = wines.quality

# Isolate data
X = wines.drop('quality', axis=1) 
```

We also need to perform the scaling again because we had a lot of differences in some of the values for our `red`, `white` data.  


```python
# Scale the data with `StandardScaler`
X = StandardScaler().fit_transform(X)
```

Time to start modeling our neural network.

## 12.2 Model Neural Network Architecture
Our first layer is our input layer and we will need to pass the shape of our input data to it. We will make use of `input_dim` to pass the dimensions of the input data to the `Dense` layer.


```python
# Import `Sequential` from `keras.models`
from keras.models import Sequential

# Import `Dense` from `keras.layers`
from keras.layers import Dense

# Initialize the model
model = Sequential()

# Add input layer 
model.add(Dense(64, input_dim=12, activation='relu'))
    
# Add output layer 
model.add(Dense(1))
```

Our input layer needs to know the input dimension of the data, so we will pass in the input dimensions, which is 12. We also need to remember that we are counting the `Type` columns that were generated in our first model.  

We will use the `relu` activation function, with no bias involved, and the number of hidden units will be `64`.  

Our network ends with a single unit `Dense(1)`, and doesn’t include an activation. This is a typical setup for `scalar regression`, where we are trying to predict a single continuous value.

## 12.3 Compile The Model, Fit The Data
With our model at hand, we can now compile and fit the data to it with the `K` fold validation partitions:


```python
import numpy as np
from sklearn.model_selection import StratifiedKFold

seed = 7
np.random.seed(seed)

kfold = StratifiedKFold(n_splits=5, shuffle=True, random_state=seed)
for train, test in kfold.split(X, y):
    model = Sequential()
    model.add(Dense(64, input_dim=12, activation='relu'))
    model.add(Dense(1))
    model.compile(optimizer='rmsprop', loss='mse', metrics=['mae'])
    model.fit(X[train], y[train], epochs=10, verbose=1)
```

    Epoch 1/10
    5195/5195 [==============================] - 0s - loss: 14.2885 - mean_absolute_error: 3.3583     
    Epoch 2/10
    5195/5195 [==============================] - 0s - loss: 1.6251 - mean_absolute_error: 0.9938     
    Epoch 3/10
    5195/5195 [==============================] - 0s - loss: 1.0007 - mean_absolute_error: 0.7737     
    Epoch 4/10
    5195/5195 [==============================] - 0s - loss: 0.7484 - mean_absolute_error: 0.6665     
    Epoch 5/10
    5195/5195 [==============================] - 0s - loss: 0.6334 - mean_absolute_error: 0.6156     
    Epoch 6/10
    5195/5195 [==============================] - 0s - loss: 0.5716 - mean_absolute_error: 0.5859     
    Epoch 7/10
    5195/5195 [==============================] - 0s - loss: 0.5392 - mean_absolute_error: 0.5709     
    Epoch 8/10
    5195/5195 [==============================] - 0s - loss: 0.5210 - mean_absolute_error: 0.5611     
    Epoch 9/10
    5195/5195 [==============================] - 0s - loss: 0.5098 - mean_absolute_error: 0.5565     
    Epoch 10/10
    5195/5195 [==============================] - 0s - loss: 0.5004 - mean_absolute_error: 0.5520     - ETA: 0s - loss: 0.4703 - mean_absolute_error: 0.
    Epoch 1/10
    5197/5197 [==============================] - 0s - loss: 12.4811 - mean_absolute_error: 3.0996     
    Epoch 2/10
    5197/5197 [==============================] - 0s - loss: 1.4883 - mean_absolute_error: 0.9524     
    Epoch 3/10
    5197/5197 [==============================] - 0s - loss: 0.9228 - mean_absolute_error: 0.7451     
    Epoch 4/10
    5197/5197 [==============================] - 0s - loss: 0.7208 - mean_absolute_error: 0.6578     
    Epoch 5/10
    5197/5197 [==============================] - 0s - loss: 0.6226 - mean_absolute_error: 0.6112     
    Epoch 6/10
    5197/5197 [==============================] - 0s - loss: 0.5700 - mean_absolute_error: 0.5861     
    Epoch 7/10
    5197/5197 [==============================] - 0s - loss: 0.5395 - mean_absolute_error: 0.5695     
    Epoch 8/10
    5197/5197 [==============================] - 0s - loss: 0.5199 - mean_absolute_error: 0.5597     
    Epoch 9/10
    5197/5197 [==============================] - 0s - loss: 0.5092 - mean_absolute_error: 0.5533     
    Epoch 10/10
    5197/5197 [==============================] - 0s - loss: 0.4996 - mean_absolute_error: 0.5487     
    Epoch 1/10
    5197/5197 [==============================] - 0s - loss: 13.3672 - mean_absolute_error: 3.2182     
    Epoch 2/10
    5197/5197 [==============================] - 0s - loss: 1.5880 - mean_absolute_error: 0.9708     
    Epoch 3/10
    5197/5197 [==============================] - 0s - loss: 0.9822 - mean_absolute_error: 0.7553     
    Epoch 4/10
    5197/5197 [==============================] - 0s - loss: 0.7341 - mean_absolute_error: 0.6579     
    Epoch 5/10
    5197/5197 [==============================] - 0s - loss: 0.6144 - mean_absolute_error: 0.6033     
    Epoch 6/10
    5197/5197 [==============================] - 0s - loss: 0.5527 - mean_absolute_error: 0.5755     
    Epoch 7/10
    5197/5197 [==============================] - 0s - loss: 0.5230 - mean_absolute_error: 0.5603     
    Epoch 8/10
    5197/5197 [==============================] - 0s - loss: 0.5081 - mean_absolute_error: 0.5551     
    Epoch 9/10
    5197/5197 [==============================] - 0s - loss: 0.4966 - mean_absolute_error: 0.5446     
    Epoch 10/10
    5197/5197 [==============================] - 0s - loss: 0.4881 - mean_absolute_error: 0.5421     
    Epoch 1/10
    5199/5199 [==============================] - 0s - loss: 12.9529 - mean_absolute_error: 3.1443     
    Epoch 2/10
    5199/5199 [==============================] - 0s - loss: 1.5940 - mean_absolute_error: 0.9716     
    Epoch 3/10
    5199/5199 [==============================] - 0s - loss: 1.0038 - mean_absolute_error: 0.7739     - ETA: 0s - loss: 1.0516 - mean_absolute_error: 0.
    Epoch 4/10
    5199/5199 [==============================] - 0s - loss: 0.7599 - mean_absolute_error: 0.6665     
    Epoch 5/10
    5199/5199 [==============================] - 0s - loss: 0.6336 - mean_absolute_error: 0.6078     
    Epoch 6/10
    5199/5199 [==============================] - 0s - loss: 0.5754 - mean_absolute_error: 0.5806     
    Epoch 7/10
    5199/5199 [==============================] - 0s - loss: 0.5403 - mean_absolute_error: 0.5648     
    Epoch 8/10
    5199/5199 [==============================] - 0s - loss: 0.5177 - mean_absolute_error: 0.5536     
    Epoch 9/10
    5199/5199 [==============================] - 0s - loss: 0.5049 - mean_absolute_error: 0.5502     
    Epoch 10/10
    5199/5199 [==============================] - 0s - loss: 0.4943 - mean_absolute_error: 0.5436     
    Epoch 1/10
    5200/5200 [==============================] - 0s - loss: 11.7594 - mean_absolute_error: 2.9585     
    Epoch 2/10
    5200/5200 [==============================] - 0s - loss: 1.6008 - mean_absolute_error: 0.9801     
    Epoch 3/10
    5200/5200 [==============================] - 0s - loss: 1.0165 - mean_absolute_error: 0.7773     
    Epoch 4/10
    5200/5200 [==============================] - 0s - loss: 0.7624 - mean_absolute_error: 0.6708     
    Epoch 5/10
    5200/5200 [==============================] - 0s - loss: 0.6438 - mean_absolute_error: 0.6162     
    Epoch 6/10
    5200/5200 [==============================] - 0s - loss: 0.5871 - mean_absolute_error: 0.5885     
    Epoch 7/10
    5200/5200 [==============================] - 0s - loss: 0.5504 - mean_absolute_error: 0.5729     
    Epoch 8/10
    5200/5200 [==============================] - 0s - loss: 0.5221 - mean_absolute_error: 0.5578     
    Epoch 9/10
    5200/5200 [==============================] - 0s - loss: 0.5104 - mean_absolute_error: 0.5518     
    Epoch 10/10
    5200/5200 [==============================] - 0s - loss: 0.4994 - mean_absolute_error: 0.5460     


We use the `compile()` function to compile the model and then the `fit()` function to fit the model to the data. When we compile the model we need to make sure that we define at minimum the `optimizer` and `loss` arguments. We then pass `rsmprop` to our optimizer, and `mse` to the loss function.  

The additional `metrics` argument that we define is actually a function that is used to judge the performance of our model. For regression problems, it’s very common to take the `Mean Absolute Error (MAE)` as a metric. Finally, we determine how many epochs we want to run the fitting.

## 12.4 Evaluate Model
We can evaluate our model by making use of the `Mean Squared Error (MSE)` and the `Mean Absolute Error (MAE)`.


```python
mse_value, mae_value = model.evaluate(X[test], y[test], verbose=0)

print("Mean Squared Error:")
print(mse_value)
print("\n""Mean Absolute Error:")
print(mae_value)
```

    Mean Squared Error:
    0.521800081686
    
    Mean Absolute Error:
    0.561619131769


Note that besides the `MSE` and `MAE` scores, we could also use the `R2 score` or the `regression score` function.


```python
from sklearn.metrics import r2_score

r2_score(y[test], y_pred)
```

0.3125092543

Our model didn't perform has well as we hoped, so we are going to experiment with optimizing the code so that the results become a little bit better.

## 12.5 Model Fine-Tuning
Most of the time we will need to fine-tune our model because not all problems are as straightforward as we would like them to be.


```python
seed = 7
np.random.seed(seed)

kfold = StratifiedKFold(n_splits=5, shuffle=True, random_state=seed)
for train, test in kfold.split(X, y):
    model = Sequential()
    model.add(Dense(64, input_dim=12, activation='relu'))
    model.add(Dense(64, activation='relu'))
    model.add(Dense(1))
    model.compile(optimizer='rmsprop', loss='mse', metrics=['mae'])
    model.fit(X[train], y[train], epochs=10, verbose=1)
```

**Hidden Units**  
We can try adding more hidden units to our model’s architecture and study the effect on the evaluation:


```python
seed = 7
np.random.seed(seed)

kfold = StratifiedKFold(n_splits=5, shuffle=True, random_state=seed)
for train, test in kfold.split(X, y):
    model = Sequential()
    model.add(Dense(128, input_dim=12, activation='relu'))
    model.add(Dense(1))
    model.compile(optimizer='rmsprop', loss='mse', metrics=['mae'])
    model.fit(X[train], y[train], epochs=10, verbose=1)
```

Something to keep in mind is that, because we don’t have a ton of data, we are more prone to overfitting. That’s why we should work with a small network.

# 13. More Experiments: Optimization Parameters  
Besides adding layers and playing around with the hidden units, we can also try to adjust some of the parameters of the optimization algorithm that we give to the `compile()` function. We can try, for example, importing `RMSprop` from `keras.models` and adjust the learning rate `lr`:


```python
from keras.optimizers import RMSprop

seed = 7
np.random.seed(seed)

rmsprop = RMSprop(lr=0.0001)

kfold = StratifiedKFold(n_splits=5, shuffle=True, random_state=seed)
for train, test in kfold.split(X, y):
    model = Sequential()
    model.add(Dense(12, input_dim=12, activation='relu'))
    model.add(Dense(1))
    model.compile(optimizer=rmsprop, loss='mse', metrics=['mae'])
    model.fit(X[train], y[train], epochs=10, verbose=1)
```

Using the Stochastic Gradient Descent (SGD) optimization algorithm:


```python
from keras.optimizers import SGD, RMSprop

seed = 7
np.random.seed(seed)

sgd=SGD(lr=0.1)

kfold = StratifiedKFold(n_splits=5, shuffle=True, random_state=seed)
for train, test in kfold.split(X, y):
    model = Sequential()
    model.add(Dense(12, input_dim=12, activation='relu'))
    model.add(Dense(1))
    model.compile(optimizer=sgd, loss='mse', metrics=['mae'])
    model.fit(X[train], y[train], epochs=10, verbose=1)
```
