# Using TensorFlow Models with Amazon EI<a name="ei-tensorflow"></a>

The Amazon EI\-enabled version of TensorFlow and TensorFlow Serving allows you to use Amazon EI accelerators with minimal changes to your TensorFlow code\. The Amazon EI enabled packages are available in the AWS Deep Learning AMI\. You can also download the packages from the [Amazon S3 bucket](https://s3.console.aws.amazon.com/s3/buckets/amazonei-tensorflow/) to build it in to your own Amazon Linux or Ubuntu AMIs, or Docker containers\.

With Amazon EI TensorFlow Serving, the standard TensorFlow Serving interface remains unchanged\. The only difference is that the entry point is a different binary named amazonei\_tensorflow\_model\_server\. 

For more information, see [TensorFlow Serving](https://www.tensorflow.org/serving/)\.

Amazon EI TensorFlow packages for Python 2 and 3 provide an EIPredictor API\. This API function provides you with a flexible way to run models on EI as an alternative to using TensorFlow Serving\.

This release of Amazon EI TensorFlow Serving has been tested to perform well and provide cost\-saving benefits with the following deep learning use cases and network architectures \(and similar variants\):


| Use Case | Example Network Topology | 
| --- | --- | 
|  **Image Recognition**  |  Inception, ResNet, MVCNN   | 
|  **Object Detection**  |  SSD, RCNN  | 
|  **Neural Machine Translation**  | GNMT | 

**Topics**
+ [Amazon EI TensorFlow Serving Example](#tensorflow-example)
+ [Amazon EI TensorFlow Predictor](#tensorflow-predictor)
+ [Amazon EI TensorFlow Predictor Example](#tensor-flow-predictor-example)
+ [Additional Requirements and Considerations](#tensorflow-additional)

## Amazon EI TensorFlow Serving Example<a name="tensorflow-example"></a>

The following is an example you can try for serving different models like ResNet using a Single Shot Detector \(SSD\)\. As a general rule, you need a servable model and client scripts to be already downloaded to your DLAMI\. 

**Activate the TensorFlow Elastic Inference Environment**
+ If you are using the AWS Deep Learning AMI, activate the Python 2\.7 TensorFlow environment\. The example scripts are not compatible with Python 3\.x\.

  ```
  source activate amazonei_tensorflow_p27
  ```

**Serve and Test Inference with an Inception Model**

1. Download the model\.

   ```
   curl -O https://s3-us-west-2.amazonaws.com/aws-tf-serving-ei-example/ssd_resnet.zip
   ```

1. Unzip the model\.

   ```
   unzip ssd_resnet.zip -d /tmp
   ```

1. Download a picture of three dogs to your home directory\.

   ```
   curl -O https://raw.githubusercontent.com/awslabs/mxnet-model-server/master/docs/images/3dogs.jpg
   ```

1. Navigate to the folder where `AmazonEI_TensorFlow_Serving` is installed and run the following command to launch the server\. Note, "model\_base\_path" must be an absolute path\.

   ```
   AmazonEI_TensorFlow_Serving_v1.12_v1 --model_name=ssdresnet --model_base_path=/tmp/ssd_resnet50_v1_coco --port=9000
   ```

1. While the server is running in the foreground, launch another terminal session\. Open a new terminal and activate the TensorFlow environment\. 

   ```
   source activate amazonei_tensorflow_p27
   ```

1. Use your preferred text editor to create a script that has the following content\. Name it `ssd_resnet_client.py`\. This script will take an image filename as a parameter and get a prediction result from the pre\-trained model\.

   ```
   from __future__ import print_function
   import grpc
   import tensorflow as tf
   from PIL import Image
   import numpy as np
   import time
   import os
   from tensorflow_serving.apis import predict_pb2
   from tensorflow_serving.apis import prediction_service_pb2_grpc
   
   tf.app.flags.DEFINE_string('server', 'localhost:9000', 
                              'PredictionService host:port')
   
   tf.app.flags.DEFINE_string('image', '', 'path to image in JPEG format')
    
   FLAGS = tf.app.flags.FLAGS
    
   if(FLAGS.image == ''): 
     print("Supply an Image using '--image [path/to/image]'")
     exit(1)
   
   coco_classes_txt = "https://raw.githubusercontent.com/amikelive/coco-labels/master/coco-labels-paper.txt"
    
   local_coco_classes_txt = "/tmp/coco-labels-paper.txt"
    
   # Downloading coco labels
   os.system("curl -o %s -O %s" % (local_coco_classes_txt, coco_classes_txt))
    
   # Setting default number of predictions
   NUM_PREDICTIONS = 20
    
   # Reading coco labels to a list
   with open(local_coco_classes_txt) as f: 
     classes = ["No Class"] + [line.strip() for line in f.readlines()]
   
   def main(_): 
     channel = grpc.insecure_channel(FLAGS.server)
     stub = prediction_service_pb2_grpc.PredictionServiceStub(channel)
   
     with Image.open(FLAGS.image) as f: 
       f.load()
    
       # Reading the test image given by the user
       data = np.asarray(f)
    
       # Setting batch size to 1
       data = np.expand_dims(data, axis=0)
    
       # Creating a prediction request
       request = predict_pb2.PredictRequest()
    
       # Setting the model spec name
       request.model_spec.name = 'ssdresnet'
    
       # Setting up the inputs and tensors from image data
       request.inputs['inputs'].CopyFrom(
           tf.contrib.util.make_tensor_proto(data, shape=data.shape))
   
       # Iterating over the predictions. The first inference request can take saveral seconds to complete 
       for curpred in range(NUM_PREDICTIONS):
    
         if(curpred == 0):
           print("The first inference request loads the model into the accelerator and can take several seconds to complete. Please standby!")
    
         # Start the timer
         start = time.time()
    
         # This is where the inference actually happens
         result = stub.Predict(request, 60.0)  # 10 secs timeout
    
         print("Inference %d took %f seconds" % (curpred, time.time()-start))
   
       # Extracting results from output 
       outputs = result.outputs
    
       detection_classes = outputs["detection_classes"]
    
       # Creating an ndarray from the output TensorProto
       detection_classes = tf.make_ndarray(detection_classes)
    
       # Getting the number of objects detected in the input image from the output of the predictor
       num_detections = int(tf.make_ndarray(outputs["num_detections"])[0])
    
       print("%d detection[s]" % (num_detections))
    
       # Getting the class ids from the output and mapping the class ids to class names from the coco labels
       class_label = [classes[int(x)]
    
       for x in detection_classes[0][:num_detections]]
           print("SSD Prediction is ", class_label)
    
   if __name__ == '__main__':
    
     tf.app.run()
   ```

1. Now run the script passing the server location, port, and the dog photo's filename as the parameters\.

   ```
   python ssd_resnet_client.py  --server=localhost:9000 --image 3dogs.jpg
   ```

## Amazon EI TensorFlow Predictor<a name="tensorflow-predictor"></a>

The EIPredictor API provides a simple interface to perform repeated inference on a pre\-trained model\. The following code sample shows the available parameters\.

```
ei_predictor = EIPredictor(model_dir,
           signature_def_key=None,
           signature_def=None,
           input_names=None,
           output_names=None,
           tags=None,
           graph=None,
           config=None,
           use_ei=True)

output_dict = ei_predictor(feed_dict)
```

Thus use of EIPredictor is similar to TensorFlow Predictor for a [saved model](https://www.tensorflow.org/api_docs/python/tf/contrib/predictor/from_saved_model) \. EIPredictor can be used in the following ways:

```
//EIPredictor class picks inputs and outputs from default serving signature def with tag “serve”. (similar to TF predictor) 
ei_predictor = EIPredictor(model_dir)

//EI Predictor class picks inputs and outputs from the signature def picked using the signtaure_def_key (similar to TF predictor) 
ei_predictor = EIPredictor(model_dir, signature_def_key='predict')

// Signature_def can be provided directly (similar to TF predictor) 
ei_predictor = EIPredictor(model_dir, signature_def= sig_def)
 
// You provide the input_names and output_names dict.
// similar to TF predictor
 
ei_predictor = EIPredictor(model_dir,
                           input_names,
                           output_names)
 
// tag is used to get the correct signature def. (similar to TF predictor)
 
ei_predictor = EIPredictor(model_dir, tags='serve')
```

Additional EI Predictor functionality includes:
+ Support for frozen models\.

  ```
  // For Frozen graphs, model_dir takes a file name , input_names and output_names
  // input_names and output_names should be provided in this case.
   
  ei_predictor = EIPredictor(model_dir,
                             input_names=None,
                             output_names=None )
  ```
+ Ability to disable use of EI by using the `use_ei` flag, which is defaulted to `True`\. This is useful for testing EIPredictor against TensorFlow Predictor\.
+ Ability to create EIPredictor from a TensorFlow Estimator\. Given a trained Estimator, you can first export a SavedModel\. See the [SavedModel documentation](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/predictor#predictor-from-a-savedmodel) for more details\. The following shows example usage:

  ```
  saved_model_dir = estimator.export_savedmodel(my_export_dir, serving_input_fn)
  ei_predictor = EIPredictor(export_dir=saved_model_dir)
   
  // After the EIPredictor is created, inference is done using the following:
  output_dict = ei_predictor(feed_dict)
  ```

## Amazon EI TensorFlow Predictor Example<a name="tensor-flow-predictor-example"></a>

**Installing Amazon EI TensorFlow**

EI\-enabled TensorFlow comes bundled in the Deep Learning AMIs\. You can also download pip wheels for Python 2 and 3 from the Amazon EI S3 bucket\. Follow these instructions to download and install the pip package:

**To download and install the pip package**

1. Choose the zip file with the pip wheel for the Python version and operating system of your choice from the [S3 bucket](https://s3.console.aws.amazon.com/s3/buckets/amazonei-tensorflow/)\. Copy the path to the zip file and run the following command to download it:

   ```
   curl -O [URL of the zip file of your choice]
   ```

1. Unzip the file:

   ```
   unzip [name of zip file] -d /tmp
   ```

1. Install the pip wheel:

   ```
   pip install [path to pip wheel]
   ```

Try the following example to serve different models, such as ResNet, using a Single Shot Detector \(SSD\)\. As a general rule, you need a servable model and client scripts downloaded to your Deep Learning AMI \(DLAMI\) before proceeding\.

**To serve and test inference with an SSD Model**

1. Download the model\. If you already downloaded the model in the Serving example, skip this step\.

   ```
   curl -O https://s3-us-west-2.amazonaws.com/aws-tf-serving-ei-example/ssd_resnet.zip
   ```

1. Unzip the model\. Again, you may skip this step if you already have the model\.

   ```
   unzip ssd_resnet.zip -d /tmp
   ```

1. Download a picture of three dogs to your current directory\.

   ```
   curl -O https://raw.githubusercontent.com/awslabs/mxnet-model-server/master/docs/images/3dogs.jpg
   ```

1. Open a text editor, such as vim, and paste the following inference script\. Save the file as `ssd_resnet_predictor.py`\.

   ```
   from __future__ import absolute_import
   from __future__ import division
   from __future__ import print_function
   
   import os 
   import sys
   import numpy as np
   import tensorflow as tf
   import matplotlib.image as mpimg
   import time
   
   from tensorflow.contrib.ei.python.predictor.ei_predictor import EIPredictor
    
   tf.app.flags.DEFINE_string('image', '', 'path to image in JPEG format')
    
   FLAGS = tf.app.flags.FLAGS
    
   if(FLAGS.image == ''): 
     print("Supply an Image using '--image [path/to/image]'")
     exit(1)
    
   coco_classes_txt = "https://raw.githubusercontent.com/amikelive/coco-labels/master/coco-labels-paper.txt"
    
   local_coco_classes_txt = "/tmp/coco-labels-paper.txt"
    
   # Downloading coco labels
   os.system("curl -o %s -O %s" % (local_coco_classes_txt, coco_classes_txt))
    
   # Setting default number of predictions
   NUM_PREDICTIONS = 20
    
   # Reading coco labels to a list
   with open(local_coco_classes_txt) as f:
     classes = ["No Class"] + [line.strip() for line in f.readlines()]
    
   def main(_):
    
     # Reading the test image given by the user
     img = mpimg.imread(FLAGS.image)
    
     # Setting batch size to 1
     img = np.expand_dims(img, axis=0)
    
     # Setting up EIPredictor Input
     ssd_resnet_input = {'inputs': img}
    
     print('Running SSD Resnet on EIPredictor using specified input and outputs')
    
     # This is the EIPredictor interface, using specified input and outputs
     eia_predictor = EIPredictor(
    
         # Model directory where the saved model is located
         model_dir='/tmp/ssd_resnet50_v1_coco/1/',
    
         # Specifying the inputs to the Predictor
         input_names={"inputs": "image_tensor:0"},
    
         # Specifying the output names to tensor for Predictor
         output_names={"detection_classes": "detection_classes:0", "num_detections": "num_detections:0",
                       "detection_boxes": "detection_boxes:0"},
     )
    
     pred = None
    
     # Iterating over the predictions. The first inference request can take saveral seconds to complete
     for curpred in range(NUM_PREDICTIONS):
       if(curpred == 0):
         print("The first inference request loads the model into the accelerator and can take several seconds to complete. Please standby!")
    
       # Start the timer
       start = time.time()
    
       # This is where the inference actually happens
       pred = eia_predictor(ssd_resnet_input)
    
       print("Inference %d took %f seconds" % (curpred, time.time()-start))
    
     # Getting the number of objects detected in the input image from the output of the predictor
    
     num_detections = int(pred["num_detections"])
     print("%d detection[s]" % (num_detections))
    
     # Getting the class ids from the output
     detection_classes = pred["detection_classes"][0][:num_detections]
    
     # Mapping the class ids to class names from the coco labels
     print([classes[int(i)] for i in detection_classes]
    
     print('Running SSD Resnet on EIPredictor using default Signature Def')
    
     # This is the EIPredictor interface using the default Signature Def
     eia_predictor = EIPredictor(
    
         # Model directory where the saved model is located
         model_dir='/tmp/ssd_resnet50_v1_coco/1/',
     )
   
    
     # Iterating over the predictions. The first inference request can take saveral seconds to complete
    
     for curpred in range(NUM_PREDICTIONS): 
       if(curpred == 0):
         print("The first inference request loads the model into the accelerator and can take several seconds to complete. Please standby!")
    
       # Start the timer
       start = time.time()
    
       # This is where the inference actually happens
       pred = eia_predictor(ssd_resnet_input)
    
       print("Inference %d took %f seconds" % (curpred, time.time()-start))
    
     # Getting the number of objects detected in the input image from the output of the predictor
     num_detections = int(pred["num_detections"])
     print("%d detection[s]" % (num_detections))
    
     # Getting the class ids from the output
     detection_classes = pred["detection_classes"][0][:num_detections]
    
     # Mapping the class ids to class names from the coco labels
     print([classes[int(i)] for i in detection_classes])
   
    
   if __name__ == "__main__": 
     tf.app.run()
   ```

1. Run the inference script\.

   ```
   python ssd_resnet_predictor.py --image 3dogs.jpg
   ```

For more tutorials and examples, see the [TensorFlow Python API](https://www.tensorflow.org/api_docs/python/)\.

## Additional Requirements and Considerations<a name="tensorflow-additional"></a>

**Model Formats Supported**

Amazon EI supports the TensorFlow saved\_model format via TensorFlow Serving\.

**OpenSSL Requirement**

Amazon EI TensorFlow Serving requires OpenSSL for IAM authentication\. OpenSSL is pre\-installed in the AWS Deep Learning AMI\. If you are building your own AMI or Docker container, you must install OpenSSL \. 
+ Command to install OpenSSL for Ubuntu:

  ```
  sudo apt-get install libssl-dev
  ```
+ Command to install OpenSSL for Amazon Linux:

  ```
  sudo yum install openssl-devel
  ```

**Warmup**

Amazon EI TensorFlow Serving provides a [warmup](https://www.tensorflow.org/serving/saved_model_warmup) feature to preload models and reduce the delay that is typical of the first inference request\. Amazon Elastic Inference TensorFlow Serving only supports warming up the "fault\-finders" signature definition\.

**Signature Definitions**

Using multiple [signature definitions](https://www.tensorflow.org/serving/signature_defs) can have a multiplicative effect on the amount of accelerator memory consumed\. If you plan to exercise more than one signature definition for your inference calls, you should test these scenarios as you determine the accelerator type for your application\.

For large models, EI tends to have larger memory overhead\. This may lead to an out\-of\-memory error\. If you receive this error, try switching to a higher EI Accelerator type\.