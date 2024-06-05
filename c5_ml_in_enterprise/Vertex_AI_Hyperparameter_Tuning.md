# Vertex AI: Hyperparameter Tuning

![image](https://cdn.qwiklabs.com/Q4vrDDMIwR4Z2y1thcjFG4RZb0ibmJcNUWpb404B4ao%3D)

## Prework

### Enable the Compute Engine API

1. In the Cloud Console, click Navigation menu > API & Services > Library.

2. Search for Compute Engine API, then click Enable if it isn't already enabled. You'll need this to create your notebook instance.

### Enable the Container Registry API

1. In the Cloud Console, click Navigation menu > API & Services > Library.

2. Search for Google Container Registry API and select Enable if it isn't already. You'll use this to create a container for your custom training job.

### Enable the Notebooks API

1. In the Google Cloud Console, in the Navigation menu, click APIs & Services > Library.

2. Search for Notebooks API and press ENTER.

3. Click on the Notebooks API result.

4. If the API is not enabled, click Enable.

### Enable the Vertex AI API

In the Google Cloud Console, on the Navigation menu, click Vertex AI > Dashboard, and click Enable Vertex AI API.

## Task 1. Launch Vertex AI Notebooks

## Task 2. Containerize training application code

### Create a Dockerfile

```Dockerfile
FROM gcr.io/deeplearning-platform-release/tf2-gpu.2-9

WORKDIR /

# Installs hypertune library
RUN pip install cloudml-hypertune

# Copies the trainer code to the docker image.
COPY trainer /trainer

# Sets up the entry point to invoke the trainer.
ENTRYPOINT ["python", "-m", "trainer.task"]
```

### Add model training code

```python
import tensorflow as tf
import tensorflow_datasets as tfds
import argparse
import hypertune

NUM_EPOCHS = 10


def get_args():
  '''Parses args. Must include all hyperparameters you want to tune.'''

  parser = argparse.ArgumentParser()
  parser.add_argument(
      '--learning_rate',
      required=True,
      type=float,
      help='learning rate')
  parser.add_argument(
      '--momentum',
      required=True,
      type=float,
      help='SGD momentum value')
  parser.add_argument(
      '--num_neurons',
      required=True,
      type=int,
      help='number of units in last hidden layer')
  args = parser.parse_args()
  return args


def preprocess_data(image, label):
  '''Resizes and scales images.'''

  image = tf.image.resize(image, (150,150))
  return tf.cast(image, tf.float32) / 255., label


def create_dataset():
  '''Loads Horses Or Humans dataset and preprocesses data.'''

  data, info = tfds.load(name='horses_or_humans', as_supervised=True, with_info=True)

  # Create train dataset
  train_data = data['train'].map(preprocess_data)
  train_data  = train_data.shuffle(1000)
  train_data  = train_data.batch(64)

  # Create validation dataset
  validation_data = data['test'].map(preprocess_data)
  validation_data  = validation_data.batch(64)

  return train_data, validation_data


def create_model(num_neurons, learning_rate, momentum):
  '''Defines and complies model.'''

  inputs = tf.keras.Input(shape=(150, 150, 3))
  x = tf.keras.layers.Conv2D(16, (3, 3), activation='relu')(inputs)
  x = tf.keras.layers.MaxPooling2D((2, 2))(x)
  x = tf.keras.layers.Conv2D(32, (3, 3), activation='relu')(x)
  x = tf.keras.layers.MaxPooling2D((2, 2))(x)
  x = tf.keras.layers.Conv2D(64, (3, 3), activation='relu')(x)
  x = tf.keras.layers.MaxPooling2D((2, 2))(x)
  x = tf.keras.layers.Flatten()(x)
  x = tf.keras.layers.Dense(num_neurons, activation='relu')(x)
  outputs = tf.keras.layers.Dense(1, activation='sigmoid')(x)
  model = tf.keras.Model(inputs, outputs)
  model.compile(
      loss='binary_crossentropy',
      optimizer=tf.keras.optimizers.SGD(learning_rate=learning_rate, momentum=momentum),
      metrics=['accuracy'])
  return model


def main():
  args = get_args()
  train_data, validation_data = create_dataset()
  model = create_model(args.num_neurons, args.learning_rate, args.momentum)
  history = model.fit(train_data, epochs=NUM_EPOCHS, validation_data=validation_data)

  # DEFINE METRIC
  hp_metric = history.history['val_accuracy'][-1]

  hpt = hypertune.HyperTune()
  hpt.report_hyperparameter_tuning_metric(
      hyperparameter_metric_tag='accuracy',
      metric_value=hp_metric,
      global_step=NUM_EPOCHS)


if __name__ == "__main__":
    main()
```

### Build the container

```bash
PROJECT_ID='your-cloud-project'

IMAGE_URI="gcr.io/$PROJECT_ID/horse-human:hypertune"

docker build ./ -t $IMAGE_URI

docker push $IMAGE_URI
```

## Task 3. Run a hyperparameter tuning job on Vertex AI

### Configure training job

1. Click Train new model to enter the parameters for your hyperparameter tuning job:

    - Under Dataset, select No managed dataset.

    - Select Custom training (advanced) as your training method and click Continue.

    - Enter horses-humans-hyptertune (or whatever you'd like to call your model) for Model name.

    - Click Continue.

2. In the Training container step, select Custom container:

    - In the Custom container settings, for Container image, enter the value of your IMAGE_URI variable from the previous section. It should be: `gcr.io/<your-cloud-project>/horse-human:hypertune`, with your own project name. Leave the rest of the fields blank and click Continue.

### Configure hyperparameter tuning job

- Select Enable hyperparameter tuning.

#### Configure hyperparameters

Next, you'll need to add the hyperparameters that you set as command line arguments in the training application code. When adding a hyperparameter, you'll first need to provide the name. This should match the argument name that you passed to argparse.

##### For learning_rate

1. Enter learning_rate for Parameter name.

2. Select Double as Type.

3. Enter 0.01 for Min, and 1 for Max.

4. Select Log as Scaling.

5. Click DONE.

6. After adding the learning_rate hyperparameter, add parameters for momentum and num_neurons.

##### For momentum

1. Click ADD A HYPERPARAMETER.

2. Enter momentum for Parameter name.

3. Select Double as Type.

4. Enter 0 for Min, and 1 for Max.

5. Select Linear as Scaling.

6. Click DONE.

##### For num_neurons

1. Click ADD A HYPERPARAMETER.

2. Enter num_neurons for Parameter name.

3. Select Discrete as Type.

4. Enter 64,128,512 for Values.

5. Select No scaling as Scaling.

6. Click DONE.

![image](https://cdn.qwiklabs.com/le2h%2BfzoEwIUSdOAxX7T2CR4sqnh1U%2FtcLAQ0YFtUF4%3D)

## Task 4. Cleanup
