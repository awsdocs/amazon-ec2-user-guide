# Using MXNet Models with Amazon EI<a name="ei-mxnet"></a>

The Amazon Elastic Inference enabled version of Apache MXNet lets you use Amazon EI seamlessly, with few changes to your MXNet code\. You can use Amazon EI with the following MXNet API operations:
+ MXNet Python Symbol API
+ MXNet Python Module API

**Topics**
+ [Install Amazon EI Enabled Apache MXNet](#ei-apache)
+ [Activate the MXNet Amazon EI Environment](#ei-activate-python-mxnet)
+ [Use Amazon EI with the MXNet Symbol API](#install-mxnet)
+ [Use Amazon EI with the MXNet Module API](#ei-mxnet-module-api)
+ [Additional Requirements and Considerations](#mxnet-additional)

## Install Amazon EI Enabled Apache MXNet<a name="ei-apache"></a>

Amazon EI enabled Apache MXNet is available in the AWS Deep Learning AMI\. A 'pip' package is also available on [Amazon S3](https://s3.console.aws.amazon.com/s3/buckets/amazonei-apachemxnet/) so you can build it in to your own Amazon Linux or Ubuntu AMIs, or Docker containers\. 

## Activate the MXNet Amazon EI Environment<a name="ei-activate-python-mxnet"></a>

If you are using the AWS Deep Learning AMI, activate the Python 3 MXNet Amazon EI environment or Python 2 MXNet Amazon EI environment, depending on your version of Python\. 

For Python 3:

```
source activate amazonei_mxnet_p36
```

For Python 2:

```
source activate amazonei_mxnet_p27
```

## Use Amazon EI with the MXNet Symbol API<a name="install-mxnet"></a>

Pass `mx.eia()` as the context in a call to either the `simple_bind()` or the `bind()` methods\. For information, see [Symbol API](https://mxnet.incubator.apache.org/api/python/symbol/symbol.html)\.

The following example calls the `simple_bind()` method:

```
import mxnet as mx

data = mx.sym.var('data', shape=(1,))
 
sym = mx.sym.exp(data)

 
# Pass mx.eia() as context during simple bind operation 
executor = sym.simple_bind(ctx=mx.eia(), grad_req='null')
 
for i in range(10):

  # Forward call is performed on remote accelerator 
  executor.forward()
 
  print('Inference %d, output = %s' % (i, executor.outputs[0]))
```

The following example calls the `bind()` method:

```
import mxnet as mx
 
a = mx.sym.Variable('a')
b = mx.sym.Variable('b')
c = 2 * a + b
 
# Even for execution of inference workloads on eia,
 
# context for input ndarrays to be mx.cpu()
 
a_data = mx.nd.array([1,2], ctx=mx.cpu())
b_data = mx.nd.array([2,3], ctx=mx.cpu())
 
# Then in the bind call, use the mx.eia() context
e = c.bind(mx.eia(), {'a': a_data, 'b': b_data})

# Forward call is performed on remote accelerator 
e.forward()
```

The following example calls the `bind()` method on a pre\-trained real model \(Resnet\-50\) from the Symbol API:

```
import mxnet as mx
import numpy as np

path='http://data.mxnet.io/models/imagenet/'
 
[mx.test_utils.download(path+'resnet/50-layers/resnet-50-0000.params'),
 
mx.test_utils.download(path+'resnet/50-layers/resnet-50-symbol.json'),
 
mx.test_utils.download(path+'synset.txt')]
  
ctx = mx.eia()
 
with open('synset.txt', 'r') as f: 
  labels = [l.rstrip() for l in f]

sym, args, aux = mx.model.load_checkpoint('resnet-50', 0)
 
fname = mx.test_utils.download('https://github.com/dmlc/web-data/blob/master/mxnet/doc/tutorials/python/predict_image/cat.jpg?raw=true')
 
img = mx.image.imread(fname)
 
# convert into format (batch, RGB, width, height)
 
img = mx.image.imresize(img, 224, 224) # resize
 
img = img.transpose((2, 0, 1)) # Channel first
 
img = img.expand_dims(axis=0) # batchify
 
img = img.astype(dtype='float32')
 
args['data'] = img
 
 
softmax = mx.nd.random_normal(shape=(1,))
 
args['softmax_label'] = softmax
  
exe = sym.bind(ctx=ctx, args=args, aux_states=aux, grad_req='null')
 
exe.forward()
 
prob = exe.outputs[0].asnumpy()
 
# print the top-5
 
prob = np.squeeze(prob)
 
a = np.argsort(prob)[::-1]
 
for i in a[0:5]:
  print('probability=%f, class=%s' %(prob[i], labels[i]))
```

## Use Amazon EI with the MXNet Module API<a name="ei-mxnet-module-api"></a>

When you create the `Module` object, pass `mx.eia()` as the context\. For more information, see [Module API](https://mxnet.incubator.apache.org/api/python/index.html#module-api)\.

To use the MXNet Module API, you can use the following commands: 

```
# Load saved model
sym, arg_params, aux_params = mx.model.load_checkpoint(model_path, EPOCH_NUM)
 
# Pass mx.eia() as context while creating Module object
mod = mx.mod.Module(symbol=sym, context=mx.eia())
 
# Only for_training = False is supported for eia
 
mod.bind(for_training=False, data_shapes=data_shape)
mod.set_params(arg_params, aux_params)
 
# Forward call is performed on remote accelerator
mod.forward(data_batch)
```

The following example uses Amazon EI with the Module API on a pre\-trained real model \(Resnet\-152\):

```
import mxnet as mx
import numpy as np
 
from collections import namedtuple
 
Batch = namedtuple('Batch', ['data'])
 
path='http://data.mxnet.io/models/imagenet/'
 
[mx.test_utils.download(path+'resnet/152-layers/resnet-152-0000.params'),
 
mx.test_utils.download(path+'resnet/152-layers/resnet-152-symbol.json'),
 
mx.test_utils.download(path+'synset.txt')]
 
ctx = mx.eia()
 
sym, arg_params, aux_params = mx.model.load_checkpoint('resnet-152', 0)
 
mod = mx.mod.Module(symbol=sym, context=ctx, label_names=None)
 
mod.bind(for_training=False, data_shapes=[('data', (1,3,224,224))],
     label_shapes=mod._label_shapes)
 
mod.set_params(arg_params, aux_params, allow_missing=True)
 
with open('synset.txt', 'r') as f:
  labels = [l.rstrip() for l in f]
 
fname = mx.test_utils.download('https://github.com/dmlc/web-data/blob/master/mxnet/doc/tutorials/python/predict_image/cat.jpg?raw=true')
 
img = mx.image.imread(fname)
 
# convert into format (batch, RGB, width, height)
 
img = mx.image.imresize(img, 224, 224) # resize
img = img.transpose((2, 0, 1)) # Channel first
img = img.expand_dims(axis=0) # batchify
 
mod.forward(Batch([img]))
 
prob = mod.get_outputs()[0].asnumpy()
 
# print the top-5
prob = np.squeeze(prob)
 
a = np.argsort(prob)[::-1]
 
for i in a[0:5]:
  print('probability=%f, class=%s' %(prob[i], labels[i]))
```

This release of Amazon EI Apache MXNet has been tested to perform well and provide cost\-saving benefits with the following deep learning use cases and network architectures \(and similar variants\)\.


| Use Case | Example Network Topology | 
| --- | --- | 
|  **Image Recognition**  |  Inception, ResNet, VGG, ResNext  | 
|  **Object Detection**  | SSD                | 
|  **Text to Speech**  |  WaveNet    | 

## Additional Requirements and Considerations<a name="mxnet-additional"></a>
+ Amazon EI Apache MXNet is built with MKLDNN\. Therefore, all operations are supported when using the `mx.cpu()` context\. The `mx.gpu()` context is not supported, therefore no operations can be performed on a local GPU\.
+ Amazon EI is not currently supported for MXNet Imperative mode or the MXNet Gluon API\.
+ `mx.eia()` does not currently provide the full functionality of an MXNet context\. You cannot allocate memory for NDArray on the Amazon EI accelerator by writing something such as: `x = mx.nd.array([[1, 2], [3, 4]], ctx=mx.eia())`\. This results in an error\. Instead you should use: `x = mx.nd.array([[1, 2], [3, 4]], ctx=mx.cpu())`\. MXNet automatically transfers your data to the accelerator as necessary\.
+ Because Amazon EI only supports inference, the `backward()` method or calls to bind\(\) with `for_training=True`\. Because the default value of `for_training` is `True`, make sure that you set `for_training=False`\. 