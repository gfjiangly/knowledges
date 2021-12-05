# How to get the weights of Keras model?


Keras has implemented some functions for getting or setting weights for every layer. 

- **layer.get_weights()**: returns the weights of the layer as a list of Numpy arrays.
- **layer.set_weights(weights)**: sets the weights of the layer from a list of Numpy arrays.

Using these functions you can write a piece of code to get all layers' weights

```python
for layer in model.layers:
    weights = layer.get_weights() # list of numpy arrays
```

Or you can get the weights right from the model

```python
from keras.models import Sequential

model = Sequential()
# ...
weights = model.get_weights() # returs a numpy list of weights
```

Keras model also has **get_weights()** and **set_weights(weights)** functions like every layer has.

If you need more take a look at this [keras doc](https://keras.io/models/about-keras-models/).

