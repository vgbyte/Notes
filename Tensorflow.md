# Tensorflow

# Table of contents

- [Tensorflow](#tensorflow)
  - [Tensors](#tensors)
    - [About shapes](#about-shapes)
    - [Operation](#operation)
    - [Manipulating Shapes](#manipulating-shapes)
  - [Algorithms](#algorithms)
    - [Linear Regression](#linear-regression)
      - [Feature Columns](#feature-columns)
      - [The Training Process](#the-training-process)
      - [Input Function](#input-function)
      - [Creating the Model](#creating-the-model)
      - [Training the Model](#training-the-model)
      - [Predicting](#predicting)
    - [Classification](#classification)
      - [Input Function](#input-function)
      - [Feature Columns](#feature-columns)
      - [Building the Model](#building-the-model)
      - [Training](#training)
      - [Evaluation](#evaluation)
      - [Predictions](#predictions)
    - [Hidden Markov Models](#hidden-markov-models)
      - [Building a model](#building-a-model)



```python
import tensorflow as tf
import numpy as np
```

## Tensors

```python
# This will be an int32 tensor by default; see "dtypes" below.
rank_0_tensor = tf.constant(4)
```

```python
# Let's make this a float tensor.
rank_1_tensor = tf.constant([2.0, 3.0, 4.0])
```

```python
# If you want to be specific, you can set the dtype (see below) at creation time
rank_2_tensor = tf.constant([[1, 2],
                             [3, 4],
                             [5, 6]], dtype=tf.float16)
```

### About shapes

Tensors have shapes. Some vocabulary:

- **Shape**: The length (number of elements) of each of the axes of a tensor.
- **Rank**: Number of tensor axes. A scalar has rank 0, a vector has rank 1, a matrix is rank 2.
- **Axis** or **Dimension**: A particular dimension of a tensor.
- **Size**: The total number of items in the tensor, the product shape vector.

**Note:** Although you may see reference to a "tensor of two dimensions", a rank-2 tensor does not usually describe a 2D space.

```python
rank_4_tensor = tf.zeros([3, 2, 4, 5])

print("Type of every element:", rank_4_tensor.dtype)
print("Number of axes:", rank_4_tensor.ndim)
print("Shape of tensor:", rank_4_tensor.shape)
print("Elements along axis 0 of tensor:", rank_4_tensor.shape[0])
print("Elements along the last axis of tensor:", rank_4_tensor.shape[-1])
print("Total number of elements (3*2*4*5): ", tf.size(rank_4_tensor).numpy())

***
# Type of every element: <dtype: 'float32'>
# Number of axes: 4
# Shape of tensor: (3, 2, 4, 5)
# Elements along axis 0 of tensor: 3
# Elements along the last axis of tensor: 5
# Total number of elements (3*2*4*5):  120
***
```

<img src="https://www.tensorflow.org/guide/images/tensor/shape.png"><img src="https://www.tensorflow.org/guide/images/tensor/shape2.png"><img src="https://www.tensorflow.org/guide/images/tensor/4-axis_block.png">

### Operation

```python
a = tf.constant([[1, 2],
                 [3, 4]])
b = tf.constant([[1, 1],
                 [1, 1]]) # Could have also said `tf.ones([2,2])`

print(tf.add(a, b), "\n")
print(tf.multiply(a, b), "\n")
print(tf.matmul(a, b), "\n")
```

```
tf.Tensor(
[[2 3]
 [4 5]], shape=(2, 2), dtype=int32) 

tf.Tensor(
[[1 2]
 [3 4]], shape=(2, 2), dtype=int32) 

tf.Tensor(
[[3 3]
 [7 7]], shape=(2, 2), dtype=int32)
```

### Manipulating Shapes

Reshaping a tensor is of great utility.

```python
# Shape returns a `TensorShape` object that shows the size along each axis
x = tf.constant([[1], [2], [3]])
print(x.shape) # (3, 1)
# You can convert this object into a Python list, too
print(x.shape.as_list()) # [3, 1]
```

Typically the only reasonable use of [`tf.reshape`](https://www.tensorflow.org/api_docs/python/tf/reshape) is to combine or split adjacent axes (or add/remove `1`s).

You can reshape a tensor into a new shape. The [`tf.reshape`](https://www.tensorflow.org/api_docs/python/tf/reshape) operation is fast and cheap as the underlying data does not need to be duplicated.

```python
# You can reshape a tensor to a new shape.
# Note that you're passing in a list
reshaped = tf.reshape(x, [1, 3])
print(x.shape) # (3, 1)
print(reshaped.shape) # (1, 3)
```

<img src="https://www.tensorflow.org/guide/images/tensor/reshape-before.png">

**Some good reshapes for above tensor**

<img src="https://www.tensorflow.org/guide/images/tensor/reshape-good1.png"><img src="https://www.tensorflow.org/guide/images/tensor/reshape-good2.png">

## Algorithms

### Linear Regression

Linear regression is one of the most basic forms of machine learning and is used to predict numeric values.

#### Feature Columns

In our dataset we have two different kinds of information: **Categorical and Numeric**

Our **categorical data** is anything that is not numeric! For example, the sex column does not use numbers, it uses the words "male" and "female".

Before we continue and create/train a model we must convet our categorical data into numeric data. We can do this by encoding each category with an integer (ex. male = 1, female = 2).

Fortunately for us TensorFlow has some tools to help!

```python
CATEGORICAL_COLUMNS = ['sex', 'n_siblings_spouses', 'parch', 'class', 'deck',
                       'embark_town', 'alone']
NUMERIC_COLUMNS = ['age', 'fare']

feature_columns = []
for feature_name in CATEGORICAL_COLUMNS:
  vocabulary = dftrain[feature_name].unique()  # gets a list of all unique values from given feature column
  feature_columns.append(tf.feature_column.categorical_column_with_vocabulary_list(feature_name, vocabulary)) # Label Encoder
  feature_columns.append(tf.feature_column.indicator_column(tf.feature_column.categorical_column_with_vocabulary_list(feature_name, vocabulary))) # one hot encoder

for feature_name in NUMERIC_COLUMNS:
  feature_columns.append(tf.feature_column.numeric_column(feature_name, dtype=tf.float32))
```

#### The Training Process

So, we are almost done preparing our dataset and I feel as though it's a good time to explain how our model is trained. Specifically, how input data is fed to our model.

For this specific model data is going to be streamed into it in small **batches** of 32. This means we will not feed the entire dataset to our model at once, but simply small batches of entries. We will feed these batches to our model multiple times according to the number of **epochs**.

An **epoch** is simply one stream of our entire dataset. The number of epochs we define is the amount of times our model will see the entire dataset. We use multiple epochs in hope that after seeing the same data multiple times the model will better determine how to estimate it.
Ex. if we have 10 ephocs, our model will see the same dataset 10 times.

Since we need to feed our data in batches and multiple times, we need to create something called an **input function**. The input function simply defines how our dataset will be converted into batches at each epoch.

#### Input Function

The TensorFlow model we are going to use requires that the data we pass it comes in as a `tf.data.Dataset` object. This means we must create a *input function* that can convert our current pandas dataframe into that object.

Below you'll see a seemingly complicated input function, this is straight from the TensorFlow documentation (https://www.tensorflow.org/tutorials/estimator/linear).

```python
def make_input_fn(data_df, label_df, num_epochs=10, shuffle=True, batch_size=32):
  def input_function():  # inner function, this will be returned
    ds = tf.data.Dataset.from_tensor_slices((dict(data_df), label_df))  # create tf.data.Dataset object with data and its label
    if shuffle:
      ds = ds.shuffle(1000)  # randomize order of data
    ds = ds.batch(batch_size).repeat(num_epochs)  # split dataset into batches of 32 and repeat process for number of epochs
    return ds  # return a batch of the dataset
  return input_function  # return a function object for use

train_input_fn = make_input_fn(dftrain, y_train)  # here we will call the input_function that was returned to us to get a dataset object we can feed to the model
eval_input_fn = make_input_fn(dfeval, y_eval, num_epochs=1, shuffle=False)
```

#### Creating the Model

Creating linear regression algorithm is pretty easy! Have a look below.

```python
linear_est = tf.estimator.LinearClassifier(feature_columns=feature_columns)
# We create a linear estimtor by passing the feature columns we created earlier
```

#### Training the Model

Training the model is as easy as passing the input functions that we created earlier.

```python
linear_est.train(train_input_fn)  # train
result = linear_est.evaluate(eval_input_fn)  # get model metrics/stats by testing on tetsing data

clear_output()  # clears console output
print(result['accuracy'])  # the result variable is simply a dict of stats about our model
```

#### Predicting

```python
pred_dicts = list(linear_est.predict(eval_input_fn))
```

### Classification

Classification is used to seperate data points into classes of different labels. In this example we will use a TensorFlow estimator to classify flowers.
This section is based on the following guide from the TensorFlow website. https://www.tensorflow.org/tutorials/estimator/premade

#### Input Function

```python
def input_fn(features, labels, training=True, batch_size=256):
    # Convert the inputs to a Dataset.
    dataset = tf.data.Dataset.from_tensor_slices((dict(features), labels))

    # Shuffle and repeat if you are in training mode.
    if training:
        dataset = dataset.shuffle(1000).repeat()
    
    return dataset.batch(batch_size)
```

#### Feature Columns

```python
# Feature columns describe how to use the input.
my_feature_columns = []
for key in train.keys():
    my_feature_columns.append(tf.feature_column.numeric_column(key=key))
print(my_feature_columns)
```

#### Building the Model

And now we are ready to choose a model. For classification tasks there are variety of different estimators/models that we can pick from. Some options are listed below.

- `DNNClassifier` (Deep Neural Network)
- `LinearClassifier`

```python
# Build a DNN with 2 hidden layers with 30 and 10 hidden nodes each.
classifier = tf.estimator.DNNClassifier(
    feature_columns=my_feature_columns,
    # Two hidden layers of 30 and 10 nodes respectively.
    hidden_units=[30, 10],
    # The model must choose between 3 classes.
    n_classes=3)
```

What we've just done is created a deep neural network that has two hidden layers. These layers have 30 and 10 neurons respectively. This is the number of neurons the TensorFlow official tutorial uses so we'll stick with it. However, it is worth mentioning that the number of hidden neurons is an arbitrary number and many experiments and tests are usually done to determine the best choice for these values.

#### Training

```python
classifier.train(
    input_fn=lambda: input_fn(train, train_y, training=True),
    steps=5000)
# We include a lambda to avoid creating an inner function like we did in case of Linear Classifier
```

**Steps** argument simply tells the classifier to run for 5000 steps. 

#### Evaluation

```python
eval_result = classifier.evaluate(
    input_fn=lambda: input_fn(test, test_y, training=False))

print('\nTest set accuracy: {accuracy:0.3f}\n'.format(**eval_result))
```

#### Predictions

Now that we have a trained model it's time to use it to make predictions. I've written a little script below that allows you to type the features of a flower and see a prediction for its class.

```python
def input_fn(features, batch_size=256):
    # Convert the inputs to a Dataset without labels.
    return tf.data.Dataset.from_tensor_slices(dict(features)).batch(batch_size)

features = ['SepalLength', 'SepalWidth', 'PetalLength', 'PetalWidth']
predict = {}

print("Please type numeric values as prompted.")
for feature in features:
  valid = True
  while valid: 
    val = input(feature + ": ")
    if not val.isdigit(): valid = False

  predict[feature] = [float(val)]

predictions = classifier.predict(input_fn=lambda: input_fn(predict))
for pred_dict in predictions:
    class_id = pred_dict['class_ids'][0]
    probability = pred_dict['probabilities'][class_id]

    print('Prediction is "{}" ({:.1f}%)'.format(
        SPECIES[class_id], 100 * probability))
```

### Hidden Markov Models

"The Hidden Markov Model is a finite set of states, each of which is associated with a (generally multidimensional) probability distribution []. Transitions among the states are governed by a set of probabilities called transition probabilities." (http://jedlik.phy.bme.hu/~gerjanos/HMM/node4.html)

A hidden markov model works with probabilities to predict future events or states. In this section we will learn how to create a hidden markov model that can predict the weather.

*This section is based on the following TensorFlow tutorial.* https://www.tensorflow.org/probability/api_docs/python/tfp/distributions/HiddenMarkovModel

```python
import tensorflow_probability as tfp  # We are using a different module from tensorflow this time
import tensorflow as tf
```

**Weather** **Model**

Taken direclty from the TensorFlow documentation (https://www.tensorflow.org/probability/api_docs/python/tfp/distributions/HiddenMarkovModel).

We will model a simple weather system and try to predict the temperature on each day given the following information.

1. Cold days are encoded by a 0 and hot days are encoded by a 1.
2. The first day in our sequence has an 80% chance of being cold.
3. A cold day has a 30% chance of being followed by a hot day.
4. A hot day has a 20% chance of being followed by a cold day.
5. On each day the temperature is normally distributed with mean and standard deviation 0 and 5 on a cold day and mean and standard deviation 15 and 10 on a hot day.

To model this in TensorFlow we will do the following.

```python
tfd = tfp.distributions  # making a shortcut for later on
initial_distribution = tfd.Categorical(probs=[0.8, 0.2])  # Refer to point 2 above
transition_distribution = tfd.Categorical(probs=[[0.7, 0.3],
                                                 [0.2, 0.8]])  # refer to points 3 and 4 above
observation_distribution = tfd.Normal(loc=[0., 15.], scale=[5., 10.])  # refer to point 5 above, we have a dot after no. because it is axpecting a float no.

# the loc argument represents the mean and the scale is the standard devitation
```

#### Building a model

```python
model = tfd.HiddenMarkovModel(
    initial_distribution=initial_distribution,
    transition_distribution=transition_distribution,
    observation_distribution=observation_distribution,
    num_steps=7)
```

The number of steps represents the number of days that we would like to predict information for. In this case we've chosen 7, an entire week.

To get the **expected temperatures** on each day we can do the following.

```python
mean = model.mean()

# due to the way TensorFlow works on a lower level we need to evaluate part of the graph
# from within a session to see the value of this tensor

# in the new version of tensorflow we need to use tf.compat.v1.Session() rather than just tf.Session()
with tf.compat.v1.Session() as sess:  
  print(mean.numpy())
```

