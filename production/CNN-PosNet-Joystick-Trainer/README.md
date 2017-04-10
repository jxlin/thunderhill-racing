### Joystick commander and Agile trainer with Polysync Main Node

The work done in this section is based heavily on work done from John Chen on the behavioral cloning project from the Self-Driving Car Nano degree: [John's Agile Trainer](https://github.com/diyjac/AgileTrainer)

## Communicating with Polysync and the Kia Soul:

To be able to get data to and from polysync and the Kia Soul we had a simple architecture:

```

-------------------
|  data_buffer    |
|                 |
-------------------
        |
-------------------
|  client_node    |
|                 |
-------------------

```

The first portion consists of a data buffer which is essentially a queue to hold our data up to a limit we have specified in the data_buffer, when the queue hits that limit it will start sending data or poping whatever it contains to polysync.


In the Client Node, there are three distictive parallel threads:

```
-----------------------
|  make_prediction    | --> Gets data from the car as well to predict and send values using the third thread to make the car drive 
|                     |
-----------------------
        
-------------------
|  train_model    | --> Takes in values from the car and feeds into a preloaded trained model to keep training 
|                 |
-------------------

-------------------
|  send_values    | --> Communicates with the Main Node in Polysync to send values like the throttle and brake
|                 |  
-------------------

```

## Python Trainer:

As it turns out, in order for the in place python trainer to re-train the model with a different learning rate, you need to create a seperate model.json and model.h5 weight file.  Otherwise, any retraining will only use the original optimizer and learning rate compiled into the h5 model and weight combo file.  The way to do this is to use the new script `modelh52json.py`.  Below is an example execution.

```
python modelh52json.py psyncPosNet.h5 modelTrained.json
```

After the model has been retrained, you can move it back into a model and weight combo h5 by using the `modeljson2h5.py` script.  Below is an example execution.

```
python modeljson2h5.py modelTrained.json psyncPosNet-retrained.h5
```

To run:

1. in MainNode folder:
```
cmake .
make
```
2. in SmallController folder:
```
python clientnode.py
```
3. start session in PS studio

